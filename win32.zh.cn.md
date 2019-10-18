# Win32 笔记

- 控制台程序隐藏命令行窗口：`#pragma comment(linker, "/SUBSYSTEM:WINDOWS /ENTRY:\"mainCRTStartup\"")`。如果编译时提示入口点错误，就把`mainCRTStartup`更改为`wmainCRTStartup`，是`UNICODE`的问题
- 窗口程序显示命令行窗口：`#pragma comment(linker, "/SUBSYSTEM:CONSOLE /ENTRY:\"WinMainCRTStartup\"")`。如果编译时提示入口点错误，就把`WinMainCRTStartup`更改为`wWinMainCRTStartup`，是`UNICODE`的问题
- 如何做到完美的开机自启：注册一个 Windows 服务，使用 Windows 服务来启动你的程序。这样做不仅可以使你的程序比任何程序都早运行，还可以做到以管理员权限运行而不弹出 UAC 提示框。注册服务时，注意选择为可与图形界面交互，否则无法启动 GUI 程序。很多资料都说，Windows Vista 之后的系统不支持服务程序启动 GUI 程序，但经过我的亲自测试，直到 Windows 10 1809 都还支持。
- 通过嵌入 Manifest 文件实现完美的高 DPI 缩放：<https://msdn.microsoft.com/en-us/C9488338-D863-45DF-B5CB-7ED9B869A5E2>
- 自定义开始屏幕磁贴：<https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn449733(v=win.10)>
- 如何使用API将文件（夹）移动到回收站而不是直接彻底删除：

  ```cpp
  // 头文件：shellapi.h
  // 库文件：Shell32.lib（Shell32.dll）
  // 示例代码，请注意修改
  BOOL MoveToRecycleBin(LPCTSTR pszPath, BOOL bDelete)
  {
    SHFILEOPSTRUCT shDelFile;
    shDelFile.fFlags |= FOF_SILENT; // don't report progress
    shDelFile.fFlags |= FOF_NOERRORUI; // don't report errors
    shDelFile.fFlags |= FOF_NOCONFIRMATION; // don't confirm delete
    // Set SHFILEOPSTRUCT params for delete operation
    shDelFile.wFunc = FO_DELETE; // REQUIRED: delete operation
    shDelFile.pFrom = pszPath; // REQUIRED: which file(s)
    shDelFile.pTo = NULL; // MUST be NULL
    if (bDelete)
    {
      shDelFile.fFlags &= ~FOF_ALLOWUNDO; // don't use Recycle Bin if delete is requested
    }
    else
    {
      shDelFile.fFlags |= FOF_ALLOWUNDO; // move to Recycle Bin
    }
    return SHFileOperation(&shDelFile);
  }
  ```

- 判断当前程序是否正在以管理员权限运行

  ```cpp
  // 头文件：securitybaseapi.h (include Windows.h)
  // 库文件：Advapi32.lib（Advapi32.dll）
  BOOL IsUserAdmin(VOID)
  /*++
  Routine Description: This routine returns TRUE if the caller's
  process is a member of the Administrators local group. Caller is NOT
  expected to be impersonating anyone and is expected to be able to
  open its own process and process token.
  Arguments: None.
  Return Value:
     TRUE - Caller has Administrators local group.
     FALSE - Caller does not have Administrators local group. --
  */
  {
  BOOL b;
  SID_IDENTIFIER_AUTHORITY NtAuthority = SECURITY_NT_AUTHORITY;
  PSID AdministratorsGroup;
  b = AllocateAndInitializeSid(
      &NtAuthority,
      2,
      SECURITY_BUILTIN_DOMAIN_RID,
      DOMAIN_ALIAS_RID_ADMINS,
      0, 0, 0, 0, 0, 0,
      &AdministratorsGroup);
  if(b)
  {
      if (!CheckTokenMembership( NULL, AdministratorsGroup, &b))
      {
           b = FALSE;
      }
      FreeSid(AdministratorsGroup);
  }

  return(b);
  }
  ```

  摘自：<https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-checktokenmembership>
- 以管理员权限启动程序：

  ```cpp
  // 头文件：shellapi.h
  // 库文件：Shell32.lib（Shell32.dll）
  SHELLEXECUTEINFO sei;
  sei.lpVerb = TEXT("runas"); // 这一行是关键，有了这一行才能以管理员权限执行
  sei.lpFile = TEXT("notepad.exe"); // 待启动程序的路径
  sei.nShow = SW_HIDE; // 想隐藏程序窗口的话要加上这一句，否则不需要这一行
  sei.lpParameters = TEXT("/ABC /DEF"); // 要传给程序的参数，没有的话也不需要这一行
  if(ShellExecuteEx(&sei) != TRUE)
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
  }
  ```

