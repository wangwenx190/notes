# Win32 笔记

- 控制台程序隐藏命令行窗口：共3个方法
  1. 代码

     ```cpp
     #if defined(UNICODE) || defined(_UNICODE)
     #pragma comment(linker, "/SUBSYSTEM:WINDOWS /ENTRY:\"wmainCRTStartup\"")
     #else
     #pragma comment(linker, "/SUBSYSTEM:WINDOWS /ENTRY:\"mainCRTStartup\"")
     #endif
     ```

  2. Visual Studio 设置：选中项目->右键“属性”->连接器->系统->子系统->选择子系统，选中项目->右键“属性”->连接器->高级->入口点->填写入口点。
  3. 连接器参数：通过IDE的设置或修改编译时的脚本，将上面的两个参数传给连接器即可

- 窗口程序显示命令行窗口：共3个方法
  1. 代码

     ```cpp
     #if defined(UNICODE) || defined(_UNICODE)
     #pragma comment(linker, "/SUBSYSTEM:CONSOLE /ENTRY:\"wWinMainCRTStartup\"")
     #else
     #pragma comment(linker, "/SUBSYSTEM:CONSOLE /ENTRY:\"WinMainCRTStartup\"")
     #endif
     ```

  2. Visual Studio 设置：选中项目->右键“属性”->连接器->系统->子系统->选择子系统，选中项目->右键“属性”->连接器->高级->入口点->填写入口点。
  3. 连接器参数：通过IDE的设置或修改编译时的脚本，将上面的两个参数传给连接器即可
- 如何做到完美的开机自启：注册一个 Windows 服务，使用 Windows 服务来启动你的程序。这样做不仅可以使你的程序比任何程序都早运行，还可以做到以管理员权限运行而不弹出 UAC 提示框。注册服务时，注意选择为可与图形界面交互，否则无法启动 GUI 程序。很多资料都说，Windows Vista 之后的系统不支持服务程序启动 GUI 程序，但经过我的亲自测试，直到 Windows 10 都还支持。
- 通过嵌入清单文件（*.manifest）开启系统的DPI缩放支持并设置相应的缩放策略

  ```xml
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <!-- 基础信息设置，根据项目的实际信息填写，非必需。name不是应用程序名，而是Java风格的包名。 -->
    <assemblyIdentity type="win32" name="com.mycompany.myapplication" version="1.0.0.0"/>
    <!-- 应用程序描述，根据项目的实际信息填写，非必需。 -->
    <description>My application</description>
    <!-- 下面这个设置依赖项的区段是固定的，不要改动。这一段的作用是开启现代风格（Aero）的控件外观，没有这一段会导致默认Win32控件为经典外观。必需。 -->
    <dependency>
      <dependentAssembly>
        <assemblyIdentity type="win32" name="Microsoft.Windows.Common-Controls" version="6.0.0.0" processorArchitecture="*" publicKeyToken="6595b64144ccf1df" language="*"/>
      </dependentAssembly>
    </dependency>
    <!-- 权限设置，根据项目的具体需求设置，非必需。 -->
    <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
      <security>
        <requestedPrivileges>
          <!-- 声明我们的程序不需要管理员权限。asInvoker意为与调用者权限相同。requireAdministrator意为需要管理员权限。 -->
          <!-- asInvoker/highestAvailable/requireAdministrator -->
          <requestedExecutionLevel level="asInvoker" uiAccess="false"/>
        </requestedPrivileges>
      </security>
    </trustInfo>
    <!-- 兼容性设置，根据项目的具体需求设置，非必需。 -->
    <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
      <application>
        <!-- “maxversiontested”对于使用了 XAML Island 技术的程序而言是必需的，否则程序无法运行。对于 Win32 程序而言是可选的。 -->
        <!-- 由于Windows系统的bug，每一个经过测试的版本都要在此列举出来，而不是仅仅声明“最高”测试的版本。此特性虽于1903引入，但最低可填1809。 -->
        <!-- Windows 10 Version 1809 (October 2018 Update) -->
        <maxversiontested Id="10.0.17763.0"/>
        <!-- Windows 10 Version 1903 (May 2019 Update) -->
        <maxversiontested Id="10.0.18362.0"/>
        <!-- Windows 10 Version 1909 (November 2019 Update) -->
        <maxversiontested Id="10.0.18363.0"/>
        <!-- Windows 10 Version 2004 (May 2020 Update) -->
        <maxversiontested Id="10.0.19041.0"/>
        <!-- Windows 10 Version 20H2 (October 2020 Update) -->
        <maxversiontested Id="10.0.19042.0"/>
        <!-- Windows 10 Version 21H1 (May 2021 Update) -->
        <maxversiontested Id="10.0.19043.0"/>
        <!-- Windows 10 Version 21H2 (November 2021 Update) -->
        <maxversiontested Id="10.0.19044.0"/>
        <!-- Windows 10 Version 22H2 (October 2022 Update) -->
        <maxversiontested Id="10.0.19045.0"/>
        <!-- Windows 11 Version 21H2 -->
        <maxversiontested Id="10.0.22000.0"/>
        <!-- Windows 11 Version 22H2 (October 2022 Update) -->
        <maxversiontested Id="10.0.22621.0"/>
        <!-- 把不支持的系统从下面移除即可。注意下面的Id值都是固定的，不要改动。 -->
        <!-- 对于没有列举在此的系统，Windows会对你的程序使用的API进行特定的fallback，而不是让你的程序无法运行。 -->
        <!-- 这里最低只能填Vista，是不能填XP及以下的。 -->
        <!-- Windows Vista and Windows Server 2008 -->
        <supportedOS Id="{e2011457-1546-43c5-a5fe-008deee3d3f0}"/>
        <!-- Windows 7 and Windows Server 2008 R2 -->
        <supportedOS Id="{35138b9a-5d96-4fbd-8e2d-a2440225f93a}"/>
        <!-- Windows 8 and Windows Server 2012 -->
        <supportedOS Id="{4a2f28e3-53b9-4441-ba9c-d69d4a4a6e38}"/>
        <!-- Windows 8.1 and Windows Server 2012 R2 -->
        <supportedOS Id="{1f676c76-80e1-4239-95bb-83d0f6d0da78}"/>
        <!-- Windows 10 and Windows 11 -->
        <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}"/>
      </application>
    </compatibility>
    <!-- Windows 设置，根据项目的具体需求设置，非必需。 -->
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
        <!-- 声明我们的程序支持高DPI缩放，Windows Vista ~ Win8。如果不进行此项设置，系统会自动强行拉伸程序的界面，导致界面非常模糊。此选项不可填多个值。推荐开启。 -->
        <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">True/PM</dpiAware>
        <!-- 隔离打印机驱动。开启此选项后会使程序更加稳定，推荐开启。 -->
        <printerDriverIsolation xmlns="http://schemas.microsoft.com/SMI/2011/WindowsSettings">True</printerDriverIsolation>
        <!-- 开启此选项后会导致外部程序无法用普通方法查找到本程序的窗口句柄。例如Spy++就无法获取本程序的窗口句柄。 -->
        <disableWindowFiltering xmlns="http://schemas.microsoft.com/SMI/2011/WindowsSettings">True</disableWindowFiltering>
        <!-- 声明我们的程序支持高DPI缩放，Win8.1 ~ Win11。此选项可填多个值，越靠前的值越优先。推荐开启。 -->
        <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2, PerMonitor</dpiAwareness>
        <!-- 声明我们的程序支持长路径（超过260个字符）。 -->
        <longPathAware xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">True</longPathAware>
        <!-- 如果你的程序完全是用GDI绘制的界面，建议开启此选项，此选项的原理为系统会将你要绘制的内容自动放大，然后根据当前分辨率缩小渲染，从而尽量保证锐利度。注意此选项无法与dpiAware以及dpiAwareness兼容。 -->
        <!-- <gdiScaling xmlns="http://schemas.microsoft.com/SMI/2017/WindowsSettings">True</gdiScaling> -->
        <!-- 将进程的代码页设置为 UTF-8。Win10系统仅支持设置为UTF-8，设置为其他内容均会被系统忽略，从Win11开始还支持设置为传统的代码页，例如en-US。 -->
        <activeCodePage xmlns="http://schemas.microsoft.com/SMI/2019/WindowsSettings">UTF-8</activeCodePage>
        <!-- 设置进程的堆类型为新型堆类型（现代化类型，微软推荐使用）。此选项仅支持SegmentHeap这一个值，其他值均会被忽略。 -->
        <heapType xmlns="http://schemas.microsoft.com/SMI/2020/WindowsSettings">SegmentHeap</heapType>
      </windowsSettings>
    </application>
  </assembly>
  ```

  注意事项：
  - 清单文件里的所有条目都是**大小写敏感**的，比如`supportedOS`不能写成`SupportedOS`或者`supportedos`，否则对应的条目不会生效，一定要注意！
  - 使用方法：将以上内容保存到一个文本文档（`.txt`）中（注意编码问题；不要打乱格式），并重命名为**应用程序文件名.exe.manifest**（例如：`wmplayer.exe.manifest`）即可，切记**此文件一定要放到与应用程序同级的目录中**。如果不想将此清单文件暴露在外部，可以将其插入到资源文件中，这样一来编译后清单文件就会被嵌入到程序中，就不需要在外部单独放一份清单文件了。嵌入到程序中后，外部就算有同名的清单文件，也会被系统自动忽略，程序不会受到任何影响，不用担心。

    ```text
    // 将以下语句写入rc文件即可
    // #define RT_MANIFEST 24
    // #define CREATEPROCESS_MANIFEST_RESOURCE_ID 1
    // 如果资源编译器无法找到“CREATEPROCESS_MANIFEST_RESOURCE_ID”和“RT_MANIFEST”
    // 的定义，就把上面两行取消注释，一起放入rc文件中，或者直接替换成对应的数值。
    CREATEPROCESS_MANIFEST_RESOURCE_ID RT_MANIFEST "myapp.manifest"
    ```

  - 经过清单文件的设置后，系统不会对程序的界面元素进行强制拉伸，但相应元素的大小调整完全要自行解决。除UWP程序外，系统不会替任何应用程序对DPI改变进行处理。此清单文件的作用是，禁止系统对程序的界面进行自动拉伸（因为这会导致界面很糊）。
  - 参考资料：<https://docs.microsoft.com/en-us/windows/win32/sbscs/application-manifests>, <https://docs.microsoft.com/en-us/windows/win32/hidpi/high-dpi-desktop-application-development-on-windows>，<https://docs.microsoft.com/en-us/previous-versions/windows/desktop/legacy/mt846517(v=vs.85)>，<https://docs.microsoft.com/en-us/dotnet/framework/winforms/high-dpi-support-in-windows-forms>，<https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/DPIAwarenessPerWindow>，<https://github.com/Microsoft/WPF-Samples/tree/master/PerMonitorDPI>
- 自定义开始屏幕磁贴

  ```xml
  <Application xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <VisualElements
          <!-- 磁贴背景颜色：填写颜色的16进制代码 -->
          BackgroundColor="#FF0000"
          <!-- 是否在磁贴上显示应用的名字：填写on/off -->
          ShowNameOnSquare150x150Logo="on"
          <!-- 磁贴文字颜色，白色还是黑色：填写light/dark -->
          ForegroundText="light"
          <!-- 大磁贴图片：填写图片文件的相对路径 -->
          Square150x150Logo="Assets\150x150Logo.png"
          <!-- 小磁贴图片：填写图片文件的相对路径 -->
          Square70x70Logo="Assets\70x70Logo.png"/>
  </Application>
  ```

  注意事项：
  - 使用方法：将以上内容保存到一个文本文档（`.txt`）中（注意编码问题；不要打乱格式），并重命名为**应用程序文件名.VisualElementsManifest.xml**（例如：`wmplayer.VisualElementsManifest.xml`）即可，切记**此文件一定要放到与应用程序同级的目录中**。**此文件只能放在外部**，嵌入到程序中会失效。
  - 参考资料：<https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn449733(v=win.10)>
