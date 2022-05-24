# C++相比C中的改进


### C++相比C中的改进

#### for循环

C++中的For循环变得更加简洁，对For进行了扩增，就像Python中的for一样；

```cpp
int a[] = {1,3,4};
for (auto i : a){
    cout << i << endl;
}//输出结果就是a中的值，其过程就是将a中的所有元素依次赋给i，之后输出i。

for (auto &i : a){
    cout << i << endl;
}//对数组a中的值进行直接操作，此时for的先决条件变成了：auto &i ,也就是将a中的元素的地址给i，或者是说，i在每次循环的过程中，就是a元素的地址
```

示例代码：

```cpp
#include <iostream>
using std::cin;
using std::cout;
using std::endl;
int main() {
	int a[] = { 1,2,4,4 };
	for (auto i : a) {
		cout << i << endl;
	}
	for (auto& i : a) {
		i = 2 * i;
	}
	cout << endl;
	for (auto i : a) {
		cout << i << endl;
	}
	return 0;
}
```

### If and Switch Statement with an Initializer

#### Why add an Initializer in If anf Switch?

-  The variable, which ought to be limited in *if* block, leaks into the surrounding scope (本应限制于if块的变量，侵入了周边的作用域)
- The compiler can better optimize the code if it knows explicitly the scope of the variable is only in one *if* block (若编译器确知变量作用域限于if块，则可更好地优化代码)


