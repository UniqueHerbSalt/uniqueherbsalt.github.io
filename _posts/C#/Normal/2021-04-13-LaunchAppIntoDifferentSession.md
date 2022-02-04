---
title: "C# LaunchAppIntoDifferentSession"
categories:
  - C#
tags:
  - Win32
  - Marshal
  - WMI
---
C#으로 만든 윈도우 서비스에서 다른 응용프로그램을 실행해야 할때가 있습니다.    
일반적인 방법이라면 `Porcess.Start() `와 같은 메서드를 통해서 실행할 수 있지만,  
문제는 이 응용프로그램이 UI가 반드시 필요한 프로그램이라면 문제가 됩니다.
  
## 1. Windows Session?
NT계열의 윈도우라면 멀티 세션을 가지고 있습니다. 일반적인 가정에서 사용하는 OS의 경우 세션을 여러개  
가질 수는 있으나, 각 세션이 멀티로 동작되는 환경은 제공되지 않습니다.  
(다른 세션으로 이동하고자 한다면 `사용자 전환` 또는 `로그오프 -> 로그인` 을 이용 해야함)  
서버 OS의 경우 서버 옵션에 따라 다르지만 대게 2~4개의 멀티 세션을 동시에 사용할 수 있습니다.  
  
작업 스케줄러를 통해 동작하고 있는 세션 정보를 확인 할 수 있습니다.  
![Image]({{'/assets/images/posts/CS/004.png'| relative_url }})  

보이지 않는다면, 열 선택에서 `세션ID`을 추가하면됩니다.  
  
근데 서비스 얘기를 하다가 세션 얘기를 하고 있냐고 생각할 수 있습니다.  
윈도우의 서비스는 리눅스의 데몬같이 사용자의 로그인 여부와 상관없이 윈도우의 부팅과 함께 서비스가  
시작됩니다.  
이때 사용자가 로그인을 하지 않아도 동작을 해야하기에 윈도우 서비스는 항상 세션 0번으로  
동작하게 됩니다.  
문제는 세션 0번은 UI가 동작할 수 없는 세션이기에 백그라운드로 동작하게 됩니다.  
그래서 위에서 설명했듯이 `Process Start`로 동작을 해봐야 의미가 없게됩니다.

## 2. 다른 세션으로 실행 시키자
만약 사용자가 로그인되어 있다면(또는 로그오프하지 않은 상태) 그 세션으로 실행해주면 되는 것입니다.
아쉽게도 C#에서 간단하게 만들어두지는 않았고, 저번 시간에 이어서 Win32 API를 이용해야합니다.

