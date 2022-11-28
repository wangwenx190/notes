# MSVC 使用笔记

- 发行版运行时查找路径：`C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Redist`以及`C:\Program Files (x86)\Windows Kits\10\Redist`。此为常见路径，请根据自己的环境自行修改。
- 根据宏定义判断编译器版本

  | MSVC版本 | `_MSC_VER`的值 | 对应的VS版本 |
  | -------- | -------------- | ----------- |
  | 14.34 | 1934 | 2022 (17.4) |
  | 14.33 | 1933 | 2022 (17.3) |
  | 14.32 | 1932 | 2022 (17.2) |
  | 14.31 | 1931 | 2022 (17.1) |
  | 14.30 | 1930 | 2022 (17.0) |
  | 14.29 | 1929 | 2019 (16.10 \~ 16.11) |
  | 14.28 | 1928 | 2019 (16.8 \~ 16.9) |
  | 14.27 | 1927 | 2019 (16.7) |
  | 14.26 | 1926 | 2019 (16.6) |
  | 14.25 | 1925 | 2019 (16.5) |
  | 14.24 | 1924 | 2019 (16.4) |
  | 14.23 | 1923 | 2019 (16.3) |
  | 14.22 | 1922 | 2019 (16.2) |
  | 14.21 | 1921 | 2019 (16.1) |
  | 14.20 | 1920 | 2019 (16.0) |
  | 14.16 | 1916 | 2017 (15.9) |
  | 14.15 | 1915 | 2017 (15.8) |
  | 14.14 | 1914 | 2017 (15.7) |
  | 14.13 | 1913 | 2017 (15.6) |
  | 14.12 | 1912 | 2017 (15.5) |
  | 14.11 | 1911 | 2017 (15.3) |
  | 14.10 | 1910 | 2017 (15.0 \~ 15.2) |
  | 14.0 | 1900 | 2015 |
  | ~13.0~ | - | - |
  | 12.0 | 1800 | 2013 |
  | 11.0 | 1700 | 2012 |
  | 10.0 | 1600 | 2010 |
  | 9.0 | 1500 | 2008 |
  | 8.0 | 1400 | 2005 |
  | 7.1 | 1310 | 2003 |
  | 7.0 | 1300 | 2002 |
  | 6.0 | 1200 | 6.0 |
  | 5.0 | 1100 | 5.0 |
  | 4.2 | 1020 | 4.2 |
  | ~4.1~ | - | - |
  | 4.0 | 1000 | 4.0 |
  | ~3.0~ | - | - |
  | 2.0 | 900 | 2.0 |
  | 1.0 | 800 | 1.0 |

- 根据宏定义判断编译时系统版本

  | 系统版本 | `WINVER`及`_WIN32_WINNT`的值 | 相关宏定义 |
  | ------ | ---------------------------- | -- |
  | Windows 10 | 0x0A00 | `_WIN32_WINNT_WIN10`/`_WIN32_WINNT_WINTHRESHOLD` |
  | Windows 8.1 | 0x0603 | `_WIN32_WINNT_WINBLUE` |
  | Windows 8 | 0x0602 | `_WIN32_WINNT_WIN8` |
  | Windows 7 | 0x0601 | `_WIN32_WINNT_WIN7` |
  | Windows Vista/Windows Server 2008 | 0x0600 | `_WIN32_WINNT_VISTA`/`_WIN32_WINNT_WIN6`/`_WIN32_WINNT_WS08`/`_WIN32_WINNT_LONGHORN` |
  | Windows Server 2003 | 0x0502 | `_WIN32_WINNT_WS03` |
  | Windows XP | 0x0501 | `_WIN32_WINNT_WINXP` |
  | Windows 2000 | 0x0500 | `_WIN32_WINNT_WIN2K` |
  | Windows NT4 | 0x0400 | `_WIN32_WINNT_NT4` |

  注：`WINVER`及`_WIN32_WINNT`这两个宏总是存在并且二者的值永远相等。

