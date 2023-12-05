# C++ 笔记

- 使用`__TIME__`可以获取编译的时刻，`__DATE__`可以获取编译的日期，类型均为`const char *`。
- 使用`__FUNCTION__`可以获取当前函数名，`__FILE__`可以获取当前源文件路径和文件名，`__LINE__`可以获取当前行号，类型均为`const char *`。
- 如何使用`pImpl`：

  ```cpp
  #include <iostream>
  #include <memory>
  #include <experimental/propagate_const>

  // interface (widget.h)
  class widget {
      class impl;
      std::experimental::propagate_const<std::unique_ptr<impl>> pImpl;
  public:
      void draw() const; // public API that will be forwarded to the implementation
      void draw();
      bool shown() const { return true; } // public API that implementation has to call
      widget(int);
      ~widget(); // defined in the implementation file, where impl is a complete type
      widget(widget&&); // defined in the implementation file
                        // Note: calling draw() on moved-from object is UB
      widget(const widget&) = delete;
      widget& operator=(widget&&); // defined in the implementation file
      widget& operator=(const widget&) = delete;
  };

  // implementation (widget.cpp)
  class widget::impl {
      int n; // private data
  public:
      void draw(const widget& w) const {
          if(w.shown()) // this call to public member function requires the back-reference
              std::cout << "drawing a const widget " << n << '\n';
      }
      void draw(const widget& w) {
          if(w.shown())
              std::cout << "drawing a non-const widget " << n << '\n';
      }
      impl(int n) : n(n) {}
  };
  void widget::draw() const { pImpl->draw(*this); }
  void widget::draw() { pImpl->draw(*this); }
  widget::widget(int n) : pImpl{std::make_unique<impl>(n)} {}
  widget::widget(widget&&) = default;
  widget::~widget() = default;
  widget& widget::operator=(widget&&) = default;

  // user (main.cpp)
  int main()
  {
      widget w(7);
      const widget w2(8);
      w.draw();
      w2.draw();
  }
  ```

  输出：

  ```text
  drawing a non-const widget 7
  drawing a const widget 8
  ```

  摘自：<https://en.cppreference.com/w/cpp/language/pimpl>
- 字符串格式化：<https://github.com/fmtlib/fmt>
- 输出日志：<https://github.com/gabime/spdlog>
- 如何使编译生成的二进制文件最小

  关闭调试信息的生成（即release模式）+为大小优化+开启链接时间代码生成（LTCG/LTO/IPO）+关闭RTTI+关闭异常处理+消除重复代码+动态链接运行时+尽可能的静态链接所用到的库

  以`clang-cl`为例：

  ```text
  // 编译
  clang-cl /clang:-Oz -flto=thin /GR- /EHs-c- /Zc:inline /Gw /Gy /MD
  // 链接
  lld-link /OPT:REF /OPT:ICF /OPT:LBR
  // 以上都不是完整命令行，还缺少其他必要参数，此处展示的仅为所需的参数
  ```

  说明：
    1. `/clang:-Oz`：为大小优化。要有`/clang:`这个前缀是因为`Oz`这个优化级别是`Clang`独有的，`clang-cl`不能识别；
    2. `-flto=thin`：开启链接时间代码生成（LTCG/LTO/IPO）。注意`-f`不能写为`/f`。后面的`=thin`是设置为`ThinLTO`模式，默认是`FullLTO`模式，耗时较长；
    3. `/GR-`：关闭RTTI（MSVC参数）；
    4. `/EHs-c-`：关闭异常处理（MSVC参数）；
    5. `/Zc:inline /Gw /Gy` + `/OPT:REF /OPT:ICF /OPT:LBR`：消除重复代码（MSVC参数）；
    6. `/MD`：动态链接运行时（MSVC参数）；

  注：模板元编程会导致代码体积急剧膨胀，如果想要追求二进制文件的大小，一定要尽量减少使用。
- 函数指针

  ```text
  using 函数指针名字 = 返回值类型 (调用约定 *) (参数1类型, 参数2类型, ..., 参数N类型);
  例如：
  using myFuncPtr1 = bool (__stdcall *) (int, bool, char *);
  using myFuncPtr2 = int (*) ();
  ```

- 重定向`std::cout`/`std::cerr`/`std::clog`到文件

  ```cpp
  std::streambuf* outBuf = std::cout.rdbuf();
  std::streambuf* errBuf = std::cerr.rdbuf();
  std::streambuf* logBuf = std::clog.rdbuf();
  std::ofstream logFile("test.log", std::ios::out | std::ios::trunc);
  if (logFile.is_open())
  {
      std::streambuf* fileBuf = logFile.rdbuf();
      std::cout.rdbuf(fileBuf);
      std::cerr.rdbuf(fileBuf);
      std::clog.rdbuf(fileBuf);
  }
  else
  {
      std::cerr << "Failed to create the log file." << std::endl;
  }
  // output messages ...
  if (logFile.is_open())
  {
      std::cout.rdbuf(outBuf);
      std::cerr.rdbuf(errBuf);
      std::clog.rdbuf(logBuf);
      logFile.flush();
      logFile.close();
  }
  ```

- 现代化方法生成随机数

  ```cpp
  template<typename T>
  [[nodiscard]] T getRandomNumber(const T min, const T max)
  {
      std::random_device rd{};
      std::default_random_engine dre{ rd() };
      if (std::is_same_v<T, std::int32_t>)
      {
          std::uniform_int_distribution<std::int32_t> uid{ static_cast<std::int32_t>(min), static_cast<std::int32_t>(max) };
          return uid(dre);
      }
      else if (std::is_same_v<T, std::double_t>)
      {
          std::uniform_real_distribution<std::double_t> urd{ static_cast<std::double_t>(min), static_cast<std::double_t>(max) };
          return urd(dre);
      }
      else
      {
          return T{};
      }
  }
  ```