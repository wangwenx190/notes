# Visual Studio Code 笔记

- 配置文件路径：`%APPDATA%\Code\User\settings.json`
- 推荐设置：

  ```json
  {
    "files.eol": "auto",
    "files.encoding": "utf8",
    "files.autoGuessEncoding": true,
    //"files.trimFinalNewlines": true,
    //"files.trimTrailingWhitespace": true,
    //"files.insertFinalNewline": true,
    "files.hotExit": "off",
    "files.associations": {
        "*.qss": "css",
        "*.qss.in": "css",
        "*.json": "jsonc",
        "*.json5": "jsonc",
        "*.json.in": "jsonc",
        "*.rc.in": "rc-script"
    },
    "editor.fontLigatures": true,
    "editor.fontFamily": "'Cascadia Code', 'Cascadia Mono', Consolas, 'Courier New', monospace",
    "editor.renderWhitespace": "all",
    "editor.fontSize": 18,
    "editor.gotoLocation.alternativeDefinitionCommand": "editor.action.revealDeclaration",
    "editor.semanticTokenColorCustomizations": {
        "[Visual Studio Dark - C++]": {
            "enabled": true,
            "rules": {
                "property": "#DDBFFF",
                // "property": "#BEB7FF",
                "comment": "#808066",
                "parameter": "#CCCCCC",
                "macro": "#569cd6",
                "namespace": "#4EC9B0",
                "method.virtual:cpp": {
                    "italic": true
                },
                "variable.readonly.fileScope": {
                    "foreground": "#6C95EB",
                    "fontStyle": "bold"
                },
                "variable.globalScope": {
                    "foreground": "#6C95EB",
                    "fontStyle": "bold"
                },
            }
        }
    },
    "editor.tokenColorCustomizations": {
        "[Visual Studio Dark - C++]": {
            "comments": "#6A9955",
        },
    },
    "workbench.colorTheme": "Visual Studio Dark - C++",
    "workbench.colorCustomizations": {
        "[Visual Studio Dark - C++]": {
            "editorInlayHint.foreground": "#ffffff80",
            "editorInlayHint.background": "#ffffff10",
        }
    },
    // PKief.material-icon-theme
    "workbench.iconTheme": "material-icon-theme",
    "window.restoreWindows": "none",
    "window.autoDetectHighContrast": false,
    "window.autoDetectColorScheme": false,
    "window.dialogStyle": "custom",
    "cmake.options.statusBarVisibility": "compact",
    "cmake.showOptionsMovedNotification": false,
    "cmake.useCMakePresets": "never",
    "cmake.configureOnEdit": false,
    "cmake.configureOnOpen": false,
    "cmake.generator": "Ninja",
    "cmake.buildDirectory": "${workspaceFolder}/build/${buildType}",
    "cmake.copyCompileCommands": "${workspaceFolder}/build/compile_commands.json",
    "C_Cpp.intelliSenseEngine": "disabled",
    "clangd.arguments": [
        "--header-insertion=never",
        "--clang-tidy",
        "--compile-commands-dir=${workspaceFolder}/build"
    ],
    "clangd.path": "C:/Develop/Tools/LLVM-19.1.7-win64/bin/clangd.exe",
    // akiramiyakoda.cppincludeguard
    "C/C++ Include Guard.Auto Update Include Guard": false,
    "C/C++ Include Guard.Remove Extension": false,
    "C/C++ Include Guard.Remove Pragma Once": false,
    "C/C++ Include Guard.Spaces After Endif": 1,
    "C/C++ Include Guard.Path Depth": 0,
    "C/C++ Include Guard.Macro Type": "Filename",
    "C/C++ Include Guard.Path Skip": 0,
    "C/C++ Include Guard.Comment Style": "Line"
    // IBM.output-colorizer
    // josetr.cmake-language-support-vscode
    // ms-vscode.vs-keybindings
    // eamodio.gitlens
    // Gruntfuggly.todo-tree
    // llvm-vs-code-extensions.vscode-clangd
  }
  ```

- 使用项目设置覆盖全局设置

    在项目顶层文件夹下创建`.vscode`文件夹，并创建`settings.json`

    ```json
    "cmake.configureSettings": {
        "CMAKE_PREFIX_PATH": "C:/qt-sdk/build/qt_sdk_msvc_release_static",
        "MY_OPTION_1": true,
        "MY_OPTION_2": "123"
    },
    "cmake.configureArgs": [
        "-DTESTaaa=ON",
        "-Dbabababa=123",
        "-GNinja"
    ],
    "cmake.debugConfig": {
        "environment": [
            {
                "name": "PATH",
                "value": "${env:PATH};C:/aaa/bin"
            },
            {
                "name": "my_env_var",
                "value": "aaa"
            }
        ]
    }
    ```

    其中`cmake.configureSettings`和`cmake.configureArgs`作用是一样的，只是写法不同，可以只留着一个。