- `NTDDI_VERSION`的值

  | 系统版本 | `NTDDI_VERSION`宏 | 实际值 |
  | ------- | ----------------- | ----- |
  | Windows 11 22H2 | `NTDDI_WIN10_NI` | 0x0A00000C |
  | Windows 11 | `NTDDI_WIN10_CO` | 0x0A00000B |
  | Windows 10 ? | `NTDDI_WIN10_FE` | 0x0A00000A |
  | Windows 10 2004 | `NTDDI_WIN10_MN` | 0x0A000009 |
  | Windows 10 1909 | `NTDDI_WIN10_VB` | 0x0A000008 |
  | Windows 10 1903 | `NTDDI_WIN10_19H1` | 0x0A000007 |
  | Windows 10 1809 | `NTDDI_WIN10_RS5` | 0x0A000006 |
  | Windows 10 1803 | `NTDDI_WIN10_RS4` | 0x0A000005 |
  | Windows 10 1709 | `NTDDI_WIN10_RS3` | 0x0A000004 |
  | Windows 10 1703 | `NTDDI_WIN10_RS2` | 0x0A000003 |
  | Windows 10 1607 | `NTDDI_WIN10_RS1` | 0x0A000002 |
  | Windows 10 1511 | `NTDDI_WIN10_TH2` | 0x0A000001 |
  | Windows 10 | `NTDDI_WINTHRESHOLD`/`NTDDI_WIN10` | 0x0A000000 |
  | Windows 8.1 Update 1 | - | - |
  | Windows 8.1 | `NTDDI_WINBLUE` | 0x06030000 |
  | Windows 8 | `NTDDI_WIN8` | 0x06020000 |
  | Windows 7 SP1 | - | - |
  | Windows 7 | `NTDDI_WIN7` | 0x06010000 |
  | Windows Vista SP4 | `NTDDI_WIN6SP4`/`NTDDI_VISTASP4`/`NTDDI_WS08SP4` | 0x06000400 |
  | Windows Vista SP3 | `NTDDI_WIN6SP3`/`NTDDI_VISTASP3`/`NTDDI_WS08SP3` | 0x06000300 |
  | Windows Vista SP2 | `NTDDI_WIN6SP2`/`NTDDI_VISTASP2`/`NTDDI_WS08SP2` | 0x06000200 |
  | Windows Vista SP1 | `NTDDI_WIN6SP1`/`NTDDI_VISTASP1`/`NTDDI_WS08` | 0x06000100 |
  | Windows Vista | `NTDDI_WIN6`/`NTDDI_VISTA`/`NTDDI_LONGHORN` | 0x06000000 |
  | Windows Server 2003 SP4 | `NTDDI_WS03SP4` | 0x05020400 |
  | Windows Server 2003 SP3 | `NTDDI_WS03SP3` | 0x05020300 |
  | Windows Server 2003 SP2 | `NTDDI_WS03SP2` | 0x05020200 |
  | Windows Server 2003 SP1 | `NTDDI_WS03SP1` | 0x05020100 |
  | Windows Server 2003 | `NTDDI_WS03` | 0x05020000 |
  | Windows XP SP4 | `NTDDI_WINXPSP4` | 0x05010400 |
  | Windows XP SP3 | `NTDDI_WINXPSP3` | 0x05010300 |
  | Windows XP SP2 | `NTDDI_WINXPSP2` | 0x05010200 |
  | Windows XP SP1 | `NTDDI_WINXPSP1` | 0x05010100 |
  | Windows XP | `NTDDI_WINXP` | 0x05010000 |
  | Windows 2000 SP4 | `NTDDI_WIN2KSP4` | 0x05000400 |
  | Windows 2000 SP3 | `NTDDI_WIN2KSP3` | 0x05000300 |
  | Windows 2000 SP2 | `NTDDI_WIN2KSP2` | 0x05000200 |
  | Windows 2000 SP1 | `NTDDI_WIN2KSP1` | 0x05000100 |
  | Windows 2000 | `NTDDI_WIN2K` | 0x05000000 |
  | Windows NT4 | `NTDDI_WIN4` | 0x04000000 |
