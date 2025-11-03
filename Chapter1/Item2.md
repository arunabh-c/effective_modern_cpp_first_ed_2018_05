# auto type deduction

auto type deduction follows all the same rules as template type deduction except for one.

## Similarities:

```
template<typename T> //from item1
void f(ParamType param); //from item1
f(expr); //from item1

// auto plays role of T

//Case 3: ParamType is Neither a Pointer nor a Reference
auto x = 27;//translates to...

template<typename T> //conceptual template for
void func_for_x(T param); //deducing x's type
func_for_x(27); //conceptual call: param's deduced type is x's type

//Case 3: ParamType is Neither a Pointer nor a Reference
const auto cx = x;

template<typename T> //conceptual template for
void func_for_cx(const T param); //deducing cx's type
func_for_cx(x); //conceptual call: param's deduced type is cx's type

//Case 1: ParamType is reference or pointer
const auto& rx = x;

template<typename T> //conceptual template for
void func_for_rx(const T& param); //deducing rx's type
func_for_rx(x); //conceptual call: param's deduced type is rx's type

//Case 2: ParamType is Universal Reference
auto&& uref1 = x;//x is int and lvalue, so uref1's type is int&
auto&& uref2 = cx;//cx is const int and lvalue, so uref2's type is const int&
auto&& uref3 = 27; 27 is int and rvalue, so uref3's type is int&&

//char array
const char name[] = "R. N. Briggs";//name's type is const char[13]
auto arr1 = name;// arr1's type is const char*
auto& arr2 = name;//arr2's type is const char {&}[13]

//functions
void someFunc(int, double); //type is void(int, double)

auto func1 = someFunc; //func1's type is void(*)(int, double)
auto& func2 = someFunc;//func2's type is void(%)(int, double)

//Different ways to initialize int with value 27:
int x1 = 27; //C++98
int x2(27);  //C++98
int x3 = {27}; //C++11
int x4{ 27 }; //C++11

//Replacing int with auto does the following:
auto x1 = 27;//type is int of value 27
auto x2(27);//type is int of value 27
auto x3 = {27};//type is std::initializer_list<int>, value is {27}
auto x4{ 27 }; //type is std::initializer_list<int>, value is {27}

auto x5 = {1, 2, 3.0}; //error! can't deduce T for std://initializer_list<T>

//replacing auto with template for above initializer scenario also fails:
auto x = {1, 2, 3};//std::initializer_list<int>

template<typename T>
void f(T param);
f({1, 2, 3});//error! can't deudce type for T

template<typename T>
void f(std::initializer_list<T> initlist);
f({1, 2, 3});//T deducedas int, initlist type is std::initializer_list<int>

In C++14, following uses of auto imply template type deduction:

auto createInitList()
{
  return {1, 2, 3}; // error can't deduce type for {1, 2, 3}
}

std::vector<int> v;
auto resetV = [&v](const auto& newValue) { v = newValue; };
resetV({1, 2, 3});//error, can't deduce type for {1, 2, 3}
```