- 如何使用API将文件（夹）移动到回收站而不是直接彻底删除：

  ```cpp
  // 头文件：shellapi.h
  // 库文件：Shell32.lib（Shell32.dll）
  // 示例代码，请注意修改
  BOOL MoveToRecycleBin(LPCWSTR pszPath, BOOL bDelete)
  {
    SHFILEOPSTRUCTW sfos;
    SecureZeroMemory(&sfos, sizeof(sfos));
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
  // 头文件：processthreadsapi.h (include Windows.h), securitybaseapi.h (include Windows.h)
  // 库文件：Advapi32.lib（Advapi32.dll）

  bool IsCurrentProcessElevated()
  {
      bool Result = false;

      HANDLE CurrentProcessAccessToken = nullptr;
      if (::OpenProcessToken(
          ::GetCurrentProcess(),
          TOKEN_ALL_ACCESS,
          &CurrentProcessAccessToken))
      {
          TOKEN_ELEVATION Information = { 0 };
          DWORD Length = sizeof(Information);
          if (::GetTokenInformation(
              CurrentProcessAccessToken,
              TOKEN_INFORMATION_CLASS::TokenElevation,
              &Information,
              Length,
              &Length))
          {
              Result = Information.TokenIsElevated;
          }

          ::CloseHandle(CurrentProcessAccessToken);
      }

      return Result;
  }
  ```

  摘自：<https://github.com/M2Team/NanaRun/blob/4fefc0151fa877d32ba8921c5863df163ee327c8/MinSudo/MinSudo.cpp#L287>
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

  bool runAsAdmin(const QString &path, const QString &params) {
      // This function uses UAC to ask for admin privileges. If the
      // user is no administrator yet and the computer's policies are set to not
      // use UAC (which is the case in some corporate networks), the call to
      // this function will simply succeed and not at all launch the child process. To
      // avoid this, we detect this situation here and return early.
      if (!hasAdminRights()) { // 使用上面提到的方法检测是否已经拥有管理员权限
          QLatin1String key("HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System");
          QSettings registry(key, QSettings::NativeFormat);
          const QVariant enableLUA = registry.value(QLatin1String("EnableLUA"), 0);
          if ((enableLUA.type() == QVariant::Int) && (enableLUA.toInt() == 0)) {
              return false;
          }
      }

      // 虽然没有用到这个变量，但它的析构函数有用
      DeCoInitializer _deCoInitializer;

      SHELLEXECUTEINFOW sei;
      SecureZeroMemory(&sei, sizeof(sei));
      // 这一行是关键，有了这一行才能以管理员权限执行
      sei.lpVerb = L"runas";
      sei.lpFile = reinterpret_cast<LPCWSTR>(path.utf16());
      // 想隐藏程序窗口的话要加上这一句，否则不需要这一行
      sei.nShow = SW_HIDE;
      sei.lpParameters = reinterpret_cast<LPCWSTR>(params.utf16());
      // 必需的参数，不要忘掉
      sei.cbSize = sizeof(SHELLEXECUTEINFOW);
      // 必需的参数，不要忘掉
      sei.fMask = SEE_MASK_NOASYNC;

      if (ShellExecuteExW(&sei) != TRUE) {
          DWORD dwStatus = GetLastError();
          if (dwStatus == ERROR_CANCELLED) {
              std::cerr << "[UAC] : User denied to give admin privilege." << std::endl;
          }
          else if (dwStatus == ERROR_FILE_NOT_FOUND) {
              std::cerr << "[UAC] : File not found." << std::endl;
          }
          else {
              std::cerr << "[UAC] : balabalabala." << std::endl;
          }
          return false;
      }

      return true;
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
              SecureZeroMemory(&sdesc, sizeof(sdesc));
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
  VOID WINAPI ServiceMain(DWORD argc, LPWSTR *argv)
  {
      (void)argc;
      (void)argv;
      serviceStatusHandle = RegisterServiceCtrlHandlerW(SERVICE_NAME, ServiceCtrlHandler);
      if (serviceStatusHandle == nullptr)
          return;
      SecureZeroMemory(&serviceStatus, sizeof(serviceStatus));
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

- 获取系统是否已经开启了深色模式/浅色模式 & 将窗口设置为深色模式/浅色模式

  设置路径：设置->个性化->颜色->选择颜色->浅色/深色/自定义
  - 系统层面

    ```text
    HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
    ```

    查看`AppsUseLightTheme`（程序使用浅色主题）和`SystemUsesLightTheme`（系统使用浅色主题）的键值，若为`0`则表示目前应用的是深色主题，若为`1`则表示目前应用的是浅色主题。修改此键值则可修改系统的相关设置。
  - 程序层面

    ```cpp
    using ShouldAppsUseDarkModePFN = BOOL(WINAPI *)();
    static ShouldAppsUseDarkModePFN pfnShouldAppsUseDarkMode = nullptr;

    using ShouldSystemUseDarkModePFN = BOOL(WINAPI *)();
    static ShouldSystemUseDarkModePFN pfnShouldSystemUseDarkMode = nullptr;

    const HMODULE UxThemeDll = LoadLibraryW(L"UxTheme.dll");
    if (UxThemeDll) {
        pfnShouldAppsUseDarkMode = reinterpret_cast<ShouldAppsUseDarkModePFN>(GetProcAddress(UxThemeDll, MAKEINTRESOURCEA(132)));
        pfnShouldSystemUseDarkMode = reinterpret_cast<ShouldSystemUseDarkModePFN>(GetProcAddress(UxThemeDll, MAKEINTRESOURCEA(138)));
    }

    enum : DWORD {
        DWMWA_USE_IMMERSIVE_DARK_MODE = 20,
        DWMWA_USE_IMMERSIVE_DARK_MODE_BEFORE_20H1 = 19
    };

    static inline bool isDarkBorderEnabled(const HWND hwnd) {
        BOOL result = FALSE;
        return SUCCEEDED(DwmGetWindowAttribute(hwnd, DWMWA_USE_IMMERSIVE_DARK_MODE, &result, sizeof(result))) || SUCCEEDED(DwmGetWindowAttribute(hwnd, DWMWA_USE_IMMERSIVE_DARK_MODE_BEFORE_20H1, &result, sizeof(result)));
    }

    static inline bool setDarkBorderEnabled(const HWND hwnd, const bool enable = true) {
        const BOOL darkBorder = enable ? TRUE : FALSE;
        return SUCCEEDED(DwmSetWindowAttribute(hwnd, DWMWA_USE_IMMERSIVE_DARK_MODE, &darkBorder, sizeof(darkBorder))) || SUCCEEDED(DwmSetWindowAttribute(hwnd, DWMWA_USE_IMMERSIVE_DARK_MODE_BEFORE_20H1, &darkBorder, sizeof(darkBorder)));
    }
    ```

    注意

    - 调用`DwmSetWindowAttribute(hwnd, DWMWA_USE_IMMERSIVE_DARK_MODE, &darkBorder, sizeof(darkBorder))`这个API的作用是将系统标题栏和窗口边框（以及部分系统标准控件，例如滚动条等）更改为深色/浅色主题的样式（但自绘的任何控件均不会被影响，例如Qt的各种Widget基本都是自绘的，所以不会被系统主题所影响，只能使用QSS统一修改样式），但窗口内部各个控件/元素的颜色不会被修改，需要这个程序的开发者自行编写一个与之相匹配的主题调色板。因此，无边框程序执行此API无效。
    - `ShouldAppsUseDarkMode`这个API在最新版的Windows上已经失效，可以使用`ShouldSystemUseDarkMode`这个API代替。
    - 可以通过监听`WM_SETTINGCHANGE`这个消息来获取系统是否已经切换了深色/浅色主题。

- 获取系统是否开启了“透明效果”

  设置路径：设置->个性化->颜色->透明效果->开/关

  ```text
  HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
  ```

  查看`EnableTransparency`的键值，若为`0`则代表透明已关闭，若为`1`则代表透明已开启。修改此键值则可修改系统的相关设置。
- 读写INI文件：<https://github.com/Chuyu-Team/CPPHelper/blob/master/IniHelper.h>
  - 读取

    ```cpp
    // 获取整型数值（区段名，键名，默认值，INI文件路径）
    UINT GetPrivateProfileIntW(LPCWSTR lpAppName, LPCWSTR lpKeyName, INT nDefault, LPCWSTR lpFileName);
    // 获取指定区段的所有子键和它们的值（区段名，字符串返回值的指针，字符串长度，INI文件路径）
    DWORD GetPrivateProfileSectionW(LPCWSTR lpAppName, LPWSTR lpReturnedString, DWORD nSize, LPCWSTR lpFileName);
    // 获取所有区段的名字（字符串返回值的指针，字符串长度，INI文件路径）
    DWORD GetPrivateProfileSectionNamesW(LPWSTR lpszReturnBuffer, DWORD nSize, LPCWSTR lpFileName);
    // 获取字符串（区段名，键名，默认值，字符串返回值的指针，字符串长度，INI文件路径）
    DWORD GetPrivateProfileStringW(LPCWSTR lpAppName, LPCWSTR lpKeyName, LPCWSTR lpDefault, LPWSTR lpReturnedString, DWORD nSize, LPCWSTR lpFileName);
    // 不太明白是干嘛的
    BOOL GetPrivateProfileStructW(LPCWSTR lpszSection, LPCWSTR lpszKey, LPVOID lpStruct, UINT uSizeStruct, LPCWSTR szFile);
    ```

  - 写入

    ```cpp
    // 替换指定区段的子键和它们的值（区段名，字符串，INI文件路径）
    BOOL WritePrivateProfileSectionW(LPCWSTR lpAppName, LPCWSTR lpString, LPCWSTR lpFileName);
    // 复制一个字符串到指定的区段中（区段名，键名，字符串，INI文件路径）
    BOOL WritePrivateProfileStringW(LPCWSTR lpAppName, LPCWSTR lpKeyName, LPCWSTR lpString, LPCWSTR lpFileName);
    // 不太明白是干嘛的
    BOOL WritePrivateProfileStructW(LPCWSTR lpszSection, LPCWSTR lpszKey, LPVOID lpStruct, UINT uSizeStruct, LPCWSTR szFile);
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
  [[nodiscard]] static inline bool operator==(const RECT &lhs, const RECT &rhs) noexcept
  {
      return ((lhs.left == rhs.left) && (lhs.top == rhs.top)
              && (lhs.right == rhs.right) && (lhs.bottom == rhs.bottom));
  }

  [[nodiscard]] static inline std::optional<MONITORINFOEXW> getMonitorForWindow(const HWND hwnd)
  {
      Q_ASSERT(hwnd);
      if (!hwnd) {
          return std::nullopt;
      }
      const HMONITOR monitor = MonitorFromWindow(hwnd, MONITOR_DEFAULTTONEAREST);
      if (!monitor) {
          WARNING << Utils::getSystemErrorMessage(kMonitorFromWindow);
          return std::nullopt;
      }
      MONITORINFOEXW monitorInfo;
      SecureZeroMemory(&monitorInfo, sizeof(monitorInfo));
      monitorInfo.cbSize = sizeof(monitorInfo);
      if (GetMonitorInfoW(monitor, &monitorInfo) == FALSE) {
          WARNING << Utils::getSystemErrorMessage(kGetMonitorInfoW);
          return std::nullopt;
      }
      return monitorInfo;
  };

  static inline void moveWindowToMonitor(const HWND hwnd, const MONITORINFOEXW &activeMonitor)
  {
      Q_ASSERT(hwnd);
      if (!hwnd) {
          return;
      }
      const std::optional<MONITORINFOEXW> currentMonitor = getMonitorForWindow(hwnd);
      if (!currentMonitor.has_value()) {
          WARNING << "Failed to retrieve the window's monitor.";
          return;
      }
      const RECT currentMonitorRect = currentMonitor.value().rcMonitor;
      const RECT activeMonitorRect = activeMonitor.rcMonitor;
      // We are in the same monitor, nothing to adjust here.
      if (currentMonitorRect == activeMonitorRect) {
          return;
      }
      RECT currentWindowRect = {};
      if (GetWindowRect(hwnd, &currentWindowRect) == FALSE) {
          WARNING << Utils::getSystemErrorMessage(kGetWindowRect);
          return;
      }
      const int currentWindowWidth = (currentWindowRect.right - currentWindowRect.left);
      const int currentWindowHeight = (currentWindowRect.bottom - currentWindowRect.top);
      const int currentWindowOffsetX = (currentWindowRect.left - currentMonitorRect.left);
      const int currentWindowOffsetY = (currentWindowRect.top - currentMonitorRect.top);
      const int newWindowX = (activeMonitorRect.left + currentWindowOffsetX);
      const int newWindowY = (activeMonitorRect.top + currentWindowOffsetY);
      static constexpr const UINT flags =
          (SWP_NOSIZE | SWP_NOZORDER | SWP_NOACTIVATE | SWP_NOOWNERZORDER);
      if (SetWindowPos(hwnd, nullptr, newWindowX, newWindowY,
             currentWindowWidth, currentWindowHeight, flags) == FALSE) {
          WARNING << Utils::getSystemErrorMessage(kSetWindowPos);
      }
  }

  void Utils::bringWindowToFront(const WId windowId)
  {
      Q_ASSERT(windowId);
      if (!windowId) {
          return;
      }
      const auto hwnd = reinterpret_cast<HWND>(windowId);
      const HWND oldForegroundWindow = GetForegroundWindow();
      if (!oldForegroundWindow) {
          // The foreground window can be NULL, it's not an API error.
          return;
      }
      const std::optional<MONITORINFOEXW> activeMonitor = getMonitorForWindow(oldForegroundWindow);
      if (!activeMonitor.has_value()) {
          WARNING << "Failed to retrieve the window's monitor.";
          return;
      }
      // We need to show the window first, otherwise we won't be able to bring it to front.
      if (IsWindowVisible(hwnd) == FALSE) {
          ShowWindow(hwnd, SW_SHOW);
      }
      if (IsMinimized(hwnd)) {
          // Restore the window if it is minimized.
          ShowWindow(hwnd, SW_RESTORE);
          // Once we've been restored, throw us on the active monitor.
          moveWindowToMonitor(hwnd, activeMonitor.value());
          // When the window is restored, it will always become the foreground window.
          // So return early here, we don't need the following code to bring it to front.
          return;
      }
      // OK, our window is not minimized, so now we will try to bring it to front manually.
      // First try to send a message to the current foreground window to check whether
      // it is currently hanging or not.
      static constexpr const UINT kTimeout = 1000;
      if (SendMessageTimeoutW(oldForegroundWindow, WM_NULL, 0, 0,
              SMTO_BLOCK | SMTO_ABORTIFHUNG | SMTO_NOTIMEOUTIFNOTHUNG, kTimeout, nullptr) == 0) {
          if (GetLastError() == ERROR_TIMEOUT) {
              WARNING << "The foreground window hangs, can't activate current window.";
          } else {
              WARNING << getSystemErrorMessage(kSendMessageTimeoutW);
          }
          return;
      }
      const DWORD windowThreadProcessId = GetWindowThreadProcessId(oldForegroundWindow, nullptr);
      const DWORD currentThreadId = GetCurrentThreadId();
      // We won't be able to change a window's Z order if it's not our own window,
      // so we use this small technique to pretend the foreground window is ours.
      if (AttachThreadInput(windowThreadProcessId, currentThreadId, TRUE) == FALSE) {
          WARNING << getSystemErrorMessage(kAttachThreadInput);
          return;
      }
      // And also don't forget to disconnect from it.
      volatile const auto cleanup = qScopeGuard([windowThreadProcessId, currentThreadId]() -> void {
          if (AttachThreadInput(windowThreadProcessId, currentThreadId, FALSE) == FALSE) {
              WARNING << getSystemErrorMessage(kAttachThreadInput);
          }
      });
      Q_UNUSED(cleanup);
      // Make our window be the first one in the Z order.
      if (BringWindowToTop(hwnd) == FALSE) {
          WARNING << getSystemErrorMessage(kBringWindowToTop);
          return;
      }
      // Activate the window too. This will force us to the virtual desktop this
      // window is on, if it's on another virtual desktop.
      if (SetActiveWindow(hwnd) == nullptr) {
          WARNING << getSystemErrorMessage(kSetActiveWindow);
          return;
      }
      // Throw us on the active monitor.
      moveWindowToMonitor(hwnd, activeMonitor.value());
  }
  ```

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

      QFile iconFile(iconPath);
      if (!iconFile.open(QFile::ReadOnly)) {
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
      const auto *ig = reinterpret_cast<LPICONDIR>(const_cast<char *>(temp.data()));

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

          if (!UpdateResourceW(updateRes, RT_ICON, MAKEINTRESOURCEW(i + 1), MAKELANGID(LANG_NEUTRAL, SUBLANG_NEUTRAL), const_cast<char *>(temp1), size1)) {
              qWarning() << "Cannot update icon" << i + 1;
          }
      }

      if (!UpdateResourceW(updateRes, RT_GROUP_ICON, MAKEINTRESOURCEW(1), MAKELANGID(LANG_NEUTRAL, SUBLANG_NEUTRAL), newDir, newSize)) {
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
                      pFixedInfo->dwFileVersionMS = DWORD((ver.majorVersion() << 48) | ((ver.minorVersion() & 0xFFFF) << 32));
                      pFixedInfo->dwFileVersionLS = DWORD(((ver.microVersion() & 0xFFFF) << 16) | (0 & 0xFFFF));
                  } else {
                      pFixedInfo->dwProductVersionMS = DWORD((ver.majorVersion() << 48) | ((ver.minorVersion() & 0xFFFF) << 32));
                      pFixedInfo->dwProductVersionLS = DWORD(((ver.microVersion() & 0xFFFF) << 16) | (0 & 0xFFFF));
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
  // 头文件：shlobj.h、intshcut.h
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

  bool createShortcut(
      const QString &fileName, const QString &linkName, const QString &workingDir,
      const QStringList &arguments, const QString &iconPath,
      const QString &iconId, const QString &description) {
      if (fileName.isEmpty() || linkName.isEmpty()) {
          qDebug() << "Target file name and shortcut file name cannot be empty.";
          return false;
      }
      const QFileInfo fileInfo(fileName);
      if ((fileInfo.isFile() || fileInfo.isDir()) && !fileInfo.exists()) {
          qDebug() << "Target file doesn't exist.";
          return false;
      }
      DeCoInitializer deCoInitializer;
      IUnknown *iunkn = nullptr;
      QString _linkName = linkName;
      if (fileName.startsWith(QStringLiteral("https:"), Qt::CaseInsensitive) ||
          fileName.startsWith(QStringLiteral("http:"), Qt::CaseInsensitive) ||
          fileName.startsWith(QStringLiteral("ftp:"), Qt::CaseInsensitive)) {
          IUniformResourceLocatorW *iurl = nullptr;
          if (FAILED(CoCreateInstance(CLSID_InternetShortcut, nullptr,
                                      CLSCTX_INPROC_SERVER,
                                      IID_PPV_ARGS(&iurl)))) {
              qDebug() << "Failed to initialize the operation.";
              return false;
          }
          if (FAILED(iurl->SetURL(
                  reinterpret_cast<const wchar_t *>(fileName.utf16()),
                  IURL_SETURL_FL_GUESS_PROTOCOL))) {
              iurl->Release();
              qDebug() << "Failed to set URL of the shortcut.";
              return false;
          }
          iunkn = iurl;
      } else {
          // The shortcut file name must ends with ".lnk" to let QFile::link()
          // work.
          static constexpr const char postfix[] = ".lnk";
          if (!_linkName.endsWith(QString::fromUtf8(postfix),
                                  Qt::CaseInsensitive)) {
              _linkName.append(QString::fromUtf8(postfix));
          }
          if (!QFile::link(fileName, _linkName)) {
              qDebug() << "Failed to create the shortcut.";
              return false;
          }
          const QString _workingDir =
              workingDir.isEmpty() ? fileInfo.absolutePath() : workingDir;
          IShellLinkW *psl = nullptr;
          if (FAILED(CoCreateInstance(CLSID_ShellLink, nullptr,
                                      CLSCTX_INPROC_SERVER, IID_PPV_ARGS(&psl)))) {
              qDebug() << "Failed to initialize the operation.";
              return false;
          }
          // TODO: implement this server side, since there's not Qt equivalent to
          // set working dir and arguments
          psl->SetPath(reinterpret_cast<const wchar_t *>(
              QDir::toNativeSeparators(fileName).utf16()));
          psl->SetWorkingDirectory(reinterpret_cast<const wchar_t *>(
              QDir::toNativeSeparators(_workingDir).utf16()));
          if (!arguments.isEmpty()) {
              psl->SetArguments(reinterpret_cast<const wchar_t *>(
                  arguments.join(QChar(u' ')).utf16()));
          }
          if (!iconPath.isEmpty()) {
              psl->SetIconLocation(
                  reinterpret_cast<const wchar_t *>(
                      QDir::toNativeSeparators(iconPath).utf16()),
                  iconId.toInt());
          }
          if (!description.isEmpty()) {
              psl->SetDescription(
                  reinterpret_cast<const wchar_t *>(description.utf16()));
          }
          iunkn = psl;
      }
      IPersistFile *ppf = nullptr;
      if (SUCCEEDED(iunkn->QueryInterface(IID_PPV_ARGS(&ppf)))) {
          ppf->Save(reinterpret_cast<const wchar_t *>(
                        QDir::toNativeSeparators(_linkName).utf16()),
                    true);
          ppf->Release();
      }
      iunkn->Release();
      // Force start menu cache update
      PIDLIST_ABSOLUTE pidl;
      if (SUCCEEDED(SHGetFolderLocation(0, CSIDL_STARTMENU, 0, 0, &pidl))) {
          SHChangeNotify(SHCNE_UPDATEDIR, SHCNF_IDLIST, pidl, 0);
          CoTaskMemFree(pidl);
      }
      if (SUCCEEDED(
              SHGetFolderLocation(0, CSIDL_COMMON_STARTMENU, 0, 0, &pidl))) {
          SHChangeNotify(SHCNE_UPDATEDIR, SHCNF_IDLIST, pidl, 0);
          CoTaskMemFree(pidl);
      }
      return true;
  }
  ```

- 同一个应用程序如何开启多个选项卡（Win7开始添加的任务栏选项卡，Aero Peek）：请参考<https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/Win7Samples/winui/shell/appshellintegration/TabThumbnails>
- 对于Win10系统，如何获取/修改显示主题色的区域

  设置路径：设置->个性化->颜色->在以下区域显示主题色->“开始”菜单、任务栏和操作中心 & 标题栏和窗口边框
  - “开始”菜单、任务栏和操作中心：

    ```text
    HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
    ```

    查看`ColorPrevalence`的键值，`0`代表显示系统默认颜色，`1`代表显示用户设置的主题色。读取此键值便可获取用户的设置，修改此键值便可修改用户的设置。

  - 标题栏和窗口边框：

    ```text
    HKEY_CURRENT_USER\Software\Microsoft\Windows\DWM
    ```

    查看`ColorPrevalence`的键值，`0`代表显示系统默认颜色，`1`代表显示用户设置的主题色。读取此键值便可获取用户的设置，修改此键值便可修改用户的设置。
  - 程序实现

    ```cpp
    enum class ColorizationSurfaces {
        None,
        StartTaskbarAndActionCenter,
        TitleBarsAndWindowBorders,
        All
    };

    static constexpr const char PERSONALIZATION_REGISTRY_KEY[] =
        R"(HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize)";
    static constexpr const char DWM_REGISTRY_KEY[] =
        R"(HKEY_CURRENT_USER\Software\Microsoft\Windows\DWM)";

    bool isWin10OrNewer() {
        return (QOperatingSystemVersion::current() >=
                QOperatingSystemVersion::Windows10);
    }

    // 获取用户设置的主题色显示区域
    ColorizationSurfaces colorizationSurfaces() {
        if (!isWin10OrNewer()) {
            qDebug(
                "Querying colorization surfaces is meaningless below Windows 10.");
            return ColorizationSurfaces::None;
        }
        bool ok = false;
        const QSettings themeRegistry(
            QString::fromUtf8(PERSONALIZATION_REGISTRY_KEY),
            QSettings::NativeFormat);
        const bool themeSurfaces =
            themeRegistry.value(QStringLiteral("ColorPrevalence"), 0)
                .toULongLong(&ok) != 0;
        if (ok) {
            ok = false;
        } else {
            qDebug("Failed to query colorization surfaces from theme settings.");
        }
        const QSettings dwmRegistry(QString::fromUtf8(DWM_REGISTRY_KEY),
                                    QSettings::NativeFormat);
        const bool dwmSurfaces =
            dwmRegistry.value(QStringLiteral("ColorPrevalence"), 0)
                .toULongLong(&ok) != 0;
        if (!ok) {
            qDebug("Failed to query colorization surfaces from DWM settings.");
        }
        ColorizationSurfaces surfaces = ColorizationSurfaces::None;
        if (themeSurfaces && dwmSurfaces) {
            surfaces = ColorizationSurfaces::All;
        } else if (themeSurfaces) {
            surfaces = ColorizationSurfaces::StartTaskbarAndActionCenter;
        } else if (dwmSurfaces) {
            surfaces = ColorizationSurfaces::TitleBarsAndWindowBorders;
        }
        return surfaces;
    }

    // 设置
    bool setColorizationSurfaces(ColorizationSurfaces surfaces) {
        if (!isWin10OrNewer()) {
            qDebug(
                "Setting colorization surfaces is only available on Windows 10.");
            return false;
        }
        QSettings themeRegistry(QString::fromUtf8(PERSONALIZATION_REGISTRY_KEY),
                                QSettings::NativeFormat);
        if (!isDarkModeEnabled() &&
            ((surfaces == ColorizationSurfaces::StartTaskbarAndActionCenter) ||
             (surfaces == ColorizationSurfaces::All))) {
            qDebug("Cannot apply theme color to start, taskbar and action center "
                   "when system is in light mode.");
            return false;
        }
        QSettings dwmRegistry(QString::fromUtf8(DWM_REGISTRY_KEY),
                              QSettings::NativeFormat);
        switch (surfaces) {
        case ColorizationSurfaces::None:
            themeRegistry.setValue(QStringLiteral("ColorPrevalence"), 0);
            dwmRegistry.setValue(QStringLiteral("ColorPrevalence"), 0);
            break;
        case ColorizationSurfaces::StartTaskbarAndActionCenter:
            themeRegistry.setValue(QStringLiteral("ColorPrevalence"), 1);
            dwmRegistry.setValue(QStringLiteral("ColorPrevalence"), 0);
            break;
        case ColorizationSurfaces::TitleBarsAndWindowBorders:
            themeRegistry.setValue(QStringLiteral("ColorPrevalence"), 0);
            dwmRegistry.setValue(QStringLiteral("ColorPrevalence"), 1);
            break;
        case ColorizationSurfaces::All:
            themeRegistry.setValue(QStringLiteral("ColorPrevalence"), 1);
            dwmRegistry.setValue(QStringLiteral("ColorPrevalence"), 1);
            break;
        }
        return true;
    }
    ```

- 如何人为的弹出Win10设置窗口并自动跳转到我们想要打开的页面

  Win10引入的UWP版设置程序（就是新版的控制面板），可以通过`ms-settings:`这个协议直接打开。以下是常见的URI及其对应的设置页面。

  | 设置 | URI |
  | --- | --- |
  | 节电模式 | ms-settings:batterysaver |
  | 电池使用 | ms-settings:batterysaver-settings |
  | 鼠标 | ms-settings:batterysaver-usagedetails |
  | 蓝牙 | ms-settings:bluetooth |
  | 颜色 | ms-settings:colors |
  | 数据使用量 | ms-settings:datausage |
  | 日期和时间 | ms-settings:dateandtime |
  | 隐藏字幕 | ms-settings:easeofaccess-closedcaptioning |
  | 高对比度 | ms-settings:easeofaccess-highcontrast |
  | 放大镜 | ms-settings:easeofaccess-magnifier |
  | 讲述人 | ms-settings:easeofaccess-narrator |
  | 键盘 | ms-settings:easeofaccess-keyboard |
  | 鼠标 | ms-settings:easeofaccess-mouse |
  | 锁屏 | ms-settings:lockscreen |
  | 离线地图 | ms-settings:maps |
  | 飞行模式 | ms-settings:network-airplanemode |
  | 代理 | ms-settings:network-proxy |
  | VPN | ms-settings:network-vpn |
  | 通知和操作 | ms-settings:notifications |
  | 账户信息 | ms-settings:privacy-accountinfo |
  | 日历 | ms-settings:privacy-calendar |
  | 联系人 | ms-settings:privacy-contacts |
  | 其他设备 | ms-settings:privacy-customdevices |
  | 反馈 | ms-settings:privacy-feedback |
  | 位置 | ms-settings:privacy-location |
  | 消息 | ms-settings:privacy-messaging |
  | 麦克风 | ms-settings:privacy-microphone |
  | 运动数据 | ms-settings:privacy-motion |
  | 无线电收发器 | ms-settings:privacy-radios |
  | 语音、墨迹书写和键入 | ms-settings:privacy-speechtyping |
  | 摄像头 | ms-settings:privacy-webcam |
  | 区域和语言 | ms-settings:regionlanguage |
  | 语音 | ms-settings:speech |
  | Windows更新 | ms-settings:windowsupdate |
  | 连接工作或学校账户 | ms-settings:workplace |
  | 已连接设备 | ms-settings:connecteddevices |
  | 开发人员 | ms-settings:developers |
  | 显示 | ms-settings:display |
  | 鼠标和触摸板 | ms-settings:mousetouchpad |
  | 拨号 | ms-settings:network-dialup |
  | 以太网 | ms-settings:network-ethernet |
  | 移动热点 | ms-settings:network-mobilehotspot |
  | Wi-Fi | ms-settings:network-wifi |
  | WiFi管理 | ms-settings:network-wifisettings |
  | 可选功能 | ms-settings:optionalfeatures |
  | 其他账户 | ms-settings:otherusers |
  | 个性化 | ms-settings:personalization |
  | 背景 | ms-settings:personalization-background |
  | 颜色 | ms-settings:personalization-colors |
  | 开始 | ms-settings:personalization-start |
  | 电源和睡眠 | ms-settings:powersleep |
  | 无线设备 | ms-settings:proximity |
  | 显示 | ms-settings:screenrotation |
  | 登录选项 | ms-settings:signinoptions |
  | 存储感知 | ms-settings:storagesense |
  | 主题 | ms-settings:themes |
  | 输入 | ms-settings:typing |
  | 平板模式 | ms-settings://tabletmode/ |
  | 隐私 | ms-settings:privacy |
  | 默认应用 | ms-settings:defaultapps |

  注意事项：
  - 把URI直接当一个文件路径打开就行，例如按下*Win+R*打开”运行“窗口，输入URI，点击”确定“或直接回车，即可打开对应的设置界面。当然也可以通过Win32 API打开：

    ```cpp
    ShellExecuteW(nullptr, nullptr, L"ms-settings:defaultapps", nullptr, nullptr, SW_SHOW);
    ```

  - URI的一般构成方式是`ms-settings:大分类英文名-小分类英文名`（分类名如果是多个单词，直接连在一起，不要加空格或符号）
  - 以上URI命名规律是一般规律，有一小部分设置项并不遵守这个规律，大概是历史原因吧
  - 随着Win10的不断地迭代升级，有些已有的URI会失效，也会有新增的URI，上面那个名单不是一直不变的，参考性的看看就行了，不要当成指导手册
- 关联文件+为不同的后缀名设置不同的图标+注册成为默认程序
  1. 注册`.mp3`后缀（注册任何后缀名都是一样的套路，直接套下面这个模板就可以）。

      ```text
      HKEY_LOCAL_MACHINE
         SOFTWARE
            Classes
               // 此注册表项的名字只要符合ProgID的命名规则就可以了，可以随便起名，没有什么固定的格式。主要注意不要和别人的重名。
               wangwenx190.MyApp.MP3.1
                  // 下面这个是此类型文件的描述，除了可以像下面这样使用DLL中的资源，还可以直接把文字写入到此处
                  (Default) = "%ProgramFiles%\wangwenx190\MyApp\data.dll",12
                  // 下面这个是对用户更加友好的描述，可以与上面那个完全相同，也可以使用更加详细的描述。这个子键不是必需的，可以没有。
                  FriendlyTypeName = "%ProgramFiles%\wangwenx190\MyApp\data.dll",12
                  // MIME专用，不懂就去掉这一项，影响不大
                  CLSID
                     (Default) = {D92B76F4-CFA0-4b93-866B-7730FEB4CD7B}
                  // 默认图标
                  DefaultIcon
                     (Default) = "%ProgramFiles%\wangwenx190\MyApp\icons.dll",12
                  // 支持的命令
                  Shell
                     // 打开
                     Open
                        Command
                           // “%n”代表第n个参数
                           (Default) = "%ProgramFiles%\wangwenx190\MyApp\app.exe" "%1"
      ```

      ```text
      HKEY_CLASSES_ROOT
         .mp3
            OpenWithProgIDs
               // 一定要与上面那个注册表项的名字对应起来
               // 只需要写入键名，键值为空，键值类型为REG_NONE
               wangwenx190.MyApp.MP3.1 = null
      ```

      注意事项：
      - `ProgID`的命名规则为`公司名.产品名[.子产品名.版本号]`，前两项必填，后两项选填
      - 路径一定要用英文半角双引号括起来，不管路径中有没有空格
      - 尽量不要把路径写死，能用相对路径就用相对路径，能用环境变量就用环境变量
      - 路径中尽量用`\`而不是`/`
      - 涉及到显示给用户的文字，尽量使用嵌入DLL的资源而不是直接往注册表中写字符串，以便实现国际化（否则注册表里写的是什么，系统就显示什么，无法针对用户设置的语言和地区进行切换）
      - `(Default)`代表的是此注册表项（注意不是子键）的默认值
      - `=`前面是键名，后面是键值
      - `CLSID`这一项是针对`MIME`类型的，其值是系统提供的不是自己编的，不支持或不熟悉可以没有这一项
  2. 将程序注册到*默认程序*列表（只有这样，你的程序才能在系统的“默认程序”的列表中出现）

      ```text
      HKEY_LOCAL_MACHINE
         SOFTWARE
            wangwenx190
               MyApp
                  Capabilities
                     // 程序描述
                     ApplicationDescription = This is a test application.
                     // 程序名字
                     ApplicationName = My Application
                     // 支持的文件后缀
                     FileAssociations
                        .mp3 = wangwenx190.MyApp.MP3.1
                        .mpeg = wangwenx190.MyApp.MPG.1
                     // 支持的MIME类型，可选，可以没有
                     MimeAssociations
                        audio/mp3 = wangwenx190.MyApp.MP3.1
                        audio/mpeg = wangwenx190.MyApp.MPG.1
      ```

      注意事项：
      - `ApplicationDescription`此键必须要有且需要好好填写，否则系统会忽略你的程序
      - `ApplicationName`是系统在“默认程序”窗口显示的名字，不是什么内部的产品名，一定要好好填。不填的话系统会用程序的文件名代替。
      - `FileAssociations`下的各个子键就是你的程序所支持的**所有**后缀名，子键的键名就是以英文半角句点开头的完整后缀名，键值就是在上一步创建的那个注册表项的名字，注意**名字一定要一一对应**，字母的大小写也不能有错
      - `MimeAssociations`下的各个子键是你的程序所支持的所有的MIME类型，不熟悉的可以跳过此部分，非必需。注意，只有填写了`CLSID`的注册表项才支持填在`MimeAssociations`下面
  3. 注册成为默认程序

      ```text
      HKEY_LOCAL_MACHINE
         SOFTWARE
            RegisteredApplications
               My Application = SOFTWARE\wangwenx190\MyApp\Capabilities
      ```

      注意事项：`RegisteredApplications`下的各个子键就是系统的默认程序，将你的程序写入到此项的子键即可使你的程序也成为系统的默认程序之一。子键的键名为程序的名字（这里写什么都无所谓，因为系统会从你在上一步的设置里读取具体的名字，但还是照实填写比较好），键值为你在上一步创建的注册表项`Capabilities`的路径。
  4. 通知系统文件扩展名已经进行了修改，使系统立即进行一次刷新

      ```cpp
      // 头文件：Shlobj.h
      // 库文件：Shell32.lib（Shell32.dll）
      SHChangeNotify(SHCNE_ASSOCCHANGED, SHCNF_IDLIST | SHCNF_FLUSH, nullptr, nullptr);
      ```

  5. 在Win7及更新的系统上打开系统设置窗口，提示用户设置默认程序

      ```cpp
      // 头文件：shobjidl.h, atlbase.h
      CComPtr<IApplicationAssociationRegistrationUI> pAARUI;
      if (SUCCEEDED(CoCreateInstance(CLSID_ApplicationAssociationRegistrationUI, nullptr, CLSCTX_INPROC, IID_PPV_ARGS(&pAARUI)))) {
          // 下面用到的这个程序名一定要与“HKLM\SOFTWARE\RegisteredApplications”下的名字相对应
          pAARUI->LaunchAdvancedAssociationUI(L"My Application");
      }
      ```

      注意：从Win8开始，系统就不允许程序自身强行修改系统的默认程序了，只能将程序支持打开的文件类型通过以上步骤注册到系统中，然后打开系统的设置窗口，让用户自行选择默认程序。如果通过强行修改注册表的方式修改系统默认打开方式，会导致被修改的文件类型被系统强制还原为Windows默认打开方式，得不偿失。
  6. 检查程序是否已经被用户设置为默认程序

      ```cpp
      CComPtr<IApplicationAssociationRegistration> m_pAAR;
      if (FAILED(CoCreateInstance(CLSID_ApplicationAssociationRegistration, nullptr, CLSCTX_INPROC, IID_PPV_ARGS(&m_pAAR)))) {
          return false;
      }
      BOOL bIsDefault = FALSE;
      if (m_pAAR) {
          // 下面用到的这个程序名一定要与“HKLM\SOFTWARE\RegisteredApplications”下的名字相对应
          m_pAAR->QueryAppIsDefault(L".mp3", AT_FILEEXTENSION, AL_EFFECTIVE, L"My Application", &bIsDefault);
      }
      // 现在“bIsDefault”存放的就是针对某个特定的扩展名，我们的程序是不是默认打开程序
      // 如果要检查所有扩展名的设置状况，只能使用循环，逐一判断，没法一次性全部判断
      ```

      注意：此方式仅Windows Vista/7支持，Windows XP只能通过手动检查注册表的方式确认，Win8及更新的系统不支持此类检查。
  7. 程序实现

      ```cpp
      // Only extensionName can't be empty. CLSID is used when you want to register
      // MIME types.
      struct FileAssocId {
          QString extensionName;
          QString description;
          QString friendlyTypeName;
          QString mimeType;
          QString CLSID;
          QString defaultIcon;
          QString operation;
          QString command;
      };
      using FileAssocIdList = QList<FileAssocId>;

      bool registerFileType(const QString &applicationPath,
                                             const QString &applicationDisplayName,
                                             const QString &applicationDescription,
                                             const QString &companyName,
                                             const FileAssocIdList &assocIdList) {
          if (applicationPath.isEmpty() || applicationDescription.isEmpty() ||
              companyName.isEmpty()) {
              qDebug() << "Application path, application description and company "
                          "name cannot be empty.";
              return false;
          }
          if (!isAdminProcess()) {
              qDebug() << "Cannot register file types without admin privileges.";
              return false;
          }
          if (assocIdList.isEmpty()) {
              qDebug() << "Empty file type list.";
              return false;
          }
          const QFileInfo fileInfo(applicationPath);
          if (!fileInfo.exists()) {
              qDebug() << "The given application doesn't exist.";
              return false;
          }
          if (!fileInfo.isExecutable()) {
              qDebug() << "The given path is not an executable.";
              return false;
          }
          QString exePath = QDir::toNativeSeparators(fileInfo.canonicalFilePath());
          if (!exePath.startsWith(QChar(u'"'))) {
              exePath.prepend(QChar(u'"'));
          }
          if (!exePath.endsWith(QChar(u'"'))) {
              exePath.append(QChar(u'"'));
          }
          const QString displayName = applicationDisplayName.isEmpty()
              ? fileInfo.completeBaseName()
              : applicationDisplayName;
          QString progid_com = companyName;
          progid_com.remove(QChar(u' '));
          QString progid_app = displayName;
          progid_app.remove(QChar(u' '));
          const QString regKey3 =
                  QStringLiteral(
                      R"(HKEY_LOCAL_MACHINE\SOFTWARE\%1\%2\Capabilities)")
                      .arg(progid_com, progid_app);
          QSettings settings3(regKey3, QSettings::NativeFormat);
          // ApplicationDescription MUST NOT BE EMPTY!!!
          settings3.setValue(QStringLiteral("ApplicationDescription"),
                             applicationDescription);
          settings3.setValue(QStringLiteral("ApplicationName"), displayName);
          const QString regKey4 = QStringLiteral(
                  R"(HKEY_LOCAL_MACHINE\SOFTWARE\RegisteredApplications)");
          QSettings settings4(regKey4, QSettings::NativeFormat);
          settings4.setValue(displayName,
                             QStringLiteral(R"(SOFTWARE\%1\%2\Capabilities)")
                                 .arg(progid_com, progid_app));
          for (auto &&assocId : std::as_const(assocIdList)) {
              if (assocId.extensionName.isEmpty()) {
                  qDebug() << "Empty extension name. Skipping.";
                  continue;
              }
              QString ext = assocId.extensionName.toLower();
              QString progid_ext = ext.toUpper();
              if (ext.startsWith(QChar(u'.'))) {
                  progid_ext.remove(0, 1);
              } else {
                  ext.prepend(QChar(u'.'));
              }
              const QString ProgID = QStringLiteral("%1.%2.%3.1000")
                                         .arg(progid_com, progid_app, progid_ext);
              const QString regKey1 =
                  QStringLiteral(R"(HKEY_LOCAL_MACHINE\SOFTWARE\Classes\%1)")
                      .arg(ProgID);
              QSettings settings1(regKey1, QSettings::NativeFormat);
              if (!assocId.description.isEmpty()) {
                  settings1.setValue(QStringLiteral(""), assocId.description);
              }
              if (!assocId.friendlyTypeName.isEmpty()) {
                  settings1.setValue(QStringLiteral("FriendlyTypeName"),
                                     assocId.friendlyTypeName);
              }
              if (!assocId.CLSID.isEmpty()) {
                  QSettings settings1_clsid(
                      QStringLiteral(R"(%1\CLSID)").arg(regKey1),
                      QSettings::NativeFormat);
                  settings1_clsid.setValue(QStringLiteral(""),
                                           assocId.CLSID.toUpper());
              }
              if (!assocId.defaultIcon.isEmpty()) {
                  settings1.setValue(QStringLiteral("DefaultIcon"),
                                     QDir::toNativeSeparators(assocId.defaultIcon));
              }
              if (!assocId.operation.isEmpty()) {
                  QSettings settings1_command(
                      QStringLiteral(R"(%1\Shell\%2\Command)")
                          .arg(regKey1, assocId.operation),
                      QSettings::NativeFormat);
                  const QString cmd = assocId.command.isEmpty()
                      ? (exePath + QStringLiteral(R"( "%1")"))
                      : QDir::toNativeSeparators(assocId.command);
                  settings1_command.setValue(QStringLiteral(""), cmd);
              }
              const QString regKey2 =
                  QStringLiteral(R"(HKEY_CLASSES_ROOT\%1\OpenWithProgIDs)")
                      .arg(ext);
              QSettings settings2(regKey2, QSettings::NativeFormat);
              settings2.setValue(ProgID, QStringLiteral(""));
              QSettings settings3_fileAssociations(
                  QStringLiteral(R"(%1\FileAssociations)").arg(regKey3),
                  QSettings::NativeFormat);
              settings3_fileAssociations.setValue(ext, ProgID);
              if (!assocId.mimeType.isEmpty()) {
                  QSettings settings3_mimeAssociations(
                      QStringLiteral(R"(%1\MimeAssociations)").arg(regKey3),
                      QSettings::NativeFormat);
                  settings3_mimeAssociations.setValue(assocId.mimeType.toLower(),
                                                      ProgID);
              }
          }
          // Tell the system to flush itself immediately.
          SHChangeNotify(SHCNE_ASSOCCHANGED, SHCNF_IDLIST | SHCNF_FLUSH, nullptr,
                         nullptr);
          if (isWin10OrNewer()) {
              ShellExecuteW(nullptr, nullptr, L"ms-settings:defaultapps", nullptr,
                      nullptr, SW_SHOW);
          } else if (isWin7OrNewer()) {
              // Let the user choose the default application.
              CComPtr<IApplicationAssociationRegistrationUI> pAARUI;
              if (SUCCEEDED(CoCreateInstance(CLSID_ApplicationAssociationRegistrationUI,
                                      nullptr, CLSCTX_INPROC, IID_PPV_ARGS(&pAARUI)))) {
                  pAARUI->LaunchAdvancedAssociationUI(
                      reinterpret_cast<const wchar_t *>(displayName.utf16()));
              } else {
                  qDebug() << "Failed to launch advanced association UI.";
                  // No need to return false because we have registered all file
                  // types successfully.
              }
          }
          return true;
      }
      ```

  参考资料：<https://docs.microsoft.com/en-us/windows/win32/shell/default-programs>，<https://docs.microsoft.com/en-us/windows/win32/shell/fa-best-practices>，<https://github.com/microsoft/Windows-classic-samples/blob/master/Samples/Win7Samples/winui/shell/appshellintegration/AutomaticJumpList/FileRegistrations.h>
- 如何将图标文件或本地化的字符串存放到一个单独的DLL中，供外部程序使用
  - 头文件`resources.h`

    ```cpp
    // 图标资源
    // 序号是大于或等于0的整数，不允许重复，最好从小到大顺序书写，最好也不要间隔。
    // 这里所写的序号，就是以后你调用时所用的序号：“res.dll,5”
    #define IDI_OTHER_ICON   0
    #define IDI_AAC_ICON     1
    #define IDI_AC3_ICON     2
    #define IDI_AIFF_ICON    3
    #define IDI_ALAC_ICON    4
    #define IDI_AMR_ICON     5
    // 字符串资源
    // 注意不要与前面的序号重复
    #define IDS_WARNING_STRING     100
    #define IDS_ERROR_STRING       101
    #define IDS_QUESTION_STRING    102
    #define IDS_INFORMATION_STRING 103
    #define IDS_TEST_STRING        104
    #define IDS_ABOUT_STRING       105
    ```

  - 资源文件`resources.rc`

    ```text
    #include "resources.h"
    // 这个头文件是必需的
    #include <windows.h>
    // 需要版本信息的话可以写在这里，不会影响下面的东西
    // 图标资源（相对路径和绝对路径都可以）
    IDI_OTHER_ICON ICON "icons\\other.ico"
    IDI_AAC_ICON   ICON "icons\\aac.ico"
    IDI_AC3_ICON   ICON "icons\\ac3.ico"
    IDI_AIFF_ICON  ICON "icons\\aiff.ico"
    IDI_ALAC_ICON  ICON "icons\\alac.ico"
    IDI_AMR_ICON   ICON "icons\\amr.ico"
    // 字符串资源
    // 中文（简体，中国）
    LANGUAGE LANG_CHINESE, SUBLANG_CHINESE_SIMPLIFIED
    #pragma code_page(936)
    STRINGTABLE
    BEGIN
        IDS_WARNING_STRING     "警告"
        IDS_ERROR_STRING       "错误"
        IDS_QUESTION_STRING    "问题"
        IDS_INFORMATION_STRING "信息"
        IDS_TEST_STRING        "测试"
        IDS_ABOUT_STRING       "关于"
    END
    // 中文（繁体，台湾）
    LANGUAGE LANG_CHINESE, SUBLANG_CHINESE_TRADITIONAL
    #pragma code_page(950)
    STRINGTABLE
    BEGIN
        IDS_WARNING_STRING     "警告"
        IDS_ERROR_STRING       "错误"
        IDS_QUESTION_STRING    "问题"
        IDS_INFORMATION_STRING "信息"
        IDS_TEST_STRING        "测试"
        IDS_ABOUT_STRING       "关于"
    END
    // 英文（美国）
    LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
    #pragma code_page(1252)
    STRINGTABLE
    BEGIN
        IDS_WARNING_STRING     "warning"
        IDS_ERROR_STRING       "error"
        IDS_QUESTION_STRING    "question"
        IDS_INFORMATION_STRING "information"
        IDS_TEST_STRING        "test"
        IDS_ABOUT_STRING       "about"
    END
    ```

    存放字符串时，可以使用`L""`的形式存放Unicode字符串，例如：`IDS_RUSSIANSTRING L"\x0421\x043f\x0440\x0430\x0432\x043a\x0430"`。

  - 链接器添加`/NOENTRY`参数（属性->链接器->高级->无入口点->是），编译DLL

  参考资料：<https://docs.microsoft.com/en-us/windows/win32/menurc/stringtable-resource>

- 打开文件管理器（explorer.exe），并在其中定位某个文件或文件夹

  ```cpp
  bool showContainingFolder(const QString &path) {
      if (path.isEmpty()) {
          qDebug() << "Cannot locate the given file: empty path.";
          return false;
      }
      const QFileInfo fileInfo(path);
      if (!fileInfo.exists()) {
          qDebug() << "Cannot locate a file which do not exist.";
          return false;
      }
      // It's OK if we are locating a shortcut file, no need to check whether it's
      // a broken shortcut or not.
      // Don't use canonicalFilePath() here because we want to locate the file
      // itself, not the target file of the shortcut.
      QStringList params = {
          QDir::toNativeSeparators(fileInfo.absoluteFilePath())};
      if (!fileInfo.isDir()) {
          params.prepend(QStringLiteral("/select,"));
      }
      return QProcess::startDetached(QStringLiteral("explorer.exe"), params);
  }
  ```

  或使用官方API`SHOpenFolderAndSelectItems`。

- 打开命令提示符窗口并将工作路径切换到指定位置

  ```cpp
  bool openTerminal(const QString &workingDir) {
      STARTUPINFOW si;
      SecureZeroMemory(&si, sizeof(si));
      si.cb = sizeof(si);
      PROCESS_INFORMATION pinfo;
      SecureZeroMemory(&pinfo, sizeof(pinfo));
      const QString ComSpec = qEnvironmentVariable(
          "ComSpec", QStringLiteral(R"(%SystemRoot%\System32\cmd.exe)"));
      // lpCommandLine is assumed to be detached. See:
      // https://devblogs.microsoft.com/oldnewthing/?p=18083
      BOOL result =
          CreateProcessW(nullptr,
                         reinterpret_cast<wchar_t *>(const_cast<ushort *>(
                             QDir::toNativeSeparators(ComSpec).utf16())),
                         nullptr, nullptr, FALSE,
                         CREATE_NEW_CONSOLE | CREATE_UNICODE_ENVIRONMENT, nullptr,
                         workingDir.isEmpty()
                             ? nullptr
                             : reinterpret_cast<const wchar_t *>(
                                   QDir::toNativeSeparators(workingDir).utf16()),
                         &si, &pinfo);
      if (result) {
          CloseHandle(pinfo.hThread);
          CloseHandle(pinfo.hProcess);
      } else {
          qDebug() << "Failed to launch cmd.exe";
      }
      return result;
  }
  ```

- 如何获取用户设置的桌面壁纸的文件路径以及设置系统的桌面壁纸
  - 获取

    ```cpp
    QString wallpaper() {
        wchar_t path[MAX_PATH] = {};
        if (!SystemParametersInfoW(SPI_GETDESKWALLPAPER, MAX_PATH, path, 0)) {
            qDebug() << "Failed to query wallpaper path.";
            return {};
        }
        return QString::fromWCharArray(path);
    }
    ```

  - 设置

    ```cpp
    bool setWallpaper(const QString &path) {
        if (path.isEmpty()) {
            qDebug() << "Failed to set wallpaper: empty path.";
            return false;
        }
        const QFileInfo fileInfo(path);
        if (!fileInfo.exists()) {
            qDebug() << "Failed to set wallpaper: file doesn't exist.";
            return false;
       }
        if (!fileInfo.isFile() || fileInfo.isShortcut()) {
            qDebug() << "Failed to set wallpaper: file type mismatch.";
            return false;
        }
        const auto *_path = reinterpret_cast<const wchar_t *>(
            QDir::toNativeSeparators(path).utf16());
        if (!SystemParametersInfoW(SPI_SETDESKWALLPAPER, path.length(),
                                   const_cast<wchar_t *>(_path),
                                   SPIF_UPDATEINIFILE | SPIF_SENDCHANGE)) {
            qDebug() << "Failed to set wallpaper.";
            return false;
        }
        return true;
    }
    ```

- 获取用户是否开启了高对比度模式以及开启/关闭高对比度模式
  - 获取

    ```cpp
    bool isHighContrastModeEnabled() {
        HIGHCONTRASTW hc;
        SecureZeroMemory(&hc, sizeof(hc));
        hc.cbSize = sizeof(hc);
        if (!SystemParametersInfoW(SPI_GETHIGHCONTRAST, sizeof(hc), &hc, 0)) {
            qDebug() << "Failed to query high contrast mode state.";
            return false;
        }
        return (hc.dwFlags & HCF_HIGHCONTRASTON);
    }
    ```

  - 设置

    ```cpp
    bool setHighContrastModeEnabled(bool enable) {
        HIGHCONTRASTW hc;
        SecureZeroMemory(&hc, sizeof(hc));
        hc.cbSize = sizeof(hc);
        if (enable) {
            hc.dwFlags = HCF_HIGHCONTRASTON;
        }
        // FIXME: What is the default scheme?
        // hc.lpszDefaultScheme = L"aaa";
        if (!SystemParametersInfoW(SPI_SETHIGHCONTRAST, sizeof(hc), &hc,
                                   SPIF_UPDATEINIFILE | SPIF_SENDCHANGE)) {
            qDebug() << "Failed to change High Contrast Mode's state.";
            return false;
        }
        return true;
    }
    ```

- 获取用户设置的主题色
  - 方法1：Win32 API `DwmGetColorizationColor()`

    ```cpp
    QColor colorizationColor() {
        DWORD color = 0;
        BOOL dummy = FALSE;
        if (FAILED(DwmGetColorizationColor(&color, &dummy))) {
            qDebug() << "Failed to query DWM colorization color.";
            return QColorConstants::White;
        }
        return QColor::fromRgba(color);
    }
    ```

  - 方法2：读取注册表

    ```cpp
    QColor QtWin::realColorizationColor() {
        bool ok = false;
        const QSettings registry(R"(HKEY_CURRENT_USER\Software\Microsoft\Windows\DWM)", QSettings::NativeFormat);
        const quint64 value = registry.value("ColorizationColor", 0).toULongLong(&ok);
        if (!ok) {
            qDebug("Failed to read colorization color.");
            return QColorConstants::White;
        }
        return QColor::fromRgba(value);
    }
    ```

  区别及注意事项：以上两个方法都是用来获取用户设置的主题色的，区别在于在某些情况下两者获取到的颜色会有所不同，`DwmGetColorizationColor()`可能会返回一个半透明的灰色，具体原因未知，因此最保险的就是读注册表。
- 如何查看一个exe或dll是32位的还是64位的以及有没有启用*Control Flow Guard*

  ```bat
  dumpbin.exe /headers /loadconfig avcodec-58.dll
  ```

  dll的导入库（.lib文件）也可以用这个方法查看
- 判断系统版本：引入`VersionHelpers.h`（来自Windows SDK）后你会发现惊喜
- Windows版本号收录

  | 版本号 | 公开名称 | 开发代号 |
  | --- | --- | --- |
  | 5.0.2195 | Windows 2000 | - |
  | 5.1.2600 | Windows XP | - |
  | 5.2.3790 | Windows XP x64 Edition or Windows Server 2003 | - |
  | 6.0.6000 | Windows Vista | - |
  | 6.0.6001 | Windows Vista with Service Pack 1 or Windows Server 2008 | - |
  | 6.0.6002 | Windows Vista with Service Pack 2 | - |
  | 6.1.7600 | Windows 7 or Windows Server 2008 R2 | - |
  | 6.1.7601 | Windows 7 with Service Pack 1 or Windows Server 2008 R2 with Service Pack 1 | - |
  | 6.2.9200 | Windows 8 or Windows Server 2012 | - |
  | 6.3.9200 | Windows 8.1 or Windows Server 2012 R2 | - |
  | 6.3.9600 | Windows 8.1 with Update 1 | - |
  | 10.0.10240 | Windows 10 Version 1507 | TH1 |
  | 10.0.10586 | Windows 10 Version 1511 (November Update) | TH2 |
  | 10.0.14393 | Windows 10 Version 1607 (Anniversary Update) or Windows Server 2016 | RS1 |
  | 10.0.15063 | Windows 10 Version 1703 (Creators Update) | RS2 |
  | 10.0.16299 | Windows 10 Version 1709 (Fall Creators Update) | RS3 |
  | 10.0.17134 | Windows 10 Version 1803 (April 2018 Update) | RS4 |
  | 10.0.17763 | Windows 10 Version 1809 (October 2018 Update) or Windows Server 2019 | RS5 |
  | 10.0.18362 | Windows 10 Version 1903 (May 2019 Update) | 19H1 |
  | 10.0.18363 | Windows 10 Version 1909 (November 2019 Update) | 19H2 |
  | 10.0.19041 | Windows 10 Version 2004 (May 2020 Update) | 20H1 |
  | 10.0.19042 | Windows 10 Version 20H2 (October 2020 Update) | 20H2 |
  | 10.0.19043 | Windows 10 Version 21H1 (May 2021 Update) | 21H1 |
  | 10.0.19044 | Windows 10 Version 21H2 (November 2021 Update) | 21H2 |
  | 10.0.19045 | Windows 10 Version 22H2 (October 2022 Update) | 22H2 |
  | 10.0.22000 | Windows 11 Version 21H2 | 21H2 |
  | 10.0.22621 | Windows 11 Version 22H2 (October 2022 Update) | 22H2 |

- 使用Win32 API获取系统常见文件夹的路径

  使用`SHGetKnownFolderPath`即可。用法示例：

  ```cpp
  LPWSTR path = nullptr;
  if (SUCCEEDED(SHGetKnownFolderPath(FOLDERID_Windows, KF_FLAG_DEFAULT, nullptr, &path))) {
      std::wcout << path << std::endl;
      CoTaskMemFree(path); // 记得释放内存！
  } else {
      // 获取失败
  }
  ```

  更多`KNOWNFOLDERID`：
  | KNOWNFOLDERID | 对应的文件夹 | 默认路径 |
  | ------------- | ----------- | ------- |
  | FOLDERID_Windows | Windows文件夹 | `%windir%` |
  | FOLDERID_System | 系统文件夹 | `%windir%\system32` |
  | FOLDERID_SystemX86 | 系统文件夹（x86） | `%windir%\system32` 或 `%windir%\syswow64` |
  | FOLDERID_ProgramFiles | 程序文件夹 | `%ProgramFiles%` (`%SystemDrive%\Program Files`) |
  | FOLDERID_ProgramFilesX86 | 程序文件夹（x86） | `%ProgramFiles%` (`%SystemDrive%\Program Files`) |
  | FOLDERID_ProgramFilesX64 | 程序文件夹（x64） | `%ProgramFiles%` (`%SystemDrive%\Program Files`) |
  | FOLDERID_ProgramFilesCommon | 程序公共文件夹 | `%ProgramFiles%\Common Files` |
  | FOLDERID_ProgramFilesCommonX86 | 程序公共文件夹（x86） | `%ProgramFiles%\Common Files` |
  | FOLDERID_ProgramFilesCommonX64 | 程序公共文件夹（x64） | `%ProgramFiles%\Common Files` |
  | FOLDERID_UserProgramFiles | 用户程序文件夹 | `%LOCALAPPDATA%\Programs` |
  | FOLDERID_UserProgramFilesCommon | 用户程序公共文件夹 | `%LOCALAPPDATA%\Programs\Common` |
  | FOLDERID_Programs | 开始菜单程序文件夹 | `%APPDATA%\Microsoft\Windows\Start Menu\Programs` |
  | FOLDERID_CommonPrograms | 开始菜单程序公共文件夹 | `%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu\Programs` |
  | FOLDERID_ProgramData | 程序数据文件夹 | `%ALLUSERSPROFILE%` (`%ProgramData%`, `%SystemDrive%\ProgramData`) |
  | FOLDERID_Fonts | 字体文件夹 | `%windir%\Fonts` |
  | FOLDERID_LocalAppData | 程序数据文件夹（本地） | `%LOCALAPPDATA%` (`%USERPROFILE%\AppData\Local`) |
  | FOLDERID_RoamingAppData | 程序数据文件夹（漫游） | `%APPDATA%` (`%USERPROFILE%\AppData\Roaming`) |
  | FOLDERID_Desktop | 桌面文件夹 | `%USERPROFILE%\Desktop` |
  | FOLDERID_PublicDesktop | 公共桌面文件夹 | `%PUBLIC%\Desktop` |
  | FOLDERID_Documents | 用户文档文件夹（我的文档） | `%USERPROFILE%\Documents` |
  | FOLDERID_PublicDocuments | 公共文档文件夹 | `%PUBLIC%\Documents` |
  | FOLDERID_Favorites | 收藏夹文件夹 | `%USERPROFILE%\Favorites` |
  | FOLDERID_SavedGames | 保存的游戏（游戏存档）文件夹 | `%USERPROFILE%\Saved Games` |
  | FOLDERID_SendTo | 发送到文件夹 | `%APPDATA%\Microsoft\Windows\SendTo` |
  | FOLDERID_StartMenu | 开始菜单文件夹 | `%APPDATA%\Microsoft\Windows\Start Menu` |
  | FOLDERID_CommonStartMenu | 公共开始菜单文件夹 | `%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu` |
  | FOLDERID_Startup | 启动文件夹 | `%APPDATA%\Microsoft\Windows\Start Menu\Programs\StartUp` |
  | FOLDERID_CommonStartup | 公共启动文件夹 | `%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu\Programs\StartUp` |
  | FOLDERID_Templates | 模板文件夹 | `%APPDATA%\Microsoft\Windows\Templates` |
  | FOLDERID_CommonTemplates | 公共模板文件夹 | `%ALLUSERSPROFILE%\Microsoft\Windows\Templates` |

  参考资料
    - API参考：<https://docs.microsoft.com/en-us/windows/win32/api/shlobj_core/nf-shlobj_core-shgetknownfolderpath>
    - `KNOWNFOLDERID`参考：<https://docs.microsoft.com/en-us/windows/win32/shell/knownfolderid>
    - `KNOWN_FOLDER_FLAG`参考：<https://docs.microsoft.com/en-us/windows/win32/api/shlobj_core/ne-shlobj_core-known_folder_flag>

- 字符串转换
  - 从`char *`

    ```cpp
    // convert_from_char.cpp
    // compile with: /clr /link comsuppw.lib

    #include <iostream>
    #include <stdlib.h>
    #include <string>

    #include "atlbase.h"
    #include "atlstr.h"
    #include "comutil.h"

    using namespace std;
    using namespace System;

    int main()
    {
        // Create and display a C style string, and then use it
        // to create different kinds of strings.
        char *orig = "Hello, World!";
        cout << orig << " (char *)" << endl;

        // newsize describes the length of the
        // wchar_t string called wcstring in terms of the number
        // of wide characters, not the number of bytes.
        size_t newsize = strlen(orig) + 1;

        // The following creates a buffer large enough to contain
        // the exact number of characters in the original string
        // in the new format. If you want to add more characters
        // to the end of the string, increase the value of newsize
        // to increase the size of the buffer.
        wchar_t * wcstring = new wchar_t[newsize];

        // Convert char* string to a wchar_t* string.
        size_t convertedChars = 0;
        mbstowcs_s(&convertedChars, wcstring, newsize, orig, _TRUNCATE);
        // Display the result and indicate the type of string that it is.
        wcout << wcstring << _T(" (wchar_t *)") << endl;

        // Convert the C style string to a _bstr_t string.
        _bstr_t bstrt(orig);
        // Append the type of string to the new string
        // and then display the result.
        bstrt += " (_bstr_t)";
        cout << bstrt << endl;

        // Convert the C style string to a CComBSTR string.
        CComBSTR ccombstr(orig);
        if (ccombstr.Append(_T(" (CComBSTR)")) == S_OK)
        {
            CW2A printstr(ccombstr);
            cout << printstr << endl;
        }

        // Convert the C style string to a CStringA and display it.
        CStringA cstringa(orig);
        cstringa += " (CStringA)";
        cout << cstringa << endl;

        // Convert the C style string to a CStringW and display it.
        CStringW cstring(orig);
        cstring += " (CStringW)";
        // To display a CStringW correctly, use wcout and cast cstring
        // to (LPCTSTR).
        wcout << (LPCTSTR)cstring << endl;

        // Convert the C style string to a basic_string and display it.
        string basicstring(orig);
        basicstring += " (basic_string)";
        cout << basicstring << endl;

        // Convert the C style string to a System::String and display it.
        String ^systemstring = gcnew String(orig);
        systemstring += " (System::String)";
        Console::WriteLine("{0}", systemstring);
        delete systemstring;
    }
    ```

  - 从`wchar_t *`

    ```cpp
    // convert_from_wchar_t.cpp
    // compile with: /clr /link comsuppw.lib

    #include <iostream>
    #include <stdlib.h>
    #include <string>

    #include "atlbase.h"
    #include "atlstr.h"
    #include "comutil.h"

    using namespace std;
    using namespace System;

    int main()
    {
        // Create a string of wide characters, display it, and then
        // use this string to create other types of strings.
        wchar_t *orig = _T("Hello, World!");
        wcout << orig << _T(" (wchar_t *)") << endl;

        // Convert the wchar_t string to a char* string. Record
        // the length of the original string and add 1 to it to
        // account for the terminating null character.
        size_t origsize = wcslen(orig) + 1;
        size_t convertedChars = 0;

        // Use a multibyte string to append the type of string
        // to the new string before displaying the result.
        char strConcat[] = " (char *)";
        size_t strConcatsize = (strlen( strConcat ) + 1)*2;

        // Allocate two bytes in the multibyte output string for every wide
        // character in the input string (including a wide character
        // null). Because a multibyte character can be one or two bytes,
        // you should allot two bytes for each character. Having extra
        // space for the new string is not an error, but having
        // insufficient space is a potential security problem.
        const size_t newsize = origsize*2;
        // The new string will contain a converted copy of the original
        // string plus the type of string appended to it.
        char *nstring = new char[newsize+strConcatsize];

        // Put a copy of the converted string into nstring
        wcstombs_s(&convertedChars, nstring, newsize, orig, _TRUNCATE);
        // append the type of string to the new string.
        _mbscat_s((unsigned char*)nstring, newsize+strConcatsize, (unsigned char*)strConcat);
        // Display the result.
        cout << nstring << endl;

        // Convert a wchar_t to a _bstr_t string and display it.
        _bstr_t bstrt(orig);
        bstrt += " (_bstr_t)";
        cout << bstrt << endl;

        // Convert the wchar_t string to a BSTR wide character string
        // by using the ATL CComBSTR wrapper class for BSTR strings.
        // Then display the result.

        CComBSTR ccombstr(orig);
        if (ccombstr.Append(_T(" (CComBSTR)")) == S_OK)
        {
            // CW2A converts the string in ccombstr to a multibyte
            // string in printstr, used here for display output.
            CW2A printstr(ccombstr);
            cout << printstr << endl;
            // The following line of code is an easier way to
            // display wide character strings:
            wcout << (LPCTSTR) ccombstr << endl;
        }

        // Convert a wide wchar_t string to a multibyte CStringA,
        // append the type of string to it, and display the result.
        CStringA cstringa(orig);
        cstringa += " (CStringA)";
        cout << cstringa << endl;

        // Convert a wide character wchar_t string to a wide
        // character CStringW string and append the type of string to it
        CStringW cstring(orig);
        cstring += " (CStringW)";
        // To display a CStringW correctly, use wcout and cast cstring
        // to (LPCTSTR).
        wcout << (LPCTSTR)cstring << endl;

        // Convert the wide character wchar_t string to a
        // basic_string, append the type of string to it, and
        // display the result.
        wstring basicstring(orig);
        basicstring += _T(" (basic_string)");
        wcout << basicstring << endl;

        // Convert a wide character wchar_t string to a
        // System::String string, append the type of string to it,
        // and display the result.
        String ^systemstring = gcnew String(orig);
        systemstring += " (System::String)";
        Console::WriteLine("{0}", systemstring);
        delete systemstring;
    }
    ```

  - 从`basic_string`

    ```cpp
    // convert_from_basic_string.cpp
    // compile with: /clr /link comsuppw.lib

    #include <iostream>
    #include <stdlib.h>
    #include <string>

    #include "atlbase.h"
    #include "atlstr.h"
    #include "comutil.h"

    using namespace std;
    using namespace System;

    int main()
    {
        // Set up a basic_string string.
        string orig("Hello, World!");
        cout << orig << " (basic_string)" << endl;

        // Convert a wide character basic_string string to a multibyte char*
        // string. To be safe, we allocate two bytes for each character
        // in the original string, including the terminating null.
        const size_t newsize = (strlen(orig.c_str()) + 1)*2;
        char *nstring = new char[newsize];
        strcpy_s(nstring, newsize, orig.c_str());
        cout << nstring << " (char *)" << endl;

        // Convert a basic_string string to a wide character
        // wchar_t* string. You must first convert to a char*
        // for this to work.
        const size_t newsizew = strlen(orig.c_str()) + 1;
        size_t convertedChars = 0;
        wchar_t *wcstring = new wchar_t[newsizew];
        mbstowcs_s(&convertedChars, wcstring, newsizew, orig.c_str(), _TRUNCATE);
        wcout << wcstring << _T(" (wchar_t *)") << endl;

        // Convert a basic_string string to a wide character
        // _bstr_t string.
        _bstr_t bstrt(orig.c_str());
        bstrt += _T(" (_bstr_t)");
        wcout << bstrt << endl;

        // Convert a basic_string string to a wide character
        // CComBSTR string.
        CComBSTR ccombstr(orig.c_str());
        if (ccombstr.Append(_T(" (CComBSTR)")) == S_OK)
        {
            // Make a multibyte version of the CComBSTR string
            // and display the result.
            CW2A printstr(ccombstr);
            cout << printstr << endl;
        }

        // Convert a basic_string string into a multibyte
        // CStringA string.
        CStringA cstring(orig.c_str());
        cstring += " (CStringA)";
        cout << cstring << endl;

        // Convert a basic_string string into a wide
        // character CStringW string.
        CStringW cstringw(orig.c_str());
        cstringw += _T(" (CStringW)");
        wcout << (LPCTSTR)cstringw << endl;

        // Convert a basic_string string to a System::String
        String ^systemstring = gcnew String(orig.c_str());
        systemstring += " (System::String)";
        Console::WriteLine("{0}", systemstring);
        delete systemstring;
    }
    ```

  摘自：<https://docs.microsoft.com/en-us/cpp/text/how-to-convert-between-various-string-types>

- 如何监听用户是否按下了键盘上的音量+/-，静音，打印，播放，暂停等功能键？

  在`WndProc`中监听`WM_APPCOMMAND`这个消息，使用`GET_APPCOMMAND_LPARAM(lParam)`获取触发消息的类型，使用`GET_DEVICE_LPARAM(lParam)`获取触发该消息的设备，使用`GET_KEYSTATE_LPARAM(lParam)`获取触发该消息的按键。

  参考：<https://docs.microsoft.com/en-us/windows/win32/inputdev/wm-appcommand>
- Windows 官方支持周期

  | 操作系统 | 支持开始日期 | 主流支持结束日期 | 延长支持结束日期 | 是否已经停止支持 |
  | ------- | ---------- | -------------- | -------------- | ------------- |
  | Windows 7 | 10/22/2009 | 01/13/2015 | 01/14/2020 | **是** |
  | Windows 8 | 10/30/2012 | - | 01/12/2016 | **是** |
  | Windows 8.1 | 11/13/2013 | 01/09/2018 | 01/10/2023 | 否（延长支持阶段） |
  | Windows 10 **Home and Pro** 1507 | 07/29/2015 | 05/09/2017 | - | - |
  | Windows 10 Home and Pro 1511 | 11/10/2015 | 10/10/2017 | - | - |
  | Windows 10 Home and Pro 1607 | 08/02/2016 | 04/10/2018 | - | - |
  | Windows 10 Home and Pro 1703 | 04/05/2017 | 10/09/2018 | - | - |
  | Windows 10 Home and Pro 1709 | 10/17/2017 | 04/09/2019 | - | - |
  | Windows 10 Home and Pro 1803 | 04/30/2018 | 11/12/2019 | - | - |
  | Windows 10 Home and Pro 1809 | 11/13/2018 | 11/10/2020 | - | - |
  | Windows 10 Home and Pro 1903 | 05/21/2019 | 12/08/2020 | - | - |
  | Windows 10 Home and Pro 1909 | 11/12/2019 | 05/11/2021 | - | - |
  | Windows 10 Home and Pro 2004 | 05/27/2020 | 12/14/2021 | - | - |
  | Windows 10 Home and Pro 20H2 | 10/20/2020 | 05/10/2022 | - | - |
  | Windows 10 **Enterprise and Education** 1507 | 07/29/2015 | 05/09/2017 | - | - |
  | Windows 10 Enterprise and Education 1511 | 11/10/2015 | 10/10/2017 | - | - |
  | Windows 10 Enterprise and Education 1607 | 08/02/2016 | 04/09/2019 | - | - |
  | Windows 10 Enterprise and Education 1703 | 04/11/2017 | 10/08/2019 | - | - |
  | Windows 10 Enterprise and Education 1709 | 10/17/2017 | 10/13/2020 | - | - |
  | Windows 10 Enterprise and Education 1803 | 04/30/2018 | 05/11/2021 | - | - |
  | Windows 10 Enterprise and Education 1809 | 11/13/2018 | 05/11/2021 | - | - |
  | Windows 10 Enterprise and Education 1903 | 05/21/2019 | 12/08/2020 | - | - |
  | Windows 10 Enterprise and Education 1909 | 11/12/2019 | 05/10/2022 | - | - |
  | Windows 10 Enterprise and Education 2004 | 05/27/2020 | 12/14/2021 | - | - |
  | Windows 10 Enterprise and Education 20H2 | 10/20/2020 | 05/09/2023 | - | - |

  注意：
  - 购买Extended Security Updates (ESU)可以延长三年支持周期，最多可以购买三个ESU，也就是最多延长九年支持。此类支持周期没有在上表中列出。
  - 参考链接：<https://docs.microsoft.com/en-us/lifecycle/products/windows-7>、<https://docs.microsoft.com/en-us/lifecycle/products/windows-8>、<https://docs.microsoft.com/en-us/lifecycle/products/windows-81>、<https://docs.microsoft.com/en-us/lifecycle/products/windows-10-home-and-pro>、<https://docs.microsoft.com/en-us/lifecycle/products/windows-10-enterprise-and-education>

- 判断系统此刻是否联网（这里是指真正的联网，连了一个没网的网络是不行的）：

  ```cpp
  #include <netlistmgr.h>
  #include <atlbase.h>

  bool Utilities::isInternetAvailable()
  {
      if (FAILED(CoInitialize(nullptr))) {
          return false;
      }
      bool result = false;
      CComPtr<IUnknown> pUnknown;
      if (SUCCEEDED(CoCreateInstance(CLSID_NetworkListManager, nullptr, CLSCTX_ALL, IID_PPV_ARGS(&pUnknown)))) {
          CComPtr<INetworkListManager> pNetworkListManager;
          if (SUCCEEDED(pUnknown->QueryInterface(IID_PPV_ARGS(&pNetworkListManager)))) {
              VARIANT_BOOL isConnected = VARIANT_FALSE;
              if (SUCCEEDED(pNetworkListManager->get_IsConnectedToInternet(&isConnected))) {
                  result = isConnected == VARIANT_TRUE;
  #if 0
                  NLM_CONNECTIVITY connectivity = NLM_CONNECTIVITY_DISCONNECTED;
                  if (SUCCEEDED(pNetworkListManager->GetConnectivity(&connectivity))) {
                      // 此时的“connectivity”指示了此刻互联网连接的状态，具体请查看枚举的定义
                  }
  #endif
              }
          }
      }
      CoUninitialize();
      return result;
  }
  ```

- GUI程序如何创建一个命令行窗口，并将输出重定向到那个窗口？

  ```cpp
  #ifndef ENABLE_VIRTUAL_TERMINAL_PROCESSING
  #  define ENABLE_VIRTUAL_TERMINAL_PROCESSING (0x0004)
  #endif

  bool createConsoleWindow(const bool utf8Enabled, const bool vtEnabled)
  {
      // AllocConsole() 执行成功之后，在不需要该命令行窗口后，记得执行 FreeConsole()，否则会有内存泄露。
      // 或者使用 AttachConsole()，可以附加到父进程的控制台（例如从控制台运行GUI程序）。使用该函数不需要
      // 调用 FreeConsole()，而且下面的代码也仍然是通用的。
      if (!AllocConsole()) {
          qWarning().noquote() << "Failed to alloc console window.";
          return false;
      }
      FILE *in = nullptr;
      FILE *out = nullptr;
      FILE *err = nullptr;
      freopen_s(&in, "CONIN$", "r", stdin);
      freopen_s(&out, "CONOUT$", "w", stdout);
      freopen_s(&err, "CONOUT$", "w", stderr);
      if (utf8Enabled) {
          if (!(SetConsoleCP(CP_UTF8) && SetConsoleOutputCP(CP_UTF8))) {
              qWarning().noquote() << "Failed to change console's code page to UTF-8.";
          }
      }
      if (vtEnabled && (QOperatingSystemVersion::current() >= QOperatingSystemVersion::Windows10)) {
          const auto enableVTMode = [](const HANDLE handle) -> bool {
              if (!handle || (handle == INVALID_HANDLE_VALUE)) {
                  return false;
              }
              DWORD dwMode = 0;
              if (!GetConsoleMode(handle, &dwMode)) {
                  return false;
              }
              if (dwMode & ENABLE_VIRTUAL_TERMINAL_PROCESSING) {
                  return true;
              }
              dwMode |= ENABLE_VIRTUAL_TERMINAL_PROCESSING;
              return SetConsoleMode(handle, dwMode);
          };
          if (!(enableVTMode(GetStdHandle(STD_OUTPUT_HANDLE))
                && enableVTMode(GetStdHandle(STD_ERROR_HANDLE)))) {
              qWarning().noquote() << "Failed to enable virtual terminal mode for console.";
          }
      }
      return true;
  }
  ```

- Win10如何使普通的Win32窗口也拥有亚克力效果？

  ```cpp
  using WINDOWCOMPOSITIONATTRIB = enum _WINDOWCOMPOSITIONATTRIB
  {
      WCA_ACCENT_POLICY = 19
  };

  using WINDOWCOMPOSITIONATTRIBDATA = struct _WINDOWCOMPOSITIONATTRIBDATA
  {
      WINDOWCOMPOSITIONATTRIB Attrib;
      PVOID pvData;
      SIZE_T cbData;
  };

  using ACCENT_STATE = enum _ACCENT_STATE
  {
      ACCENT_DISABLED = 0,
      ACCENT_ENABLE_GRADIENT = 1,
      ACCENT_ENABLE_TRANSPARENTGRADIENT = 2,
      ACCENT_ENABLE_BLURBEHIND = 3,
      ACCENT_ENABLE_ACRYLICBLURBEHIND = 4, // RS4 1803
      ACCENT_ENABLE_HOSTBACKDROP = 5, // RS5 1809
      ACCENT_INVALID_STATE = 6
  };

  using ACCENT_FLAG = enum _ACCENT_FLAG
  {
      ACCENT_NONE = 0,
      ACCENT_ENABLE_LUMINOSITY = 1 << 1,
      ACCENT_ENABLE_BORDER_LEFT = 1 << 5,
      ACCENT_ENABLE_BORDER_TOP = 1 << 6,
      ACCENT_ENABLE_BORDER_RIGHT = 1 << 7,
      ACCENT_ENABLE_BORDER_BOTTOM = 1 << 8,
      ACCENT_ENABLE_BORDER = ACCENT_ENABLE_BORDER_LEFT | ACCENT_ENABLE_BORDER_TOP | ACCENT_ENABLE_BORDER_RIGHT | ACCENT_ENABLE_BORDER_BOTTOM,
      ACCENT_ENABLE_ALL = ACCENT_ENABLE_BORDER
  };

  using ACCENT_POLICY = struct _ACCENT_POLICY
  {
      ACCENT_STATE AccentState;
      DWORD AccentFlags;
      DWORD GradientColor;
      DWORD AnimationId;
  };

  using SetWindowCompositionAttributePtr = BOOL(WINAPI *)(HWND, WINDOWCOMPOSITIONATTRIBDATA *);
  static SetWindowCompositionAttributePtr SetWindowCompositionAttributePFN = nullptr;

  HMODULE User32Dll = LoadLibraryW(L"User32");
  if (User32Dll) {
      // 这个函数从 Win7 开始就已经存在并且可用了，但只能动态加载
      SetWindowCompositionAttributePFN = reinterpret_cast<SetWindowCompositionAttributePtr>(GetProcAddress(User32Dll, "SetWindowCompositionAttribute"));
      if (SetWindowCompositionAttributePFN) {
          ACCENT_POLICY accentPolicy;
          SecureZeroMemory(&accentPolicy, sizeof(accentPolicy));
          // ACCENT_ENABLE_ACRYLICBLURBEHIND 为开启亚克力模糊特效；
          // ACCENT_ENABLE_BLURBEHIND 为普通的DWM模糊特效，等价于 DwmEnableBlurBehindWindow
          accentPolicy.AccentState = ACCENT_ENABLE_ACRYLICBLURBEHIND;
          // 此为模糊特效的渐变色，不能设置成全透明，否则亚克力失效，设置成半透明效果最好。
          // 此数值格式为：AABBGGRR
          accentPolicy.GradientColor = 0x01FFFFFF;
          // 必须设置这个，否则Win11上效果不好
          accentPolicy.AccentFlags = ACCENT_ENABLE_LUMINOSITY;
          WINDOWCOMPOSITIONATTRIBDATA wcaData;
          wcaData.Attrib = WCA_ACCENT_POLICY;
          wcaData.pvData = &accentPolicy;
          wcaData.cbData = sizeof(accentPolicy);
          // hwnd 为要开启亚克力特效的窗口的句柄
          SetWindowCompositionAttributePFN(hwnd, &wcaData);
      }
  }
  ```

- 如何获取DPI？

  ```cpp
  #ifndef USER_DEFAULT_SCREEN_DPI
  #define USER_DEFAULT_SCREEN_DPI 96
  #endif

  using MONITOR_DPI_TYPE = enum _MONITOR_DPI_TYPE
  {
      MDT_EFFECTIVE_DPI = 0
  };

  using PROCESS_DPI_AWARENESS = enum _PROCESS_DPI_AWARENESS
  {
      PROCESS_DPI_UNAWARE = 0,
      PROCESS_DPI_SYSTEM_AWARE = 1,
      PROCESS_DPI_PER_MONITOR_AWARE = 2
  };

  using GetDpiForMonitorPtr = HRESULT(WINAPI *)(HMONITOR, MONITOR_DPI_TYPE, UINT *, UINT *);
  using GetProcessDpiAwarenessPtr = HRESULT(WINAPI *)(HANDLE, PROCESS_DPI_AWARENESS *);
  using GetSystemDpiForProcessPtr = UINT(WINAPI *)(HANDLE);
  using GetDpiForWindowPtr = UINT(WINAPI *)(HWND);
  using GetDpiForSystemPtr = UINT(WINAPI *)();
  using GetSystemMetricsForDpiPtr = int(WINAPI *)(int, UINT);
  using AdjustWindowRectExForDpiPtr = BOOL(WINAPI *)(LPRECT, DWORD, BOOL, DWORD, UINT);

  static GetDpiForMonitorPtr GetDpiForMonitorPFN = nullptr;
  static GetProcessDpiAwarenessPtr GetProcessDpiAwarenessPFN = nullptr;
  static GetSystemDpiForProcessPtr GetSystemDpiForProcessPFN = nullptr;
  static GetDpiForWindowPtr GetDpiForWindowPFN = nullptr;
  static GetDpiForSystemPtr GetDpiForSystemPFN = nullptr;
  static GetSystemMetricsForDpiPtr GetSystemMetricsForDpiPFN = nullptr;
  static AdjustWindowRectExForDpiPtr AdjustWindowRectExForDpiPFN = nullptr;

  // 我们之所以要动态加载，是因为很多函数是Win8.1甚至Win10才引进的，不得不动态加载以保持最大的兼容性
  HMODULE User32Dll = LoadLibraryW(L"User32");
  if (User32Dll) {
      GetDpiForWindowPFN = reinterpret_cast<GetDpiForWindowPtr>(GetProcAddress(User32Dll, "GetDpiForWindow"));
      GetDpiForSystemPFN = reinterpret_cast<GetDpiForSystemPtr>(GetProcAddress(User32Dll, "GetDpiForSystem"));
      GetSystemMetricsForDpiPFN = reinterpret_cast<GetSystemMetricsForDpiPtr>(GetProcAddress(User32Dll, "GetSystemMetricsForDpi"));
      AdjustWindowRectExForDpiPFN = reinterpret_cast<AdjustWindowRectExForDpiPtr>(GetProcAddress(User32Dll, "AdjustWindowRectExForDpi"));
      GetSystemDpiForProcessPFN = reinterpret_cast<GetSystemDpiForProcessPtr>(GetProcAddress(User32Dll, "GetSystemDpiForProcess"));
  }

  HMODULE SHCoreDll = LoadLibraryW(L"SHCore");
  if (SHCoreDll) {
      GetDpiForMonitorPFN = reinterpret_cast<GetDpiForMonitorPtr>(GetProcAddress(SHCoreDll, "GetDpiForMonitor"));
      GetProcessDpiAwarenessPFN = reinterpret_cast<GetProcessDpiAwarenessPtr>(GetProcAddress(SHCoreDll, "GetProcessDpiAwareness"));
  }

  UINT GetWindowDPI(HWND hwnd) {
      if (!hwnd) {
          return USER_DEFAULT_SCREEN_DPI;
      }
      if (GetDpiForWindowPFN) {
          return GetDpiForWindowPFN(hwnd);
      } else if (GetSystemDpiForProcessPFN) {
          return GetSystemDpiForProcessPFN(GetCurrentProcess());
      } else if (GetDpiForSystemPFN) {
          return GetDpiForSystemPFN();
      } else if (GetDpiForMonitorPFN) {
          const HMONITOR monitor = MonitorFromWindow(hwnd, MONITOR_DEFAULTTONEAREST);
          if (monitor) {
              UINT dpiX = USER_DEFAULT_SCREEN_DPI, dpiY = USER_DEFAULT_SCREEN_DPI;
              if (SUCCEEDED(GetDpiForMonitorPFN(monitor, MDT_EFFECTIVE_DPI, &dpiX, &dpiY))) {
                  return dpiX;
              } else {
                  std::cerr << "GetDpiForMonitor failed";
              }
          } else {
              std::cerr << "MonitorFromWindow failed.";
          }
      }
      // 从 Win7 开始系统支持使用 Direct2D 来获取DPI，但使用此API会产生编译警告，提示此方法已被废弃，
      // 所以此处就不使用D2D了。这里所有写出来的方法都是没有被废弃的正规方法。
      const HDC hdc = GetDC(nullptr);
      if (hdc) {
          const int dpiX = GetDeviceCaps(hdc, LOGPIXELSX);
          ReleaseDC(nullptr, hdc);
          if (dpiX > 0) {
              return dpiX;
          } else {
              std::cerr << "GetDeviceCaps failed.";
          }
      }
      return USER_DEFAULT_SCREEN_DPI;
  }
  ```

- 如何通过命令行修复系统？

  ```powershell
  DISM.exe /Online /Cleanup-Image /ScanHealth
  DISM.exe /Online /Cleanup-Image /RestoreHealth
  ```

  管理员权限PowerShell按顺序运行上述命令。若不放心，可以再运行`sfc /scannow`（管理员权限）。

- 如何设置和获取程序的DPI感知级别？

  ```cpp
  typedef enum _PROCESS_DPI_AWARENESS_EX {
      PROCESS_DPI_INVALID              = -1,
      PROCESS_DPI_UNAWARE              =  0,
      PROCESS_DPI_SYSTEM_AWARE         =  1,
      PROCESS_DPI_PER_MONITOR_AWARE    =  2,
      PROCESS_DPI_PER_MONITOR_V2_AWARE =  3
  } PROCESS_DPI_AWARENESS_EX;

  typedef enum _DPI_AWARENESS_EX {
      DPI_AWARENESS_INVALID              = -1,
      DPI_AWARENESS_UNAWARE              =  0,
      DPI_AWARENESS_SYSTEM_AWARE         =  1,
      DPI_AWARENESS_PER_MONITOR_AWARE    =  2,
      DPI_AWARENESS_PER_MONITOR_V2_AWARE =  3
  } DPI_AWARENESS_EX;

  using SetProcessDpiAwarenessContextPrototype = BOOL(WINAPI *)(DPI_AWARENESS_CONTEXT);
  using GetWindowDpiAwarenessContextPrototype = DPI_AWARENESS_CONTEXT(WINAPI *)(HWND);
  using GetAwarenessFromDpiAwarenessContextPrototype = DPI_AWARENESS_EX(WINAPI *)(DPI_AWARENESS_CONTEXT);
  using SetProcessDpiAwarenessPrototype = HRESULT(WINAPI *)(PROCESS_DPI_AWARENESS_EX);
  using GetProcessDpiAwarenessPrototype = HRESULT(WINAPI *)(HANDLE, PROCESS_DPI_AWARENESS_EX *);

  static SetProcessDpiAwarenessContextPrototype SetProcessDpiAwarenessContextPFN = nullptr;
  static GetWindowDpiAwarenessContextPrototype GetWindowDpiAwarenessContextPFN = nullptr;
  static GetAwarenessFromDpiAwarenessContextPrototype GetAwarenessFromDpiAwarenessContextPFN = nullptr;
  static SetProcessDpiAwarenessPrototype SetProcessDpiAwarenessPFN = nullptr;
  static GetProcessDpiAwarenessPrototype GetProcessDpiAwarenessPFN = nullptr;

  const HMODULE User32Dll = LoadLibraryExW(L"User32.dll", nullptr, LOAD_LIBRARY_SEARCH_SYSTEM32);
  SetProcessDpiAwarenessContextPFN = reinterpret_cast<SetProcessDpiAwarenessContextPrototype>(GetProcAddress(User32Dll, "SetProcessDpiAwarenessContext"));
  GetWindowDpiAwarenessContextPFN = reinterpret_cast<GetWindowDpiAwarenessContextPrototype>(GetProcAddress(User32Dll, "GetWindowDpiAwarenessContext"));
  GetAwarenessFromDpiAwarenessContextPFN = reinterpret_cast<GetAwarenessFromDpiAwarenessContextPrototype>(GetProcAddress(User32Dll, "GetAwarenessFromDpiAwarenessContext"));

  const HMODULE SHCoreDll = LoadLibraryExW(L"SHCore.dll", nullptr, LOAD_LIBRARY_SEARCH_SYSTEM32);
  SetProcessDpiAwarenessPFN = reinterpret_cast<SetProcessDpiAwarenessPrototype>(GetProcAddress(SHCoreDll, "SetProcessDpiAwareness"));
  GetProcessDpiAwarenessPFN = reinterpret_cast<GetProcessDpiAwarenessPrototype>(GetProcAddress(SHCoreDll, "GetProcessDpiAwareness"));

  bool isWindowDPIAware(const HWND hWnd)
  {
      if (hWnd && GetWindowDpiAwarenessContextPFN && GetAwarenessFromDpiAwarenessContextPFN) {
          // 注意！GetAwarenessFromDpiAwarenessContext 这个函数无法返回 PMv2，如果需要判断是否为 PMv2，请使用
          // AreDpiAwarenessContextsEqual 这个函数直接比较 DPI_AWARENESS_CONTEXT。
          const DPI_AWARENESS_EX windowDpiAwareness = GetAwarenessFromDpiAwarenessContextPFN(GetWindowDpiAwarenessContextPFN(hWnd));
          if (windowDpiAwareness != DPI_AWARENESS_INVALID) {
              return ((windowDpiAwareness == DPI_AWARENESS_SYSTEM_AWARE)
                      || (windowDpiAwareness == DPI_AWARENESS_PER_MONITOR_AWARE)
                      || (windowDpiAwareness == DPI_AWARENESS_PER_MONITOR_V2_AWARE));
          }
      }
      if (GetProcessDpiAwarenessPFN) {
          PROCESS_DPI_AWARENESS_EX processDpiAwareness = PROCESS_DPI_INVALID;
          if (SUCCEEDED(GetProcessDpiAwarenessPFN(nullptr, &processDpiAwareness))) {
              return ((processDpiAwareness == PROCESS_DPI_SYSTEM_AWARE)
                      || (processDpiAwareness == PROCESS_DPI_PER_MONITOR_AWARE)
                      || (processDpiAwareness == PROCESS_DPI_PER_MONITOR_V2_AWARE));
          }
      }
      return (IsProcessDPIAware() != FALSE);
  }

  bool setProcessDPIAwareEnabled(const bool enable)
  {
      if (enable) {
          if (SetProcessDpiAwarenessContextPFN) {
              if (SetProcessDpiAwarenessContextPFN(DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2) != FALSE) {
                  return true;
              }
              if (SetProcessDpiAwarenessContextPFN(DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE) != FALSE) {
                  return true;
              }
              if (SetProcessDpiAwarenessContextPFN(DPI_AWARENESS_CONTEXT_SYSTEM_AWARE) != FALSE) {
                  return true;
              }
          }
          if (SetProcessDpiAwarenessPFN) {
              if (SUCCEEDED(SetProcessDpiAwarenessPFN(PROCESS_DPI_PER_MONITOR_V2_AWARE))) {
                  return true;
              }
              if (SUCCEEDED(SetProcessDpiAwarenessPFN(PROCESS_DPI_PER_MONITOR_AWARE))) {
                  return true;
              }
              if (SUCCEEDED(SetProcessDpiAwarenessPFN(PROCESS_DPI_SYSTEM_AWARE))) {
                  return true;
              }
          }
          return (SetProcessDPIAware() != FALSE);
      } else {
          if (SetProcessDpiAwarenessContextPFN) {
              if (SetProcessDpiAwarenessContextPFN(DPI_AWARENESS_CONTEXT_UNAWARE) != FALSE) {
                  return true;
              }
          }
          if (SetProcessDpiAwarenessPFN) {
              if (SUCCEEDED(SetProcessDpiAwarenessPFN(PROCESS_DPI_UNAWARE))) {
                  return true;
              }
          }
          // TODO: Is there a way to disable DPI aware on Windows 7?
          return false;
      }
  }
  ```

- 如何获取当前桌面会话ID：

  ```cpp
  DWORD GetActiveSessionID()
  {
      DWORD Count = 0;
      PWTS_SESSION_INFOW pSessionInfo = nullptr;
      if (::WTSEnumerateSessionsW(
          WTS_CURRENT_SERVER_HANDLE,
          0,
          1,
          &pSessionInfo,
          &Count))
      {
          for (DWORD i = 0; i < Count; ++i)
          {
              if (pSessionInfo[i].State == WTS_CONNECTSTATE_CLASS::WTSActive)
              {
                  return pSessionInfo[i].SessionId;
              }
          }

          ::WTSFreeMemory(pSessionInfo);
      }

      return static_cast<DWORD>(-1);
  }
  ```

  摘自：<https://github.com/M2Team/NanaRun/blob/4fefc0151fa877d32ba8921c5863df163ee327c8/MinSudo/MinSudo.cpp#L594>

- 如何手动启动一个Windows服务？

  ```cpp
  BOOL StartWindowsService(
      _In_ LPCWSTR ServiceName,
      _Out_ LPSERVICE_STATUS_PROCESS ServiceStatus)
  {
      BOOL Result = FALSE;

      if (ServiceStatus && ServiceName)
      {
          ::memset(ServiceStatus, 0, sizeof(LPSERVICE_STATUS_PROCESS));

          SC_HANDLE ServiceControlManagerHandle = ::OpenSCManagerW(
              nullptr,
              nullptr,
              SC_MANAGER_CONNECT);
          if (ServiceControlManagerHandle)
          {
              SC_HANDLE ServiceHandle = ::OpenServiceW(
                  ServiceControlManagerHandle,
                  ServiceName,
                  SERVICE_QUERY_STATUS | SERVICE_START);
              if (ServiceHandle)
              {
                  DWORD nBytesNeeded = 0;
                  DWORD nOldCheckPoint = 0;
                  ULONGLONG nLastTick = 0;
                  bool bStartServiceWCalled = false;

                  while (::QueryServiceStatusEx(
                      ServiceHandle,
                      SC_STATUS_PROCESS_INFO,
                      reinterpret_cast<LPBYTE>(ServiceStatus),
                      sizeof(SERVICE_STATUS_PROCESS),
                      &nBytesNeeded))
                  {
                      if (SERVICE_RUNNING == ServiceStatus->dwCurrentState)
                      {
                          Result = TRUE;
                          break;
                      }
                      else if (SERVICE_STOPPED == ServiceStatus->dwCurrentState)
                      {
                          // Failed if the service had stopped again.
                          if (bStartServiceWCalled)
                          {
                              Result = FALSE;
                              ::SetLastError(ERROR_FUNCTION_FAILED);
                              break;
                          }

                          Result = ::StartServiceW(
                              ServiceHandle,
                              0,
                              nullptr);
                          if (!Result)
                          {
                              break;
                          }

                          bStartServiceWCalled = true;
                      }
                      else if (
                          SERVICE_STOP_PENDING
                          == ServiceStatus->dwCurrentState ||
                          SERVICE_START_PENDING
                          == ServiceStatus->dwCurrentState)
                      {
                          ULONGLONG nCurrentTick = ::GetTickCount();

                          if (!nLastTick)
                          {
                              nLastTick = nCurrentTick;
                              nOldCheckPoint = ServiceStatus->dwCheckPoint;

                              // Same as the .Net System.ServiceProcess, wait
                              // 250ms.
                              ::SleepEx(250, FALSE);
                          }
                          else
                          {
                              // Check the timeout if the checkpoint is not
                              // increased.
                              if (ServiceStatus->dwCheckPoint
                                  <= nOldCheckPoint)
                              {
                                  ULONGLONG nDiff = nCurrentTick - nLastTick;
                                  if (nDiff > ServiceStatus->dwWaitHint)
                                  {
                                      Result = FALSE;
                                      ::SetLastError(ERROR_TIMEOUT);
                                      break;
                                  }
                              }

                              // Continue looping.
                              nLastTick = 0;
                          }
                      }
                      else
                      {
                          break;
                      }
                  }

                  ::CloseServiceHandle(ServiceHandle);
              }

              ::CloseServiceHandle(ServiceControlManagerHandle);
          }
      }
      else
      {
          ::SetLastError(ERROR_INVALID_PARAMETER);
      }

      return Result;
  }
  ```

  摘自：<https://github.com/M2Team/NanaRun/blob/4fefc0151fa877d32ba8921c5863df163ee327c8/MinSudo/MinSudo.cpp#L723>

- 高精度TickCount实现：

  ```cpp
  EXTERN_C ULONGLONG WINAPI MileGetTickCount()
  {
      LARGE_INTEGER Frequency;
      if (::QueryPerformanceFrequency(&Frequency))
      {
          LARGE_INTEGER PerformanceCount;
          if (::QueryPerformanceCounter(&PerformanceCount))
          {
              return (PerformanceCount.QuadPart * 1000 / Frequency.QuadPart);
          }
      }

      return ::GetTickCount64();
  }
  ```

  摘自：<https://github.com/ProjectMile/Mile.Windows.Helpers/blob/a3ff3823cf866db88c61f19e358c99cf5249bcfd/Mile.Helpers/Mile.Helpers.Base.cpp#L100>

- CreateThread 的 VCRT 用 Wrapper (当你用老版本 VCRT 的时候为了创建新线程的时候能正确初始化 VCRT 这个 wrapper 会很有用)

  ```cpp
  EXTERN_C HANDLE WINAPI MileCreateThread(
      _In_opt_ LPSECURITY_ATTRIBUTES lpThreadAttributes,
      _In_ SIZE_T dwStackSize,
      _In_ LPTHREAD_START_ROUTINE lpStartAddress,
      _In_opt_ LPVOID lpParameter,
      _In_ DWORD dwCreationFlags,
      _Out_opt_ LPDWORD lpThreadId)
  {
      // sanity check for lpThreadId
      assert(sizeof(DWORD) == sizeof(unsigned));

      typedef unsigned(__stdcall* routine_type)(void*);

      // _beginthreadex calls CreateThread which will set the last error
      // value before it returns.
      return reinterpret_cast<HANDLE>(::_beginthreadex(
          lpThreadAttributes,
          static_cast<unsigned>(dwStackSize),
          reinterpret_cast<routine_type>(lpStartAddress),
          lpParameter,
          dwCreationFlags,
          reinterpret_cast<unsigned*>(lpThreadId)));
  }
  ```

  摘自：<https://github.com/ProjectMile/Mile.Windows.Helpers/blob/a3ff3823cf866db88c61f19e358c99cf5249bcfd/Mile.Helpers/Mile.Helpers.Base.cpp#L115>

- PowerShell 设置代理

  ```powershell
  $env:HTTP_PROXY="http://127.0.0.1:8080"
  $env:HTTPS_PROXY="http://127.0.0.1:8080"
  ```

  注意事项：大小写敏感、双引号不能丢，且设置后仅对当前控制台实例有效，若想全局生效，需添加全局环境变量。