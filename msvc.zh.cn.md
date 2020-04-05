# MSVC 使用笔记

- 发行版运行时查找路径：`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Redist`以及`C:\Program Files (x86)\Windows Kits\10\Redist`。此为常见路径，请根据自己的环境自行修改。
- 根据宏定义判断编译器版本

  | MSVC版本 | `_MSC_VER`的值 | 对应的VS版本 |
  | -------- | -------------- | ----------- |
  | 14.2 | 1920 | 2019 |
  | 14.1 | 1910 | 2017 |
  | 14.0 | 1900 | 2015 |
  | 13.0 | - | - |
  | 12.0 | 1800 | 2013 |
  | 11.0 | 1700 | 2012 |
  | 10.0 | 1600 | 2010 |
  | 9.0 | 1500 | 2008 |
  | 8.0 | 1400 | 2005 |
  | 7.1 | 1310 | 2003 |
  | 7.0 | 1300 | - |
  | 6.0 | 1200 | - |
  | 5.0 | 1100 | - |

- 根据宏定义判断编译时系统版本

  | 系统版本 | `WINVER`及`_WIN32_WINNT`的值 | 相关宏定义 |
  | ------ | ---------------------------- | -- |
  | Windows 10 | 0x0A00 | `_WIN32_WINNT_WIN10`/`_WIN32_WINNT_WINTHRESHOLD`/`NTDDI_WIN10`/`NTDDI_WIN10_TH2`/`NTDDI_WIN10_RS1`/`NTDDI_WIN10_RS2`/`NTDDI_WIN10_RS3`/`NTDDI_WIN10_RS4`/`NTDDI_WIN10_RS5`/`NTDDI_WIN10_19H1` |
  | Windows 8.1 | 0x0603 | `_WIN32_WINNT_WINBLUE`/`NTDDI_WINBLUE` |
  | Windows 8 | 0x0602 | `_WIN32_WINNT_WIN8`/`NTDDI_WIN8` |
  | Windows 7 | 0x0601 | `_WIN32_WINNT_WIN7`/`NTDDI_WIN7` |
  | Windows Vista/Windows Server 2008 | 0x0600 | `_WIN32_WINNT_VISTA`/`_WIN32_WINNT_WIN6`/`_WIN32_WINNT_WS08`/`_WIN32_WINNT_LONGHORN` |
  | Windows Server 2003 | 0x0502 | `_WIN32_WINNT_WS03` |
  | Windows XP | 0x0501 | `_WIN32_WINNT_WINXP` |
  | Windows 2000 | 0x0500 | `_WIN32_WINNT_WIN2K` |

  注：`WINVER`及`_WIN32_WINNT`这两个宏总是存在并且值相等
