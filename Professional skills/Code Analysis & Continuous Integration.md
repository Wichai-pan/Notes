# Code Analysis & Continuous Integration



### **Scenario**

You have just joined a software development company and have been tasked with ensuring the product is reliable before it launches.

The code base is 160,000 lines long, in a custom *C++* dialect.

You need to ensure that a feature being added next week (feature 𝐵) won’t

break a feature from six weeks ago (feature 𝐴).

*How can you be sure that the new features will not break the existing codebase?*

> You write tests that pass if (old) feature 𝐴 gives expected results.



### **Unit Testing**

> Unit Testing is a *dynamic code analysis* process which identifies issues by restricting the attention of a test to a small *unit* of code.

Unit test specification varies by language and testing library4 but focuses on *small* portions with *succinct* test functions (a *unit*).

A unit test is just a *function* you write to verify an assumption.



```cpp
TEST(FactorialTest, Zero) { 
	EXPECT_EQ(1, Factorial(0));
}
```

````python
@test 2 + 2 == 4 #success
assert 2 + 2 == 5 #fails
````



### **Unit Test Example**

We have a function which computes the sum of double* x up to element int n:

```cpp
double sum(double* x, int n){
  double s = 0.0;
  for (int i=0; i<n; i++){
  s += x[i]; 
  }
}
```

We immediately see:

- 𝑛 is the length of array 𝑥
  - if $len(𝑥) < 𝑛$ ...error?

-  if type(𝑥) ≠ double
  - if an int...error?
- This sums *any* `double*`
  - can the sum yield inaccurate results?

> There are issues with sum(...) that we might hope to catch through unit tests.



We should first test for what is *expected* and verify code *correctness*.

This example may also test for unexpected inputs, eventually.

We might write a test like:

```cpp
TEST(SumFunction, ValidInput) {
  double x[] = {1.0, 2.0, 3.0};
  int n = 3;
  double result = sum(x, n);
  EXPECT_DOUBLE_EQ(result, 6.0);
}
```

> Notice that this test will not catch all the issues we mentioned – types, accuracy.



### **How do we start and when do we stop testing?**

You should begin writing unit tests early in development – tests can serve as a demonstration of code usage, i.e. they’re self-documenting.

Your tests should begin by codifying expectations of the *correct* path through the codebase – once we’ve exhausted our expectations, we should consider how to handle unexpected situations.

> Unit tests are *sufficient* when there’s *no more capacity to surprise*.

You are *not* testing the language constructs, just your *productive usage*!



### **Structuring Testing in a Program**

Normally, we write an program to solve a problem, and testing is thought about during development but rarely considered once the program is ‘done’.

You use your build system to: 

​	include test execution in the *debug* or *test* build, and exclude tests from the *release* build.

Your build system then swaps your entrypoint to the program e.g.,



### **Unit Testing v Testing Frameworks**

There is a difference between:

- knowing how to write *unit tests* and
- knowing how to use a particular *testing framework*.

The former is a *skill*, the latter is a matter of *habit*. We have time for you to *learn the skill*...

...but the habit is a matter of practice and time.



### **Code Linting**

Linting is a *static code analysis* process which identifies issues in the *source code* defining a program, like bugs and stylistic issues

Linting tools exist for nearly all languages and can give you valuable feedback on your code.

These range from poor variable name choices to inefficient patterns – think of linting as dealing with all the *warnings* from the compiler14 rather than errors.



### **Linting, by example**

We have a python program `factorial.py`:

If we run

`pytest factorial.py`
 It will print a number of outputs to the terminal enumerating lexical and logical issues with the code.

```python
def calculate_factorial(n):
  if n < 0:
    raise ValueError("n < 0!")
  elif n == 0: #note: == != =
    return 1
  else:
    result = 1
    for i in range(1, n + 1):
      result *= i
    return result
if __name__ == "__main__":
  n = calculate_factorial(10)
print(n)
```

**Linter Output**

pytest factorial.py:

```bash
************* Module factorial
factorial.py:1:0: C0114: Missing module docstring (missing-module-
docstring)
factorial.py:1:0: C0116: Missing function or method docstring (missing-
function-docstring)
factorial.py:1:24: W0621: Redefining name 'n' from outer scope (line 13)
(redefined-outer-name)
factorial.py:2:4: R1720: Unnecessary "elif" after "raise", remove the
leading "el" from "elif" (no-else-raise)
------------------------------------------------------------------
Your code has been rated at 6.67/10 (previous run: 7.50/10, -0.83)
```



### **Linting Practice & Limits**

Software development is “the process of putting bugs in code” – linting is a guide for identifying code issues *before they become bugs* (during execution).

Using a Linter can be a great way to improve the quality of your code, especially when you don’t have ready access to an expert reviewer.

However, because Linters are a distinct, additional, way to interpret your code20 they can generate false positives – identifying *useful* feedback is difficult.





### **Linting with** C
Configuring a linter with *C/C++* is less trivial than the Python example –

but often worthwhile and very good practice.

```c
#include <stdio.h>
int r_factorial(int n){
  return n * r_factorial(n-1);
}
int main(){
  int n = 10;
  printf("%d! = %d\n",
    n, n = r_factorial(n));
}
```

Using `clang-tidy bad_factorial.c` :

```
  warning: Although the value stored to
'n' is used in the enclosing expression,
the value is never actually read from
'n' [clang-analyzer-deadcode.DeadStores]
9 |             n, n = r_factorial(n));
  |             ^   ~~~~~~~~~~~~~~
```



>**How do we ensure the code is still correct, when we make changes?**
>
>**How do we automate the process of bug finding?**