``` cs
public bool CreateProcessInConsoleSession(string CommandLine, int sessionID, bool bElevate = true)
{
    var pWmi = new ProcessWMI();
    bool findwinlogon = false;
    bool bResult = false;
    uint dwSessionId, winlogonPid = 0;
    IntPtr hUserTokenDup = IntPtr.Zero, hPToken = IntPtr.Zero, hProcess = IntPtr.Zero;

    if (sessionID != -1)
    {
        dwSessionId = (uint)sessionID;
    }
    else
    {
        // Log the client on to the local computer.
        dwSessionId = WTSGetActiveConsoleSessionId();
    }
    var getProcessList = pWmi.GetProcessList();
    findwinlogon = getProcessList.Where(p => p.ProcessNameWithExtension.ToLower().Equals("winlogon.exe")).Count() != 0;
    if (findwinlogon)
    {
        winlogonPid = (uint)(getProcessList.Where(p => p.ProcessNameWithExtension.ToLower().Equals("winlogon.exe")).First().SessionId);
        Debug.WriteLine("winlogonPid = " + winlogonPid.ToString());
        STARTUPINFO si = new STARTUPINFO();
        si.cb = (int)Marshal.SizeOf(si);
        //si.lpDesktop = "winsta0\\default";
        si.lpDesktop = "winsta0\\default";
        TOKEN_PRIVILEGES tp = new TOKEN_PRIVILEGES();
        LUID luid = new LUID();
        hProcess = OpenProcess(MAXIMUM_ALLOWED, false, winlogonPid);

        if (!OpenProcessToken(hProcess, TOKEN_ALL_ACCESS | TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY | TOKEN_DUPLICATE | TOKEN_ASSIGN_PRIMARY
                                        | TOKEN_ADJUST_SESSIONID | TOKEN_READ | TOKEN_WRITE, ref hPToken))
        {
            Debug.WriteLine(String.Format("CreateProcessInConsoleSession OpenProcessToken error: {0}", Marshal.GetLastWin32Error()));
        }

        if (!LookupPrivilegeValue(IntPtr.Zero, SE_TCB_NAME/*SE_DEBUG_NAME*/, ref luid))
        {
            Debug.WriteLine(String.Format("CreateProcessInConsoleSession LookupPrivilegeValue error: {0}", Marshal.GetLastWin32Error()));
        }

        tp.PrivilegeCount = 1;
        tp.Privileges = new int[3];
        tp.Privileges[2] = SE_PRIVILEGE_ENABLED;
        tp.Privileges[1] = luid.HighPart;
        tp.Privileges[0] = luid.LowPart;

        SECURITY_ATTRIBUTES sa = new SECURITY_ATTRIBUTES();
        sa.Length = Marshal.SizeOf(sa);

        if (!DuplicateTokenEx(hPToken, MAXIMUM_ALLOWED, ref sa, (int)SECURITY_IMPERSONATION_LEVEL.SecurityIdentification, (int)TOKEN_TYPE.TokenPrimary, ref hUserTokenDup))
        {
            Debug.WriteLine(String.Format("CreateProcessInConsoleSession DuplicateTokenEx error: {0} Token does not have the privilege.", Marshal.GetLastWin32Error()));
            CloseHandle(hProcess);
            CloseHandle(hPToken);
            return false;
        }

        if (bElevate)
        {
            tp.PrivilegeCount = 1;
            tp.Privileges = new int[3];
            tp.Privileges[2] = SE_PRIVILEGE_ENABLED;
            tp.Privileges[1] = luid.HighPart;
            tp.Privileges[0] = luid.LowPart;

            //Adjust Token privilege
            if (!SetTokenInformation(hUserTokenDup, TOKEN_INFORMATION_CLASS.TokenSessionId, dwSessionId, (uint)IntPtr.Size))
            {
                Debug.WriteLine(String.Format("CreateProcessInConsoleSession SetTokenInformation error: {0} Token does not have the privilege.", Marshal.GetLastWin32Error()));
            }
            if (!AdjustTokenPrivileges(hUserTokenDup, false, ref tp, Marshal.SizeOf(tp), /*(PTOKEN_PRIVILEGES)*/IntPtr.Zero, IntPtr.Zero))
            {
                int nErr = Marshal.GetLastWin32Error();

                if (nErr == ERROR_NOT_ALL_ASSIGNED)
                {
                    Debug.WriteLine(String.Format("CreateProcessInConsoleSession AdjustTokenPrivileges error: {0} Token does not have the privilege.", nErr));
                }
                else
                {
                    Debug.WriteLine(String.Format("CreateProcessInConsoleSession AdjustTokenPrivileges error: {0}", nErr));
                }
            }
        }

        uint dwCreationFlags = CREATE_UNICODE_ENVIRONMENT;//NORMAL_PRIORITY_CLASS | CREATE_NEW_CONSOLE;
        IntPtr pEnv = IntPtr.Zero;
        if (CreateEnvironmentBlock(ref pEnv, hUserTokenDup, true))
        {
            //dwCreationFlags |= CREATE_UNICODE_ENVIRONMENT;
        }
        else
        {
            pEnv = IntPtr.Zero;
        }
        PROCESS_INFORMATION pi;
        // Launch the process in the client's logon session.
        bResult = CreateProcessAsUser(hUserTokenDup,          // client's access token
                                        null,                   // file to execute
                                        CommandLine,            // command line
                                        ref sa,                 // pointer to process SECURITY_ATTRIBUTES
                                        ref sa,                 // pointer to thread SECURITY_ATTRIBUTES
                                        false,                  // handles are not inheritable
                                        (int)dwCreationFlags,   // creation flags
                                        pEnv,                   // pointer to new environment block 
                                        null,                   // name of current directory 
                                        ref si,                 // pointer to STARTUPINFO structure
                                        out pi                  // receives information about new process
                                        );
        // End impersonation of client.

        //GetLastError should be 0
        int iResultOfCreateProcessAsUser = Marshal.GetLastWin32Error();

        //Close handles task
        CloseHandle(hProcess);
        CloseHandle(hUserTokenDup);
        CloseHandle(hPToken);

        return (iResultOfCreateProcessAsUser == 0) ? true : false;
    }
    else
    {
        ProcessStartInfo proc = new ProcessStartInfo
        {
            UseShellExecute = true,
            FileName = CommandLine,
            Verb = "runas",
        };
        Process.Start(proc);
        return true;
    }
}

```
  
여기서 눈여겨 봐야할 것은 `"winlogon.exe"` 을 찾는 부분 입니다. 인자에 지정 세션으로 실행하지 않을 경우  
`WTSGetActiveConsoleSessionId`을 통해서 현재 활성화된 윈도우 세션ID를 취득해옵니다.  
이후 `winlogon.exe`을 찾고 해당 프로세스의 세션 아이디와 비교해서 같은 경우 진행하도록 되어 있습니다.  
  
작업관리자에서 `winlogon.exe`을 찾아보면 `SYSTEM` 계정으로 현재 사용자의 세션 아이디로 동작하고  
있는 것을 확인 할 수 있습니다.  
  
특정 계정의 토큰을 얻어서 해당 계정의 권한을 얻어서 프로세스를 동작하도록 되어있습니다.  
다만, `winlogon.exe` 프로세스 토큰을 복제한 것이기에 동작하기에 실행되는 프로그램의 계정은 `SYSTEM` 계정으로
동작하게 됩니다.
  
`AdjustTokenPrivileges` 부분은 Vista 이후부터 추가된 프로세스 보안 속성 때문에 권한 상승이 필요하여 추가된 부분 입니다.
  
정리를 하자면,  
1. winlogon.exe의 PID를 얻는다.
2. OpenProcessToken에서 핸들값으로 토큰 핸들을 가져온다.
3. DuplicateTokenEx에서 토큰을 복제한다.
4. AdjustTokenPrivileges을 통해 프로세스의 권한을 높힌다.
5. CreateProcessAsUser로 실행한다.

샘플코드는 저번에 이어서 같은 Repo에 추가했습니다.

---

샘플코드  
[Sample Code Github Repository](https://github.com/UniqueHerbSalt/CSExam){:target="_blank"}

---

