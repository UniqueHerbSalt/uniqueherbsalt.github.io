---
title: "C# Get Process List"
categories:
  - C#
tags:
  - Win32
  - Marshal
  - WMI
---
C#에서 현재 윈도우에서 동작중인 프로세스의 리스트를 얻어오는 방법에 대해 알아봅시다.  
  
제가 사용하는 방법은 2가지로 Win32 API를 Marshaling하여 가져오는 방법과 WMI(Windows Management Instrumentation)을 이용한 방법입니다.  
  
## 1. Win32 API
먼저 Win32 API를 이용하는 방법입니다. `CreateToolhelp32Snapshot`,`Process32First`,`Process32Next`를 이용하여 합니다.
#### CreateToolhelp32Snapshot
스냅샷을 찍기위한 함수 입니다.  
[CreateToolhelp32Snapshot MSDN]('https://docs.microsoft.com/ko-kr/windows/win32/api/tlhelp32/nf-tlhelp32-createtoolhelp32snapshot'){:target="_blank"}에 나와 있는 C++ 함수 원형입니다.
``` cpp
HANDLE CreateToolhelp32Snapshot(
  DWORD dwFlags,
  DWORD th32ProcessID
);
```
``` cs
IntPtr hSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
if (hSnap == IntPtr.Zero)
{
    return null;
}
```
`CreateToolhelp32Snapshot` 함수에 `TH32CS_SNAPPROCESS` Flag을 주게 되면 현재 동작하고 있는 모든 프로세스의 스냅샷을 찍게 됩니다.  
이 이후 `Process32First`을 통하여 가장 먼저 프로세스 정보를 취득한 후 `Process32Next`을 While문을 통해 호출하게 되면  
모든 프로세스의 정보를 얻어 올수 있습니다.  
  
아래의 메서드는 프로세스의 파일명, 실행경로를 취득하는 메서드 입니다.

``` cs

public List<ProcessInfo> GetProcessList()
{
    var list = new List<ProcessInfo>();
    // Find the winlogon process
    PROCESSENTRY32 procEntry = new PROCESSENTRY32();
    MODULEENTRY32 lpme = new MODULEENTRY32();
    IntPtr hSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    if (hSnap == IntPtr.Zero)
    {
        return null;
    }

    procEntry.dwSize = (uint)Marshal.SizeOf(procEntry); //sizeof(PROCESSENTRY32);

    if (!Process32First(hSnap, ref procEntry))
    {
        CloseHandle(hSnap);
        return null;
    }

    do
    {
        if (Environment.OSVersion.Version.Major.Equals(5)) //WindowsXP, WindowsServer2000, WindowsServer2003
        {
            var pi = new ProcessInfo();
            IntPtr hsnap_module = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, procEntry.th32ProcessID);
            Module32First(hSnap, ref lpme);
            pi.ProcessNameWithExtension = procEntry.szExeFile;
            pi.ProcessPath = lpme.szExePath;
            CloseHandle(hsnap_module);
            list.Add(pi);
        }
        else
        {
            StringBuilder sb = new StringBuilder(1024);

            IntPtr openprocesshandle = OpenProcess(MAXIMUM_ALLOWED, false, procEntry.th32ProcessID);
            try
            {
                int size = sb.Capacity;

                if (QueryFullProcessImageName(openprocesshandle, 0, sb, out size))
                {
                    var pi = new ProcessInfo();
                    string fullPath = sb.ToString();
                    pi.ProcessNameWithExtension = procEntry.szExeFile;
                    pi.ProcessPath = fullPath;
                    list.Add(pi);
                }
            }
            finally
            {
                CloseHandle(openprocesshandle);
            }
        }
    } while (Process32Next(hSnap, ref procEntry));
    CloseHandle(hSnap);
    return list;
}

```

위의 함수를 실행하기 위해서는 마샬링한 코드가 필요합니다.

