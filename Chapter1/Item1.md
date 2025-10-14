# Type Deduction


```
template<typename T> //function template
void f(ParamType param);

f(expr);//expr used to deduce 2 types: T and ParamType
```
type T is dependent not just on expr but also on ParamType in 3 following cases:

## Case 1: ParamType is reference or pointer

Steps:
1. If expr's type is a reference, ignore the reference part.
2. pattern-match expr's type against ParamType to determine T.

```template<typename T> //function template
void f(T& param);

int x = 27;
f(x);//T is int&

const int cx = x;
f(cx);// T is const int&

const inst& rx = x;
f(rx);//T is const int&. & of expr (rx) is ignored (step 1)


template<typename T> //function template
void f(const T& param);

int x = 27;
f(x);//T is const int&

const int cx = x;
f(cx);// T is const int&

const inst& rx = x;
f(rx);//T is const int&. & of expr (rx) is ignored (Step 1)


template<typename T> //function template
void f(T* param);

int x = 27;
f(&x);//T is int*

const int* cx = &x;
f(cx);// T is const int*

```

## Case 2: ParamType is Universal Reference

Steps:
1. If expr is lvalue, both T and ParamType are deduced as lvalue references.
2. If expr is rvalue, Case 1 rules apply.

```
template<typename T> //function template
void f(T&& param);

int x = 27;
f(x);//T is int&; ParamType is int&

const int cx = x;
f(cx);// T is const int&; ParamType is int&

const inst& rx = x;
f(rx);//T is const int&; ParamType is const int&

f(27);T is int; ParamType is int&& 
```

## Case 3: ParamType is Neither a Pointer nor a Reference

Steps:
1. if expr type is reference, ignore it.
2. If after ignoring reference, expr type is const or volatile, ignore that too.

```
template<typename T> //function template
void f(T param);

int x = 27;
f(x);//T is int;

const int cx = x;
f(cx);// T is int;

const int& rx = x;
f(rx);//T is int;

const char* const ptr = "fun with ptrs";//const left of char means char string is const; const right of char means ptr address is const.

f(ptr); //T is const char* ptr
```

## Array Arguments:

```
const char name[] = "J. P. Briggs"; //const char[13]
const char * ptrToName = name; //array decays to pointer

template<typename T> //function template
void f(T param);
f(name); //name is array but T deduced as const char*

template<typename T> //function template
void f(T &param);
f(name); //T deduced as const char[13] but name deduced as const char(&)[13]
// the above is a trick to deduce the number of elements an array contains
// as a compile time constant

template<typename T, std::size_t N> //function template
constexpr std::size_t arraysize(T (&)[N]) noexcept
{ return N;}
//constexpr causes N to be computed during compile time



 

```
