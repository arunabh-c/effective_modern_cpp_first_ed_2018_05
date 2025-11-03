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



```