``` cs

#region Win32 API Marshal
private static uint STILL_ACTIVE = 0x00000103;
//inner struct used only internally
[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
private struct PROCESSENTRY32
{
    const int MAX_PATH = 260;
    internal UInt32 dwSize;
    internal UInt32 cntUsage;
    internal UInt32 th32ProcessID;
    internal IntPtr th32DefaultHeapID;
    internal UInt32 th32ModuleID;
    internal UInt32 cntThreads;
    internal UInt32 th32ParentProcessID;
    internal Int32 pcPriClassBase;
    internal UInt32 dwFlags;
    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = MAX_PATH)]
    internal string szExeFile;
}
//inner struct used only internally
[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
private struct MODULEENTRY32
{
    const int MAX_PATH = 260;
    internal UInt32 dwSize;
    internal UInt32 th32ModuleID;
    internal UInt32 th32ProcessID;
    internal UInt32 GlblcntUsage;
    internal UInt32 ProccntUsage;
    internal Byte modBaseAddr;
    internal UInt32 modBaseSize;
    internal IntPtr hModule;
    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = MAX_PATH - 4)]
    internal string szModule;
    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = MAX_PATH)]
    internal string szExePath;
}
[DllImport("kernel32.dll")]
static extern bool Module32First(IntPtr hSnapshot, ref MODULEENTRY32 lpme);
[DllImport("kernel32", SetLastError = true, CharSet = System.Runtime.InteropServices.CharSet.Auto)]
static extern IntPtr CreateToolhelp32Snapshot([In] UInt32 dwFlags, [In] UInt32 th32ProcessID);

[DllImport("kernel32", SetLastError = true, CharSet = System.Runtime.InteropServices.CharSet.Auto)]
static extern bool Process32First([In] IntPtr hSnapshot, ref PROCESSENTRY32 lppe);
[DllImport("kernel32", SetLastError = true, CharSet = System.Runtime.InteropServices.CharSet.Auto)]
static extern bool Process32Next([In] IntPtr hSnapshot, ref PROCESSENTRY32 lppe);

[DllImport("kernel32.dll")]
static extern IntPtr OpenProcess(uint dwDesiredAccess, bool bInheritHandle, uint dwProcessId);
[DllImport("kernel32", SetLastError = true)]
[return: MarshalAs(UnmanagedType.Bool)]
static extern bool CloseHandle([In] IntPtr hObject);

[DllImport("kernel32.dll")]
static extern bool QueryFullProcessImageName(IntPtr hprocess, int dwFlags, StringBuilder lpExeName, out int size);
[DllImport("advapi32.dll", ExactSpelling = true, SetLastError = true)]
internal static extern bool OpenProcessToken(IntPtr h, int acc, ref IntPtr phtok);
[DllImport("kernel32.dll", ExactSpelling = true)]
internal static extern IntPtr GetCurrentProcess();

internal static int INVALID_HANDLE_VALUE = -1;
internal const uint TH32CS_SNAPPROCESS = 0x00000002;
internal const uint MAXIMUM_ALLOWED = 0x2000000;

internal const int SE_PRIVILEGE_ENABLED = 0x00000002;
internal const int TOKEN_QUERY = 0x00000008;
internal const int TOKEN_ADJUST_PRIVILEGES = 0x00000020;
internal const string SE_SHUTDOWN_NAME = "SeShutdownPrivilege";
internal const string SE_DEBUG_NAME = "SeDebugPrivilege";
internal const string SE_TCB_NAME = "SeTcbPrivilege";
internal const int EWX_LOGOFF = 0x00000000;
internal const int EWX_SHUTDOWN = 0x00000001;
internal const int EWX_REBOOT = 0x00000002;
internal const int EWX_FORCE = 0x00000004;
internal const int EWX_POWEROFF = 0x00000008;
internal const int EWX_FORCEIFHUNG = 0x00000010;
[DllImport("advapi32.dll", SetLastError = true)]
internal static extern bool LookupPrivilegeValue(string host, string name, ref long pluid);
[DllImport("advapi32.dll", ExactSpelling = true, SetLastError = true)]
internal static extern bool AdjustTokenPrivileges(IntPtr htok, bool disall, ref TokPriv1Luid newst, int len, IntPtr prev, IntPtr relen);

internal struct TokPriv1Luid
{
    public int Count;
    public long Luid;
    public int Attr;
}
#endregion

```

###### 결과
![Image]({{'/assets/images/posts/CS/001.png'| relative_url }}) 


## 2. WMI
WMI를 통해서 프로세스 리스트를 얻어 올 수도 있습니다.  
#### ManagementObjectSearcher
지정한 쿼리에 따라 관리 개체의 컬렉션을 검색해주는 클래스입니다. 사용 방법에 따라 로컬PC 외에도 원격PC의 정보 취득이나  
클래스의 기능에 따라 제어도 가능합니다.

가장 먼저 한번도 WMI를 사용한 적이 없다면, WMI를 사용하기 위하여 참조를 추가해야합니다.  
`System.Management`를 추가해 줍니다.  
  
![Image]({{'/assets/images/posts/CS/002.png'| relative_url }}) 

``` cs

public List<ProcessInfo> GetProcessList()
{
    var list = new List<ProcessInfo>();
    try
    {
        ManagementObjectSearcher searcher = new ManagementObjectSearcher("root\\CIMV2",
            "SELECT Caption, ExecutablePath, ProcessId, SessionId FROM Win32_Process");

        foreach (ManagementObject queryObj in searcher.Get())
        {
            try
            {
                var Caption = queryObj["Caption"].ToString();
                var ExecutablePath = queryObj["ExecutablePath"].ToString();

                if (ExecutablePath.Length != 0)
                {
                    var pi = new ProcessInfo();
                    pi.ProcessNameWithExtension = Caption;
                    pi.ProcessPath = ExecutablePath;
                    list.Add(pi);
                }
            }
            catch { }
        }
    }
    catch(Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    return list;
}

```

위의 Win32 API 보다는 매우 간단하게 코드가 구성이 됩니다.  

Win32 API를 통해서 프로세스 리스트를 취득할 때 64비트 프로그램을 제대로 취득하지 못하는 경우가 있을 수 있습니다.  
MSDN 문서에 따르면 `TH32CS_SNAPPROCESS`가 아닌 `TH32CS_SNAPMODULE32`을 사용하여 취득하라고 되어 있습니다.  
(사용해보지 않음)

Windows Form이나 WPF와 같이 UI가 동작하는 환경에서 WMI로 프로세스 리스트를 취득시 약간의 딜레이 발생으로 UI Thread를 방해하게 됩니다.  
Task와 같이 비동기로 설계해서 사용하시기 바랍니다.

---

샘플코드  
[Sample Code Github Repository](https://github.com/UniqueHerbSalt/CSExam){:target="_blank"}

---

- 샘플 코드는 프로세스 상태에서 테스트한 것 입니다. 서비스 모드에서는 다르게 동작할 수 있습니다.
- 64Bit로 동작시켰습니다.

질문이 있으시면 댓글에 남겨주세요.