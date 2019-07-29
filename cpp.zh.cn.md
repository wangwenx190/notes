# C++ 笔记
- 使用`__TIME__`可以获取编译的时刻，`__DATE__`可以获取编译的日期，类型均为`const char *`。
- 使用`__FUNCTION__`可以获取当前函数名，`__FILE__`可以获取当前源文件路径和文件名，`__LINE__`可以获取当前行号，类型均为`const char *`。
- 在使用C语言风格的字符串时，尽量使用字符数组，即`const char []`，而不是指向字符数组的指针，即`const char *`，尽量少引入指针操作
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
  摘自：https://en.cppreference.com/w/cpp/language/pimpl
