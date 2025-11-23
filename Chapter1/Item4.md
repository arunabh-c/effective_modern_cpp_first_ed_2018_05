```
#include <iostream>
#include <vector>
#include <boost/type_index.hpp>

const int theAnswer = 42;
auto x = theAnswer;
auto y = &theAnswer;

template<typename T>
class TD
{
	//std::vector<T> z; //uncomment this line to remove error in lines 24, 25
};

// below generates error because there is no template definition to instantiate
//TD<decltype(x)> xType; //error: aggregate 'TD<int> xType' has incomplete type and cannot be defined
//TD<decltype(y)> yType; //error: aggregate 'TD<const int *> yType' has incomplete type and cannot be defined

struct Widget {
	int x;
	float y;
};

template<typename T>
void f(const T& param)
{
	std::cout<<"T = " << typeid(T).name() << '\n';//T = PK6Widget // 6 is size of string 'Widget'
	std::cout<<"param = " << typeid(param).name() << '\n';//param = PK6Widget which is wrong since param = const T&
	//above behavior is because specs of std::type_info specifies to ignore reference-ness and its const/volatile-ness
	//correct type for param is const Widget * const &

	using boost::typeindex::type_id_with_cvr;
	std::cout<< "T = " << type_id_with_cvr<T>().pretty_name() <<'\n';// with_cvr refers to retain const, volatile, reference qualifiers
	std::cout<< "param = " << type_id_with_cvr<decltype(param)>().pretty_name() <<'\n';
}

std::vector<Widget> createVec()
{
	std::vector<Widget> v;

	Widget w;
	w.x = 0;
	w.y = 0.0;
	v.emplace_back(w);
	return v;
}


int main()
{
	std::cout<< typeid(x).name() <<'\n';//display types
	std::cout<< typeid(y).name() <<'\n';//PKi means Pointer to Konst

	const auto vw = createVec();
	if (!vw.empty())
	{
		f(&vw[0]);
	}

	return 0;
}
```
