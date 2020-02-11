# Win32 笔记

- 控制台程序隐藏命令行窗口：`#pragma comment(linker, "/SUBSYSTEM:WINDOWS /ENTRY:\"mainCRTStartup\"")`。如果编译时提示入口点错误，就把`mainCRTStartup`更改为`wmainCRTStartup`，是`UNICODE`的问题
- 窗口程序显示命令行窗口：`#pragma comment(linker, "/SUBSYSTEM:CONSOLE /ENTRY:\"WinMainCRTStartup\"")`。如果编译时提示入口点错误，就把`WinMainCRTStartup`更改为`wWinMainCRTStartup`，是`UNICODE`的问题
- 如何做到完美的开机自启：注册一个 Windows 服务，使用 Windows 服务来启动你的程序。这样做不仅可以使你的程序比任何程序都早运行，还可以做到以管理员权限运行而不弹出 UAC 提示框。注册服务时，注意选择为可与图形界面交互，否则无法启动 GUI 程序。很多资料都说，Windows Vista 之后的系统不支持服务程序启动 GUI 程序，但经过我的亲自测试，直到 Windows 10 都还支持。
- 通过嵌入清单文件（*.manifest）开启系统的DPI缩放支持并设置相应的缩放策略

  ```xml
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <!-- Some basic information -->
    <assemblyIdentity type="win32" processorArchitecture="*" version="6.0.0.0" name="myapplication.exe"/>
    <description>My test application</description>
    <dependency>
      <dependentAssembly>
        <assemblyIdentity type="win32" name="Microsoft.Windows.Common-Controls" version="6.0.0.0" processorArchitecture="*" publicKeyToken="6595b64144ccf1df" language="*"/>
      </dependentAssembly>
    </dependency>
    <!-- Claims that this application doesn't need UAC -->
    <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
      <security>
        <requestedPrivileges>
          <requestedExecutionLevel level="asInvoker" uiAccess="false"/>
        </requestedPrivileges>
      </security>
    </trustInfo>
    <!-- Claims that this application supports Windows Vista ~ 10 -->
    <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
      <application>
        <!-- The ID below indicates app support for Windows Vista -->
        <supportedOS Id="{e2011457-1546-43c5-a5fe-008deee3d3f0}"/>
        <!-- The ID below indicates app support for Windows 7 -->
        <supportedOS Id="{35138b9a-5d96-4fbd-8e2d-a2440225f93a}"/>
        <!-- The ID below indicates app support for Windows 8 -->
        <supportedOS Id="{4a2f28e3-53b9-4441-ba9c-d69d4a4a6e38}"/>
        <!-- The ID below indicates app support for Windows 8.1 -->
        <supportedOS Id="{1f676c76-80e1-4239-95bb-83d0f6d0da78}"/>
        <!-- The ID below indicates app support for Windows 10 -->
        <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}"/>
      </application>
    </compatibility>
    <!-- Claims that this application supports HiDPI scaling -->
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
        <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true/pm</dpiAware>
        <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2</dpiAwareness>
        <gdiScaling xmlns="http://schemas.microsoft.com/SMI/2017/WindowsSettings">true</gdiScaling>
      </windowsSettings>
    </application>
  </assembly>
  ```

  注：
  - 经过清单文件的设置后，系统不会对程序的界面元素进行强制拉伸，但相应元素的大小调整完全要自行解决。除UWP程序外，系统不会替任何应用程序对DPI改变进行处理。此清单文件的作用是，禁止系统对程序的界面进行自动拉伸（因为这会导致界面很糊）。
  - 参考资料：<https://docs.microsoft.com/en-us/windows/win32/hidpi/high-dpi-desktop-application-development-on-windows>，<https://docs.microsoft.com/en-us/previous-versions/windows/desktop/legacy/mt846517(v=vs.85)>，<https://docs.microsoft.com/en-us/dotnet/framework/winforms/high-dpi-support-in-windows-forms>，<https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/DPIAwarenessPerWindow>，<https://github.com/Microsoft/WPF-Samples/tree/master/PerMonitorDPI>