- Windows服务程序框架

  ```cpp
  #include <windows.h>
  #include <tchar.h>

  // 服务名
  #define SERVICE_NAME TEXT("mysvc")
  // 服务显示的名字
  #define SERVICE_DISPLAY_NAME TEXT("My Service")
  // 服务描述
  #define SERVICE_DESCRIPTION TEXT("This service will do something.")

  SERVICE_STATUS serviceStatus = { 0 };
  SERVICE_STATUS_HANDLE serviceStatusHandle = nullptr;

  // 服务安装函数
  VOID Install()
  {
      TCHAR filePath[MAX_PATH + 1] = { 0 };
      DWORD dwSize = GetModuleFileName(nullptr, filePath, MAX_PATH);
      filePath[dwSize] = 0;
      bool result = false;
      SC_HANDLE hSCM = OpenSCManager(nullptr, nullptr, SC_MANAGER_ALL_ACCESS);
      if (hSCM != nullptr)
      {
          SC_HANDLE hService = CreateService(hSCM, SERVICE_NAME, SERVICE_DISPLAY_NAME, SERVICE_ALL_ACCESS, SERVICE_WIN32_OWN_PROCESS | SERVICE_INTERACTIVE_PROCESS, SERVICE_AUTO_START, SERVICE_ERROR_NORMAL, filePath, nullptr, nullptr, nullptr, nullptr, nullptr);
          if (hService != nullptr)
          {
              SERVICE_DESCRIPTION sdesc;
              sdesc.lpDescription = const_cast<LPTSTR>(SERVICE_DESCRIPTION);
              result = ChangeServiceConfig2(hService, SERVICE_CONFIG_DESCRIPTION, &sdesc);
              CloseServiceHandle(hService);
          }
          CloseServiceHandle(hSCM);
      }
      if (result)
          OutputDebugString(TEXT("Installation succeeded."));
      else
          OutputDebugString(TEXT("Installation failed. Administrator privilege is needed."));
  }

  // 服务卸载函数
  VOID Uninstall()
  {
      bool result = false;
      SC_HANDLE hSCM = OpenSCManager(nullptr, nullptr, SC_MANAGER_ALL_ACCESS);
      if (hSCM != nullptr)
      {
          SC_HANDLE hService = OpenService(hSCM, SERVICE_NAME, DELETE);
          if (hService != nullptr)
          {
              result = DeleteService(hService);
              CloseServiceHandle(hService);
          }
          CloseServiceHandle(hSCM);
      }
      if (result)
          OutputDebugString(TEXT("Uninstallation succeeded."));
      else
          OutputDebugString(TEXT("Uninstallation failed. Administrator privilege is needed."));
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
      SetServiceStatus(serviceStatusHandle, &serviceStatus);
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
      serviceStatusHandle = RegisterServiceCtrlHandler(SERVICE_NAME, ServiceCtrlHandler);
      if (serviceStatusHandle == nullptr)
          return;
      ZeroMemory(&serviceStatus, sizeof(serviceStatus));
      serviceStatus.dwServiceType = SERVICE_WIN32_OWN_PROCESS | SERVICE_INTERACTIVE_PROCESS;
      serviceStatus.dwControlsAccepted = 0;
      serviceStatus.dwCurrentState = SERVICE_START_PENDING;
      serviceStatus.dwWin32ExitCode = 0;
      serviceStatus.dwServiceSpecificExitCode = 0;
      serviceStatus.dwCheckPoint = 0;
      if (SetServiceStatus(serviceStatusHandle, &serviceStatus) != TRUE)
          return;
      serviceStatus.dwCurrentState = SERVICE_RUNNING;
      serviceStatus.dwControlsAccepted = SERVICE_ACCEPT_STOP | SERVICE_ACCEPT_SHUTDOWN | SERVICE_ACCEPT_PAUSE_CONTINUE;
      serviceStatus.dwWin32ExitCode = 0;
      serviceStatus.dwCheckPoint = 0;
      if (SetServiceStatus(serviceStatusHandle, &serviceStatus) != TRUE)
          return;
      HANDLE hThread = CreateThread(nullptr, 0, ServiceWorkerThread, nullptr, 0, nullptr);
      WaitForSingleObject(hThread, INFINITE);
      serviceStatus.dwCurrentState = SERVICE_STOPPED;
      serviceStatus.dwControlsAccepted = 0;
      serviceStatus.dwWin32ExitCode = 0;
      serviceStatus.dwCheckPoint = 3;
      SetServiceStatus(serviceStatusHandle, &serviceStatus);
  }

  // 程序main函数
  int _tmain(int argc, TCHAR *argv[])
  {
      SERVICE_TABLE_ENTRY ServiceTable[] =
      {
          {const_cast<LPTSTR>(SERVICE_NAME), (LPSERVICE_MAIN_FUNCTION)ServiceMain},
          {nullptr, nullptr}
      };
      for (unsigned int i = 1; i != argc; ++i)
          if ((_tcscmp(argv[i], TEXT("-i")) == 0) || (_tcscmp(argv[i], TEXT("-I")) == 0) || (_tcscmp(argv[i], TEXT("-install")) == 0) || (_tcscmp(argv[i], TEXT("-INSTALL")) == 0) || (_tcscmp(argv[i], TEXT("-Install")) == 0))
          {
              Install();
              break;
          }
          else if ((_tcscmp(argv[i], TEXT("-u")) == 0) || (_tcscmp(argv[i], TEXT("-U")) == 0) || (_tcscmp(argv[i], TEXT("-uninstall")) == 0) || (_tcscmp(argv[i], TEXT("-UNINSTALL")) == 0) || (_tcscmp(argv[i], TEXT("-Uninstall")) == 0))
          {
              Uninstall();
              break;
          }
      if (StartServiceCtrlDispatcher(ServiceTable) != TRUE)
          return GetLastError();
      return 0;
  }
  ```

- 获取系统是否开启深色模式/浅色模式：详见[qt.zh.cn.md：Windows平台如何获取是否开启深色模式/浅色模式](/qt.zh.cn.md)。
- 将窗口设置为深色模式：详见[qt.zh.cn.md：Windows平台如何将窗口设置为深色模式](/qt.zh.cn.md)。
- 读写INI文件
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

- 读写注册表
- 置顶/取消置顶窗口
- 将最小化或被其他窗口挡住的窗口移到最前
