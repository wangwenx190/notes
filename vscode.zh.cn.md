# Visual Studio Code 笔记

- 配置文件路径：`%APPDATA%\Code\User\settings.json`
- 推荐设置：

  ```json
  {
      // 隐藏活动栏（侧边栏）
      "workbench.activityBar.visible": false,
      // 换行符根据当前平台自动调整
      "files.eol": "auto",
      // 默认文本编码设置为 UTF-8 （不带 BOM。如果要带 BOM，应设置为 utf8bom）
      "files.encoding": "utf8",
      // 打开文件时自动检测文本编码。推荐打开！
      "files.autoGuessEncoding": true,
      // 保存文件时删除最终空行后的所有行（也就是最多保留一个空行）
      "files.trimFinalNewlines": true,
      // 保存文件时删除行尾多余的空格
      "files.trimTrailingWhitespace": true,
      // 保存文件时保证文件末尾有一个空行
      "files.insertFinalNewline": true,
      // 当退出编辑器时，如果有文件未保存，提示用户保存。默认是不会提示的，推荐打开！
      "files.hotExit": "off",
      // 开启字体连字支持
      "editor.fontLigatures": true,
      // 运行VSCode时不自动恢复上一次打开的文件
      "window.restoreWindows": "none",
      // 自动检测并应用高对比度主题
      "window.autoDetectHighContrast": true,
      // 自动匹配系统的颜色主题
      "window.autoDetectColorScheme": true,
      // 使用自定义外观的对话框窗口
      "window.dialogStyle": "custom"
  }
  ```