- 自定义开始屏幕磁贴：<https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn449733(v=win.10)>
- 如何使用API将文件（夹）移动到回收站而不是直接彻底删除：

  ```cpp
  // 头文件：shellapi.h
  // 库文件：Shell32.lib（Shell32.dll）
  // 示例代码，请注意修改
  BOOL MoveToRecycleBin(LPCWSTR pszPath, BOOL bDelete)
  {
    SHFILEOPSTRUCTW sfos;
    SecureZeroMemory(sfos, sizeof(sfos));
    sfos.fFlags |= FOF_NO_UI; // 不显示任何窗口
    sfos.wFunc = FO_DELETE; // 关键，不可缺少：指定此操作为删除操作
    sfos.pFrom = pszPath; // 关键，不可缺少：待删除的文件（夹）
    sfos.pTo = nullptr; // 关键，不可缺少：必须是空指针
    if (bDelete)
    {
      sfos.fFlags &= ~FOF_ALLOWUNDO; // 直接删除，不放入回收站
    }
    else
    {
      sfos.fFlags |= FOF_ALLOWUNDO; // 将文件移动到回收站中
    }
    return SHFileOperationW(&sfos);
  }
  ```

  注：自 Windows Vista 开始，微软推荐使用新版 API [IFileOperation](https://docs.microsoft.com/en-us/windows/win32/api/shobjidl_core/nn-shobjidl_core-ifileoperation) 来代替过时的 API `SHFileOperation`。

- 判断当前程序是否正在以管理员权限运行

  ```cpp
  // 头文件：securitybaseapi.h (include Windows.h)
  // 库文件：Advapi32.lib（Advapi32.dll）
  /*
  Routine Description: This routine returns TRUE if the caller's
  process is a member of the Administrators local group. Caller is NOT
  expected to be impersonating anyone and is expected to be able to
  open its own process and process token.
  Arguments: None.
  Return Value:
     TRUE - Caller has Administrators local group.
     FALSE - Caller does not have Administrators local group. --
  */
  BOOL IsUserAdmin() {
      SID_IDENTIFIER_AUTHORITY NtAuthority = SECURITY_NT_AUTHORITY;
      PSID AdministratorsGroup;
      BOOL b = AllocateAndInitializeSid(&NtAuthority, 2, SECURITY_BUILTIN_DOMAIN_RID, DOMAIN_ALIAS_RID_ADMINS, 0, 0, 0, 0, 0, 0, &AdministratorsGroup);
      if (b) {
          if (!CheckTokenMembership(nullptr, AdministratorsGroup, &b)) {
              b = FALSE;
          }
          FreeSid(AdministratorsGroup);
      }
      return b;
  }
  ```

  摘自：<https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-checktokenmembership>
- 以管理员权限启动程序：

  ```cpp
  // 头文件：shellapi.h
  // 库文件：Shell32.lib（Shell32.dll）

  struct DeCoInitializer {
      DeCoInitializer() : neededCoInit(CoInitialize(nullptr) == S_OK) {}
      ~DeCoInitializer() {
          if (neededCoInit) {
              CoUninitialize();
          }
      }
      bool neededCoInit;
  };

  DeCoInitializer _deCoInitializer; // 虽然没有用到这个变量，但它的析构函数有用

  // AdminAuthorization::execute uses UAC to ask for admin privileges. If the
  // user is no administrator yet and the computer's policies are set to not
  // use UAC (which is the case in some corporate networks), the call to
  // execute() will simply succeed and not at all launch the child process. To
  // avoid this, we detect this situation here and return early.
  if (!hasAdminRights()) { // 使用上面提到的方法检测是否已经拥有管理员权限
      QLatin1String key("HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System");
      QSettings registry(key, QSettings::NativeFormat);
      const QVariant enableLUA = registry.value(QLatin1String("EnableLUA"));
      if ((enableLUA.type() == QVariant::Int) && (enableLUA.toInt() == 0)) {
          return false;
      }
  }

  SHELLEXECUTEINFOW sei;
  SecureZeroMemory(sei, sizeof(sei));
  sei.lpVerb = L"RunAs"; // 这一行是关键，有了这一行才能以管理员权限执行
  sei.lpFile = L"notepad.exe"; // 待启动程序的路径
  sei.nShow = SW_HIDE; // 想隐藏程序窗口的话要加上这一句，否则不需要这一行
  sei.lpParameters = L"/ABC /DEF"; // 要传给程序的参数，没有的话也不需要这一行
  sei.cbSize = sizeof(SHELLEXECUTEINFOW); // 必需的参数，不要忘掉
  sei.fMask = SEE_MASK_NOASYNC; // 必需的参数，不要忘掉
  if(ShellExecuteExW(&sei) != TRUE)
  {
      DWORD dwStatus = GetLastError();
      if(dwStatus == ERROR_CANCELLED)
      {
          std::cerr << "[UAC] : User denied to give admin privilege." << std::endl;
      }
      else if(dwStatus == ERROR_FILE_NOT_FOUND)
      {
          std::cerr << "[UAC] : File not found." << std::endl;
      }
      else
      {
          std::cerr << "[UAC] : balabalabala." << std::endl;
      }
  }
  ```

- Windows服务程序框架

  ```cpp
  #include <windows.h>
  #include <tchar.h>

  // 服务名
  #define SERVICE_NAME L"mysvc"
  // 服务显示的名字
  #define SERVICE_DISPLAY_NAME L"My Service"
  // 服务描述
  #define SERVICE_DESCRIPTION L"This service will do something."

  SERVICE_STATUS serviceStatus;
  SERVICE_STATUS_HANDLE serviceStatusHandle = nullptr;

  // 服务安装函数
  VOID Install()
  {
      wchar_t filePath[MAX_PATH + 1];
      SecureZeroMemory(filePath, sizeof(filePath));
      DWORD dwSize = GetModuleFileNameW(nullptr, filePath, MAX_PATH);
      filePath[dwSize] = 0;
      bool result = false;
      SC_HANDLE hSCM = OpenSCManagerW(nullptr, nullptr, SC_MANAGER_ALL_ACCESS);
      if (hSCM != nullptr)
      {
          SC_HANDLE hService = CreateServiceW(hSCM, SERVICE_NAME, SERVICE_DISPLAY_NAME, SERVICE_ALL_ACCESS, SERVICE_WIN32_OWN_PROCESS | SERVICE_INTERACTIVE_PROCESS, SERVICE_AUTO_START, SERVICE_ERROR_NORMAL, filePath, nullptr, nullptr, nullptr, nullptr, nullptr);
          if (hService != nullptr)
          {
              SERVICE_DESCRIPTIONW sdesc;
              SecureZeroMemory(sdesc, sizeof(sdesc));
              sdesc.lpDescription = const_cast<LPWSTR>(SERVICE_DESCRIPTION);
              result = ChangeServiceConfig2W(hService, SERVICE_CONFIG_DESCRIPTION, &sdesc);
              CloseServiceHandleW(hService);
          }
          CloseServiceHandleW(hSCM);
      }
      if (result)
          OutputDebugStringW(L"Installation succeeded.");
      else
          OutputDebugStringW(L"Installation failed. Administrator privilege is needed.");
  }

  // 服务卸载函数
  VOID Uninstall()
  {
      bool result = false;
      SC_HANDLE hSCM = OpenSCManagerW(nullptr, nullptr, SC_MANAGER_ALL_ACCESS);
      if (hSCM != nullptr)
      {
          SC_HANDLE hService = OpenServiceW(hSCM, SERVICE_NAME, DELETE);
          if (hService != nullptr)
          {
              result = DeleteServiceW(hService);
              CloseServiceHandleW(hService);
          }
          CloseServiceHandleW(hSCM);
      }
      if (result)
          OutputDebugStringW(L"Uninstallation succeeded.");
      else
          OutputDebugStringW(L"Uninstallation failed. Administrator privilege is needed.");
  }

  // 服务控制函数
  VOID WINAPI ServiceCtrlHandler(DWORD code)
  {
      switch (code)
      {
      case SERVICE_CONTROL_STOP:
      case SERVICE_CONTROL_SHUTDOWN:
          serviceStatus.dwCurrentState = SERVICE_STOPPED;
          serviceStatus.dwControlsAccepted = 0;
          serviceStatus.dwWin32ExitCode = 0;
          serviceStatus.dwCheckPoint = 3;
          break;
      case SERVICE_CONTROL_PAUSE:
          serviceStatus.dwCurrentState = SERVICE_PAUSED;
          break;
      case SERVICE_CONTROL_CONTINUE:
          serviceStatus.dwCurrentState = SERVICE_RUNNING;
          break;
      case SERVICE_CONTROL_INTERROGATE:
          break;
      default:
          break;
      };
      SetServiceStatusW(serviceStatusHandle, &serviceStatus);
  }

  // 服务工作线程
  DWORD WINAPI ServiceWorkerThread(LPVOID lpParam)
  {
      (void)lpParam;
      // 服务程序要执行的具体内容
      return ERROR_SUCCESS;
  }

  // 服务主函数
  VOID WINAPI ServiceMain(DWORD argc, LPTSTR *argv)
  {
      (void)argc;
      (void)argv;
      serviceStatusHandle = RegisterServiceCtrlHandlerW(SERVICE_NAME, ServiceCtrlHandler);
      if (serviceStatusHandle == nullptr)
          return;
      SecureZeroMemory(serviceStatus, sizeof(serviceStatus));
      serviceStatus.dwServiceType = SERVICE_WIN32_OWN_PROCESS | SERVICE_INTERACTIVE_PROCESS;
      serviceStatus.dwControlsAccepted = 0;
      serviceStatus.dwCurrentState = SERVICE_START_PENDING;
      serviceStatus.dwWin32ExitCode = 0;
      serviceStatus.dwServiceSpecificExitCode = 0;
      serviceStatus.dwCheckPoint = 0;
      if (SetServiceStatusW(serviceStatusHandle, &serviceStatus) != TRUE)
          return;
      serviceStatus.dwCurrentState = SERVICE_RUNNING;
      serviceStatus.dwControlsAccepted = SERVICE_ACCEPT_STOP | SERVICE_ACCEPT_SHUTDOWN | SERVICE_ACCEPT_PAUSE_CONTINUE;
      serviceStatus.dwWin32ExitCode = 0;
      serviceStatus.dwCheckPoint = 0;
      if (SetServiceStatusW(serviceStatusHandle, &serviceStatus) != TRUE)
          return;
      HANDLE hThread = CreateThreadW(nullptr, 0, ServiceWorkerThread, nullptr, 0, nullptr);
      WaitForSingleObjectW(hThread, INFINITE);
      serviceStatus.dwCurrentState = SERVICE_STOPPED;
      serviceStatus.dwControlsAccepted = 0;
      serviceStatus.dwWin32ExitCode = 0;
      serviceStatus.dwCheckPoint = 3;
      SetServiceStatusW(serviceStatusHandle, &serviceStatus);
  }

  // 程序main函数
  int wmain(int argc, wchar_t *argv[])
  {
      SERVICE_TABLE_ENTRY ServiceTable[] =
      {
          {const_cast<LPWSTR>(SERVICE_NAME), (LPSERVICE_MAIN_FUNCTION)ServiceMain},
          {nullptr, nullptr}
      };
      for (unsigned int i = 1; i != argc; ++i)
          if ((wcscmp(argv[i], L"-i") == 0) || (wcscmp(argv[i], L"-I") == 0) || (wcscmp(argv[i], L"-install") == 0) || (wcscmp(argv[i], L"-INSTALL") == 0) || (wcscmp(argv[i], L"-Install") == 0))
          {
              Install();
              break;
          }
          else if ((wcscmp(argv[i], L"-u") == 0) || (wcscmp(argv[i], L"-U") == 0) || (wcscmp(argv[i], L"-uninstall") == 0) || (wcscmp(argv[i], L"-UNINSTALL") == 0) || (wcscmp(argv[i], L"-Uninstall") == 0))
          {
              Uninstall();
              break;
          }
      if (StartServiceCtrlDispatcherW(ServiceTable) != TRUE)
          return GetLastError();
      return 0;
  }
  ```

- 获取系统是否已经开启了深色模式/浅色模式 & 将窗口设置为深色模式/浅色模式：<https://github.com/microsoft/terminal/blob/master/src/interactivity/win32/windowtheme.cpp>
- 读写INI文件：<https://github.com/Chuyu-Team/CPPHelper/blob/master/IniHelper.h>
  - 读取

    ```cpp
    // 获取整型数值（区段名，键名，默认值，INI文件路径）
    UINT GetPrivateProfileInt(LPCTSTR lpAppName, LPCTSTR lpKeyName, INT nDefault, LPCTSTR lpFileName);
    // 获取指定区段的所有子键和它们的值（区段名，字符串返回值的指针，字符串长度，INI文件路径）
    DWORD GetPrivateProfileSection(LPCTSTR lpAppName, LPTSTR lpReturnedString, DWORD nSize, LPCTSTR lpFileName);
    // 获取所有区段的名字（字符串返回值的指针，字符串长度，INI文件路径）
    DWORD GetPrivateProfileSectionNames(LPTSTR lpszReturnBuffer, DWORD nSize, LPCTSTR lpFileName);
    // 获取字符串（区段名，键名，默认值，字符串返回值的指针，字符串长度，INI文件路径）
    DWORD GetPrivateProfileString(LPCTSTR lpAppName, LPCTSTR lpKeyName, LPCTSTR lpDefault, LPTSTR lpReturnedString, DWORD nSize, LPCTSTR lpFileName);
    // 不太明白是干嘛的
    BOOL GetPrivateProfileStruct(LPCTSTR lpszSection, LPCTSTR lpszKey, LPVOID lpStruct, UINT uSizeStruct, LPCTSTR szFile);
    ```

  - 写入

    ```cpp
    // 替换指定区段的子键和它们的值（区段名，字符串，INI文件路径）
    BOOL WritePrivateProfileSection(LPCSTR lpAppName, LPCSTR lpString, LPCSTR lpFileName);
    // 复制一个字符串到指定的区段中（区段名，键名，字符串，INI文件路径）
    BOOL WritePrivateProfileString(LPCSTR lpAppName, LPCSTR lpKeyName, LPCSTR lpString, LPCSTR lpFileName);
    // 不太明白是干嘛的
    BOOL WritePrivateProfileStruct(LPCSTR lpszSection, LPCSTR lpszKey, LPVOID lpStruct, UINT uSizeStruct, LPCSTR szFile);
    ```

- 读写注册表：<https://github.com/Chuyu-Team/CPPHelper/blob/master/RegHelper.h>
- 置顶/取消置顶窗口

  ```cpp
  // 头文件：winuser.h (include Windows.h)
  // 库文件：User32.lib（User32.dll）
  // 置顶
  SetWindowPos(hWnd, HWND_TOPMOST, 0, 0, 0, 0, SWP_NOSIZE | SWP_NOMOVE);
  // 取消置顶
  SetWindowPos(hWnd, HWND_NOTOPMOST, 0, 0, 0, 0, SWP_NOSIZE | SWP_NOMOVE);
  // 注：hWnd 为窗口的句柄
  ```

- 将最小化或被其他窗口挡住的窗口移到最前

  ```cpp
  // 头文件：winuser.h (include Windows.h)
  // 库文件：User32.lib（User32.dll）
  // 下面这一段代码要在较早的地方执行，例如在程序的 main 函数里。
  // 这一段代码是为了确保 SetForegroundWindow 能执行成功而设置的，在前置窗口前执行过一次就足够了（如果需要的话），不是每次前置窗口都要执行的。
  DWORD dwTimeout = -1;
  SystemParametersInfo(SPI_GETFOREGROUNDLOCKTIMEOUT, 0, &dwTimeout, 0);
  if (dwTimeout >= 100) {
    // 下面这一行语句因为要读写INI文件，因此可能会导致执行此语句时卡顿两三秒
    SystemParametersInfo(SPI_SETFOREGROUNDLOCKTIMEOUT, 0, 0, SPIF_SENDCHANGE | SPIF_UPDATEINIFILE);
  }
  // 上面的代码结束，下面的代码与上面的没有关系了
  // 下面这一段代码每次前置窗口时都要执行一次，与上面那一段不一样。
  HWND hWnd = ... // 获取程序当前活动窗口的句柄
  HWND hForeWnd = GetForegroundWindow(); // 获取当前前置窗口的句柄
  DWORD dwForeID = GetWindowThreadProcessId(hForeWnd, nullptr); // 获取当前前置窗口的进程ID
  DWORD dwCurID = GetCurrentThreadId(); // 获取程序自身的进程ID
  // 如果窗口被隐藏了，先显示出来
  if (IsWindowVisible(hWnd) != TRUE) {
    // 此处一定要用 SW_SHOW，用其他的值会意外改变窗口的状态
    ShowWindow(hWnd, SW_SHOW);
  }
  WINDOWPLACEMENT wndpmt;
  SecureZeroMemory(wndpmt, sizeof(wndpmt));
  if (GetWindowPlacement(hWnd, &wndpmt) != FALSE) {
    // 如果窗口被最小化了，还原回去
    if ((wndpmt.showCmd == SW_SHOWMINIMIZED) || (wndpmt.showCmd == SW_MINIMIZE) || (wndpmt.showCmd == SW_FORCEMINIMIZE)) {
      // 此处一定要用 SW_RESTORE，用 SW_SHOW 不能恢复被最小化的窗口，用 SW_SHOWNORMAL 会导致窗口被无条件的还原为原始大小，即使在最小化之前处于最大化的状态
      ShowWindow(hWnd, SW_RESTORE);
    }
  }
  AttachThreadInput(dwCurID, dwForeID, TRUE); // 连接两个进程的输入焦点
  SetForegroundWindow(hWnd); // 设置为前置的窗口
  AttachThreadInput(dwCurID, dwForeID, FALSE); // 断开两个进程的输入焦点
  ```

- 关联文件后缀名：请参考<https://github.com/microsoft/Windows-classic-samples/blob/master/Samples/Win7Samples/winui/shell/appshellintegration/AutomaticJumpList/FileRegistrations.h>
- 将窗口嵌入桌面（类似于动态壁纸那种效果）

  ```cpp
  // 头文件：windows.h
  // 库文件：User32.lib（User32.dll）

  static bool legacyMode = false;
  static HWND HWORKERW = nullptr;

  BOOL CALLBACK EnumWindowsProc(HWND hwnd, LPARAM lParam) {
      (void)lParam;
      const HWND defview = FindWindowExW(hwnd, nullptr, L"SHELLDLL_DefView", nullptr);
      if (defview != nullptr) {
          HWORKERW = FindWindowExW(nullptr, hwnd, L"WorkerW", nullptr);
          if (HWORKERW != nullptr) {
              return FALSE;
          }
      }
      return TRUE;
  }

  HWND getProgman() {
      return FindWindowW(L"Progman", L"Program Manager");
  }

  HWND getDesktop() {
      const HWND hwnd = getProgman();
      if (hwnd == nullptr) {
          return nullptr;
      }
      SendMessage(hwnd, 0x052c, 0, 0);
      EnumWindows(EnumWindowsProc, 0);
      ShowWindow(HWORKERW, legacyMode ? SW_HIDE : SW_SHOW);
      return legacyMode ? hwnd : HWORKERW;
  }

  HWND getWallpaper() {
      return legacyMode ? getProgman() : HWORKERW;
  }

  bool setWallpaper(HWND window) {
      if (window == nullptr) {
          return false;
      }
      const HWND desktop = getDesktop();
      const HWND parent = desktop != nullptr ? SetParent(window, desktop) : nullptr;
      return parent != nullptr;
  }

  bool isWallpaperVisible() {
      return getWallpaper() == nullptr ? false : IsWindowVisible(getWallpaper());
  }

  bool isWallpaperHidden() {
      return !isWallpaperVisible();
  }

  bool setWallpaperVisible(bool visible) {
      if (getWallpaper() == nullptr) {
          return false;
      }
      if (visible != isWallpaperVisible()) {
          return ShowWindow(getWallpaper(), visible ? SW_SHOW : SW_HIDE);
      }
      return false;
  }

  void hideWallpaper() {
      if (getWallpaper() != nullptr) {
          setWallpaperVisible(false);
      }
  }

  void showWallpaper() {
      if (getWallpaper() != nullptr) {
          setWallpaperVisible(true);
      }
  }

  void setLegacyMode(bool legacy) {
      if (legacyMode != legacy) {
          legacyMode = legacy;
      }
  }
  ```

  用法：使用`setWallpaper`将你的窗口嵌入到桌面。其他函数的效果请自行体验。

  注意：窗口一旦被嵌入桌面，它就再也不能获取到焦点了。

- 将图标固定到任务栏

  ```cpp
  // 头文件：shellapi.h
  // 库文件：Shell32.lib（Shell32.dll）
  ShellExecuteW(nullptr, L"TaskbarPin", L"快捷方式（.lnk文件）的绝对路径", nullptr, nullptr, SW_SHOW);
  // 快捷方式指向的文件一定要存在，即不能把一个失效的快捷方式固定到任务栏。当然了，快捷方式本身也一定要存在，不能把一个不存在的文件固定到任务栏。
  // 将“TaskbarPin”替换为“TaskbarUnpin”即可将已经固定在任务栏上的图标移除。
  ```

  注：此API支持Win7~Win10，Windows Vista及XP请直接在`C:\Documents and Settings\用户名\Application Data\Microsoft\Internet Explorer\Quick Launch`文件夹下创建快捷方式。

- 资源脚本（.rc文件）写法

  ```text
  // 此头文件不可缺少，否则编译报错。
  // 如果把下面所有的常量替换为其对应的数值，就可以不包含任何头文件。
  #include <windows.h>

  // 第一个图标资源即为程序的图标
  IDI_MYAPPICON ICON "app_icon.ico"
  // 可以有不止一个图标资源，供程序内部使用
  IDI_MYICON2 ICON "my_icon2.ico"

  VS_VERSION_INFO VERSIONINFO
  // 文件版本，修改时别忘了一起改这里，不要加双引号，而且是逗号不是点
  FILEVERSION     1,0,0,1
  // 产品版本，修改时别忘了一起改这里，不要加双引号，而且是逗号不是点
  PRODUCTVERSION  1,0,0,1
  FILEFLAGSMASK   0x3fL
  #ifdef _DEBUG
      FILEFLAGS   VS_FF_DEBUG
  #else
      FILEFLAGS   0x0L
  #endif
  FILEOS          VOS_NT_WINDOWS32
  // 应用程序用VFT_APP，动态链接库用VFT_DLL
  FILETYPE        VFT_DLL
  FILESUBTYPE     VFT2_UNKNOWN
  BEGIN
      BLOCK "StringFileInfo"
      BEGIN
          // 英语（美国）
          BLOCK "040904b0"
          BEGIN
              VALUE "Comments",         "Built by Qt 5.14.1"
              VALUE "CompanyName",      "wangwenx190"
              VALUE "FileDescription",  "My App"
              VALUE "FileVersion",      "1.0.0.1"
              VALUE "InternalName",     "my-app"
              VALUE "LegalCopyright",   "GPLv3"
              VALUE "LegalTrademarks1", "Trademark-1"
              VALUE "LegalTrademarks2", "Trademark-2"
              VALUE "OriginalFilename", "libEg.dll"
              VALUE "ProductName",      "Test App"
              VALUE "ProductVersion",   "1.0.0.1"
          END
          // 简体中文
          BLOCK "080404b0"
          BEGIN
              VALUE "Comments",         "使用 Qt 5.14.1 构建"
              VALUE "CompanyName",      "我的公司"
              VALUE "FileDescription",  "我的应用程序"
              VALUE "FileVersion",      "1.0.0.1"
              VALUE "InternalName",     "我的程序"
              VALUE "LegalCopyright",   "许可协议"
              VALUE "LegalTrademarks1", "商标1"
              VALUE "LegalTrademarks2", "商标2"
              VALUE "OriginalFilename", "libEg.dll"
              VALUE "ProductName",      "测试应用"
              VALUE "ProductVersion",   "1.0.0.1"
          END
      END
      BLOCK "VarFileInfo"
      BEGIN
          // 下面这一行包含 WORD,WORD 对，前者为语言，后者为代码页
          // 例如：美式英语（0x409），Unicode（1200），简体中文（0x804），Unicode（1200）
          VALUE "Translation", 0x409, 1200, 0x804, 1200
      END
  END
  ```

  摘自：<https://docs.microsoft.com/en-us/windows/win32/menurc/versioninfo-resource>

  注：如果.rc文件中包含非拉丁字符，请将文本编码设置为`UTF-16 LE`或对应的本地文本编码（例如：`GB18030`），但不要改为`UTF-8`（不论带不带`BOM`），否则编译时会报错。
- 普通程序开机自启

  将应用程序的绝对路径，写注册表到`HKEY_LOCAL_MACHINE（或HKEY_CURRENT_USER）\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`下。如果路径包含空格，最好使用英文半角双引号包裹起来。
- 使用Win32 API修改PE文件（exe、dll）的图标以及版本信息

  ```cpp
  // 头文件：windows.h
  // 库文件：Kernel32.lib（Kernel32.dll）、Version.lib

  // #pragmas are used here to insure that the structure's
  // packing in memory matches the packing of the EXE or DLL.
  #pragma pack(push)
  #pragma pack(2)

  typedef struct
  {
      BYTE        bWidth;          // Width, in pixels, of the image
      BYTE        bHeight;         // Height, in pixels, of the image
      BYTE        bColorCount;     // Number of colors in image (0 if >=8bpp)
      BYTE        bReserved;       // Reserved ( must be 0)
      WORD        wPlanes;         // Color Planes
      WORD        wBitCount;       // Bits per pixel
      DWORD       dwBytesInRes;    // How many bytes in this resource?
      DWORD       dwImageOffset;   // Where in the file is this image?
  } ICONDIRENTRY, *LPICONDIRENTRY;

  typedef struct
  {
      WORD           idReserved;   // Reserved (must be 0)
      WORD           idType;       // Resource Type (1 for icons)
      WORD           idCount;      // How many images?
      ICONDIRENTRY   idEntries[1]; // An entry for each image (idCount of 'em)
  } ICONDIR, *LPICONDIR;

  typedef struct
  {
     BYTE   bWidth;               // Width, in pixels, of the image
     BYTE   bHeight;              // Height, in pixels, of the image
     BYTE   bColorCount;          // Number of colors in image (0 if >=8bpp)
     BYTE   bReserved;            // Reserved
     WORD   wPlanes;              // Color Planes
     WORD   wBitCount;            // Bits per pixel
     DWORD  dwBytesInRes;         // how many bytes in this resource?
     WORD   nID;                  // the ID
  } GRPICONDIRENTRY, *LPGRPICONDIRENTRY;

  typedef struct
  {
     WORD              idReserved;   // Reserved (must be 0)
     WORD              idType;       // Resource type (1 for icons)
     WORD              idCount;      // How many images?
     GRPICONDIRENTRY   idEntries[1]; // The entries for each image
  } GRPICONDIR, *LPGRPICONDIR;

  #pragma pack(pop)

  bool changeApplicationIcon(const QString &targetPath, const QString &iconPath) {
      Q_ASSERT(!targetPath.isEmpty() && !iconPath.isEmpty());

      if (!QFile::exists(targetPath) || !QFile::exists(iconPath)) {
          qWarning() << "Target PE file or icon file not exist";
          return false;
      }

      const QFile iconFile(iconPath);
      if (!iconFile.open(QIODevice::ReadOnly)) {
          qWarning() << "Cannot open" << iconPath << ':' << iconFile.errorString();
          return false;
      }

      const QByteArray fileFormat = QImageReader::imageFormat(iconPath);
      if (fileFormat != "ico") {
          qWarning() << "Cannot use" << iconPath << "as an application icon: unsupported format" << fileFormat.constData();
          return false;
      }

      const HANDLE updateRes = BeginUpdateResourceW(reinterpret_cast<LPCWSTR>(QDir::toNativeSeparators(targetPath).utf16()), FALSE);
      if (!updateRes) {
          qWarning() << "Cannot begin updating resource";
          return false;
      }

      const QByteArray temp = iconFile.readAll();
      const auto *ig = reinterpret_cast<LPICONDIR>(temp.data());

      const DWORD newSize = sizeof(GRPICONDIR) + sizeof(GRPICONDIRENTRY) * (ig->idCount - 1);
      auto *newDir = reinterpret_cast<LPGRPICONDIR>(new char[newSize]);
      newDir->idReserved = ig->idReserved;
      newDir->idType = ig->idType;
      newDir->idCount = ig->idCount;

      for (int i = 0; i < ig->idCount; ++i) {
          const char *temp1 = temp.data() + ig->idEntries[i].dwImageOffset;
          const DWORD size1 = ig->idEntries[i].dwBytesInRes;

          newDir->idEntries[i].bWidth = ig->idEntries[i].bWidth;
          newDir->idEntries[i].bHeight = ig->idEntries[i].bHeight;
          newDir->idEntries[i].bColorCount = ig->idEntries[i].bColorCount;
          newDir->idEntries[i].bReserved = ig->idEntries[i].bReserved;
          newDir->idEntries[i].wPlanes = ig->idEntries[i].wPlanes;
          newDir->idEntries[i].wBitCount = ig->idEntries[i].wBitCount;
          newDir->idEntries[i].dwBytesInRes = ig->idEntries[i].dwBytesInRes;
          newDir->idEntries[i].nID = i + 1;

          if (!UpdateResourceW(updateRes, RT_ICON, MAKEINTRESOURCE(i + 1), MAKELANGID(LANG_NEUTRAL, SUBLANG_NEUTRAL), temp1, size1)) {
              qWarning() << "Cannot update icon" << i + 1;
          }
      }

      if (!UpdateResourceW(updateRes, RT_GROUP_ICON, MAKEINTRESOURCE(1), MAKELANGID(LANG_NEUTRAL, SUBLANG_NEUTRAL), newDir, newSize)) {
          qWarning() << "Cannot update group icon";
      }

      delete[] newDir;

      if (!EndUpdateResourceW(updateRes, FALSE)) {
          qWarning() << "Cannot end updating resource";
          return false;
      }

      return true;
  }

  struct LANGANDCODEPAGE {
      WORD   wLanguage;
      WORD   wCodePage;
  } *lpTranslate;

  bool changeVersionInfo(const QString &targetPath, const QString &valueName, const QString &newValue) {
      Q_ASSERT(!targetPath.isEmpty() && !valueName.isEmpty() && !newValue.isEmpty());

      if (!QFile::exists(targetPath)) {
          qWarning() << "Target PE file not exist";
          return false;
      }

      const auto *lpszFile = reinterpret_cast<LPCWSTR>(QDir::toNativeSeparators(targetPath).utf16());

      DWORD dwHandle = 0;
      const DWORD dwSize = GetFileVersionInfoSizeW(lpszFile, &dwHandle);
      if (dwSize <= 0) {
          qWarning() << "Cannot acquire file version info size";
          return false;
      }

      LPBYTE lpBuffer = new BYTE[dwSize];
      SecureZeroMemory(lpBuffer, sizeof(lpBuffer));

      if (!GetFileVersionInfoW(lpszFile, dwHandle, dwSize, lpBuffer)) {
          delete[] lpBuffer;
          qWarning() << "Cannot acquire file version info";
          return false;
      }

      const HANDLE hResource = BeginUpdateResourceW(lpszFile, FALSE);
      if (!hResource) {
          delete[] lpBuffer;
          qWarning() << "Cannot begin updating resource";
          return false;
      }

      UINT cbTranslate = 0;

      if (!VerQueryValueW(lpBuffer, L"\\VarFileInfo\\Translation", reinterpret_cast<LPVOID *>(&lpTranslate), &cbTranslate)) {
          delete[] lpBuffer;
          qWarning() << "Cannot query translation values";
          return false;
      }

      for (int i = 0; i < (cbTranslate / sizeof(struct LANGANDCODEPAGE)); ++i) {
          const QString valuePath = QString("\\StringFileInfo\\%1%2\\%3").arg(lpTranslate[i].wLanguage, lpTranslate[i].wCodePage, valueName);
          const auto *szTemp = reinterpret_cast<LPCWSTR>(valuePath.utf16());
          LPVOID lpStringBuf = nullptr;
          DWORD dwStringLen = 0;
          if (!VerQueryValueW(lpBuffer, szTemp, &lpStringBuf, static_cast<PUINT>(&dwStringLen))) {
              qWarning() << "Cannot query" << valueName;
              continue;
          }
          const auto *szNewValue = reinterpret_cast<LPCWSTR>(newValue.utf16());
          memcpy(lpStringBuf, szNewValue, wcslen(szNewValue) * sizeof(wchar_t));
          if (valueName.contains("version", Qt::CaseInsensitive)) {
              LPVOID lpFixedBuf = nullptr;
              DWORD dwFixedLen = 0;
              if (VerQueryValueW(lpBuffer, L"\\", &lpFixedBuf, static_cast<PUINT>(&dwFixedLen))) {
                  QVersionNumber ver = QVersionNumber::fromString(newValue);
                  auto *pFixedInfo = reinterpret_cast<VS_FIXEDFILEINFO *>(lpFixedBuf);
                  if (valueName.contains("fileversion", Qt::CaseInsensitive)) {
                      pFixedInfo->dwFileVersionMS = DWORD(ver.majorVersion());
                      pFixedInfo->dwFileVersionLS = DWORD(ver.minorVersion());
                  } else {
                      pFixedInfo->dwProductVersionMS = DWORD(ver.majorVersion());
                      pFixedInfo->dwProductVersionLS = DWORD(ver.minorVersion());
                  }
              } else {
                  qWarning() << "Cannot query version info";
              }
          }
          if (!UpdateResourceW(hResource, RT_VERSION, MAKEINTRESOURCE(VS_VERSION_INFO), lpTranslate[i]->wLanguage, lpBuffer, dwSize)) {
              qWarning() << "Cannot update version resource";
          }
      }

      delete[] lpBuffer;

      if (!EndUpdateResourceW(hResource, FALSE)) {
          qWarning() << "Cannot end updating resource";
          return false;
      }

      return true;
  }
  ```

  注：
  - 源码摘自 [Qt Installer Framework](https://code.qt.io/cgit/installer-framework/installer-framework.git/tree/src/libs/installer/fileutils.cpp), 并进行了一定的修改
  - MSDN官方资料：<https://docs.microsoft.com/en-us/previous-versions/ms997538(v=msdn.10)>, <https://docs.microsoft.com/en-us/windows/win32/api/winver/nf-winver-verqueryvaluew>
- 创建指向网址的快捷方式（*.url）

  ```cpp
  // 头文件：shlobj.h
  // 库文件：Shell32.lib（Shell32.dll）

  struct DeCoInitializer {
      DeCoInitializer() : neededCoInit(CoInitialize(nullptr) == S_OK) {}
      ~DeCoInitializer() {
          if (neededCoInit) {
              CoUninitialize();
          }
      }
      bool neededCoInit;
  };

  bool createLink(const QString &fileName, const QString &linkName, QString workingDir, const QString &arguments = QString(), const QString &iconPath = QString(), const QString &iconId = QString(), const QString &description = QString()) {
      // CoInitialize cleanup object
      DeCoInitializer _;

      IUnknown *iunkn = nullptr;

      if (fileName.toLower().startsWith(QLatin1String("http:")) || fileName.toLower().startsWith(QLatin1String("ftp:"))) {
          IUniformResourceLocator *iurl = nullptr;
          if (FAILED(CoCreateInstance(CLSID_InternetShortcut, nullptr, CLSCTX_INPROC_SERVER, IID_IUniformResourceLocator, reinterpret_cast<LPVOID *>(&iurl)))) {
              return false;
          }

          if (FAILED(iurl->SetURL(reinterpret_cast<LPCWSTR>(fileName.utf16()), IURL_SETURL_FL_GUESS_PROTOCOL))) {
              iurl->Release();
              return false;
          }
          iunkn = iurl;
      } else {
          bool success = QFile::link(fileName, linkName);

          if (!success) {
              return success;
          }

          if (workingDir.isEmpty()) {
              workingDir = QFileInfo(fileName).absolutePath();
          }
          workingDir = QDir::toNativeSeparators(workingDir);

          IShellLink *psl = nullptr;
          if (FAILED(CoCreateInstance(CLSID_ShellLink, nullptr, CLSCTX_INPROC_SERVER, IID_IShellLink, reinterpret_cast<LPVOID *>(&psl)))) {
              return success;
          }

          // TODO: implement this server side, since there's not Qt equivalent to set working dir and arguments
          psl->SetPath(reinterpret_cast<LPCWSTR>(QDir::toNativeSeparators(fileName).utf16()));
          psl->SetWorkingDirectory(reinterpret_cast<LPCWSTR>(workingDir.utf16()));
          if (!arguments.isNull()) {
              psl->SetArguments(reinterpret_cast<LPCWSTR>(arguments.utf16()));
          }
          if (!iconPath.isNull()) {
              psl->SetIconLocation(reinterpret_cast<LPCWSTR>(iconPath.utf16()), iconId.toInt());
          }
          if (!description.isNull()) {
              psl->SetDescription(reinterpret_cast<LPCWSTR>(description.utf16()));
          }
          iunkn = psl;
      }

      IPersistFile *ppf = nullptr;
      if (SUCCEEDED(iunkn->QueryInterface(IID_IPersistFile, reinterpret_cast<void **>(&ppf)))) {
          ppf->Save(reinterpret_cast<LPCWSTR>(QDir::toNativeSeparators(linkName).utf16()), true);
          ppf->Release();
      }
      iunkn->Release();

      PIDLIST_ABSOLUTE pidl;  // Force start menu cache update
      if (SUCCEEDED(SHGetFolderLocation(0, CSIDL_STARTMENU, 0, 0, &pidl))) {
          SHChangeNotify(SHCNE_UPDATEDIR, SHCNF_IDLIST, pidl, 0);
          CoTaskMemFree(pidl);
      }
      if (SUCCEEDED(SHGetFolderLocation(0, CSIDL_COMMON_STARTMENU, 0, 0, &pidl))) {
          SHChangeNotify(SHCNE_UPDATEDIR, SHCNF_IDLIST, pidl, 0);
          CoTaskMemFree(pidl);
      }

      return true;
  }
  ```

- 如何确保GUI程序输出的调试信息能被控制台窗口接收到？

  ```cpp
  // console.h
  #pragma once

  #include <fstream>
  #include <iostream>

  class Console {
  public:
      Console();
      ~Console();

  private:
      bool parentConsole;
      bool newConsoleCreated;

      std::ofstream m_newCout;
      std::ofstream m_newCerr;

      std::streambuf* m_oldCout;
      std::streambuf* m_oldCerr;
  };

  // console.cpp
  #include "console.h"

  #include <wincon.h>

  static bool isRedirected(HANDLE stdHandle) {
      if (stdHandle == nullptr) // launched from GUI
          return false;
      DWORD fileType = GetFileType(stdHandle);
      if (fileType == FILE_TYPE_UNKNOWN) {
          // launched from console, but no redirection
          return false;
      }
      // redirected into file, pipe ...
      return true;
  }

  /**
   * Redirects stdout, stderr output to console
   *
   * Console is a RAII class that ensures stdout, stderr output is visible
   * for GUI applications on Windows.
   *
   * If the application is launched from the explorer, startup menu etc
   * a new console window is created.
   *
   * If the application is launched from the console (cmd.exe), output is
   * printed there.
   *
   * If the application is launched from the console, but stdout is redirected
   * (e.g. into a file), Console does not interfere.
   */
  Console::Console() :
      m_oldCout(nullptr),
      m_oldCerr(nullptr),
      parentConsole(false),
      newConsoleCreated(false)
  {
      bool isCoutRedirected = isRedirected(GetStdHandle(STD_OUTPUT_HANDLE));
      bool isCerrRedirected = isRedirected(GetStdHandle(STD_ERROR_HANDLE));

      if (!isCoutRedirected) { // verbose output only ends up in cout
          // try to use parent console. else launch & set up new console
          parentConsole = AttachConsole(ATTACH_PARENT_PROCESS);
          if (!parentConsole) {
              newConsoleCreated = true;
              AllocConsole();
              HANDLE handle = GetStdHandle(STD_OUTPUT_HANDLE);
              if (handle != INVALID_HANDLE_VALUE) {
                  COORD largestConsoleWindowSize = GetLargestConsoleWindowSize(handle);
                  largestConsoleWindowSize.X -= 3;
                  largestConsoleWindowSize.Y = 5000;
                  SetConsoleScreenBufferSize(handle, largestConsoleWindowSize);
              }
              handle = GetStdHandle(STD_INPUT_HANDLE);
              if (handle != INVALID_HANDLE_VALUE)
                  SetConsoleMode(handle, ENABLE_INSERT_MODE | ENABLE_QUICK_EDIT_MODE
                                 | ENABLE_EXTENDED_FLAGS);
  #ifndef Q_CC_MINGW
              HMENU systemMenu = GetSystemMenu(GetConsoleWindow(), FALSE);
              if (systemMenu != nullptr)
                  RemoveMenu(systemMenu, SC_CLOSE, MF_BYCOMMAND);
              DrawMenuBar(GetConsoleWindow());
  #endif
          }
      }

      if (!isCoutRedirected) {
          m_oldCout = std::cout.rdbuf();
          m_newCout.open("CONOUT$");
          std::cout.rdbuf(m_newCout.rdbuf());
      }

      if (!isCerrRedirected) {
          m_oldCerr = std::cerr.rdbuf();
          m_newCerr.open("CONOUT$");
          std::cerr.rdbuf(m_newCerr.rdbuf());
      }
  }

  Console::~Console()
  {
      if (parentConsole) {
          // simulate enter key to switch to boot prompt
          PostMessage(GetConsoleWindow(), WM_KEYDOWN, 0x0D, 0);
      } else if (newConsoleCreated) {
          system("PAUSE");
      }

      if (m_oldCerr)
          std::cerr.rdbuf(m_oldCerr);
      if (m_oldCout)
          std::cout.rdbuf(m_oldCout);

      if (m_oldCout)
          FreeConsole();
  }
  ```

- 同一个应用程序如何开启多个选项卡（Win7开始添加的任务栏选项卡，Aero Peek）：请参考<https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/Win7Samples/winui/shell/appshellintegration/TabThumbnails>
