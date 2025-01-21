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
