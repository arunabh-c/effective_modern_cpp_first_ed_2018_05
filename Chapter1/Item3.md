#include <iostream>
#include <deque>

const int i = 0; //decltype(i) is const int
//bool f(const Widget& w); //decltype(w) is const Widget&
//decltype(f) is bool(const Widget&)
struct Point {
	int x, y;
}; //decltype(Point::x) is int

//Widget w; //decltype(w) is Widget

//if (f(w)) ... //decltype(f(w)) is bool

//template<typename T>// defining vector class
//class vector {
//public:
//T& operator[](std::size_t index);
//};

//vector<int> v; //decltype(v) is vector<int>
//if (v[0] == 0) //decltype(v[0]) is int&

void authenticateUser(void) {}

template<typename Container, typename Index>
//auto authAndAccess(Container& c, Index i)    // fails to compile for line 72
//decltype(auto) authAndAccess(Container& c, Index i)  // fails to compile for line 78
decltype(auto) authAndAccess(Container&& c, Index i) //change Container& to Container&& to compile line 78
//decltype(auto); auto says type is to be deduced, decltype says to use decltype rules for deduction
{
	authenticateUser();
	//return c[i];//must apply std::forward to universal references
	return std::forward<Container>(c)[i];
}

////////C++ 11 version ///////////////
/*auto authAndAccess(Container&& c, Index i)
-> decltype(std::forward<Container>(c)[i])// this line mandatory for C++11
    // the -> decltype thingy is called
    // 'trailing return type'
{
	authenticateUser();
	return std::forward<Container>(c)[i];
}*/
////////C++ 11 version ///////////////

std::deque<std::string> makeStringDeque()
{
	std::deque<std::string> x = {"hah"};
	return x;
}

decltype(auto) f()
{
	int x = 0;
	return x; //decltype(x) is int, so f returns int
	//return (x); //decltype((x)) is int&, so f returns int&
}

int main()
{
	std::deque<int> d;
	authAndAccess(d,5) = 10;//this will fail if we use
	//auto authAndAccess(Container& c, Index i) because it returns rvalue
	//which will give us rvalue = rvalue. authAndAccess will return d[5] which is int&
	//but auto follows rules of Item 1, Case 3 of the book and will turn it to int.
	//return value int of a function is an rvalue. (?)

	auto s = authAndAccess(makeStringDeque(),5); //passing rvalue to lvalue-reference
	// -to-non-const fails if we use
	//decltype(auto) authAndAccess(Container& c, Index i)
	//makeStringDeque() is passing an rvalue output, but authAndAccess only accepts lvalue-reference
	//which are incompatible, hence replacing Container& c with Container&& c lets it accept both rvalue
	//and lvalue

	return 0;
}
