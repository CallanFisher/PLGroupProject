N3652: Relaxing Requirements for ConstExpr Functions Steers the Future of C++ in a Novice-friendly Direction
================================================================================================================================
  
We decided to explore the new C++14 update, implemented on August 18th of 2014.  According to Bjarne Stroustrup, one of the major players in the creation of C++14, “[It] is simply the completion of the work that became C++11.”  The improvements in this update, although seemingly minor, are actually quite important.  While deliberately small, they are the first big step towards making the language as a whole more appealing for novice users.  It patches up some issues encountered with C++11, while making it a cleaner, simpler, and faster language.  
  
  
  In particular, we focused on approved proposal N3597, selected for inclusion in the release of C++14.  This proposal aimed to relax a number of restrictions on constexpr functions. These changes all received overwhelmingly strong or unopposed support under review of the Evolution Working Group.  This update builds off of C++11, which introduced constexpr functions that were still rather restrictive.  C++11 constexpr functions are executed at compile time to produce a value to be used where a constant expression is required.  However, they could only contain a single expression that is returned.  C++14 will relax these restrictions according to the following four major categories: 
 
1. declaring a variable that is not static or local_thread 
2. the ability to use if (else/ if else) and switch (but no goto) 
3. the use of loops (for/ ranged-for, do/ do-while)
4. objects whose lifetime began within the constexpr evaluation can mutate.

Taking such changes into consideration, the new proposed definition of a constexpr function is as follows.  First and foremost, it shall not be virtual.  Furthermore, its return type shall be a literal type, as shall each of its parameter types.  Its function body must be equal to delete, default, or a compound statement that cannot contain any of the following: an asm-definition, a goto statement, a try-block, or a definition of a variable of non-literal type or of static or thread storage duration or for which no initialization is performed.  This differs greatly from the old definition which required that constexpr functions must contain only: null statements, static_assert-declarations, typedef declarations and alias-declarations that do not define classes or enumerations, using-declarations, using-directives, and exactly one return statement.  Consider the following example code: 

```C++
constexpr int prev(int x)
  { return --x; }               		// C++14 OK, C++11 error: use of increment

constexpr int g(int x, int n) { 	  // C++14 OK, C++11 error: body not just "return expr"
  int r = 1;
  while (--n > 42) r *= x;
  return r;
}
```
It is quite obvious that both these functions, while once illegal and prone to error, are now accepted under the refined definition of constexpr functions.

One of the main goals of the N3597 “Relaxing constraints on constexpr functions”, while relaxing the constexpr, was to make C++ easier to understand by naturally combining constant expressions. C++11 had restrictions preventing natural combination between other parts of the language such as for loops for example you could only pass in a single expression so it wouldn’t work. But by removing these restrictions not only does it make it more straightforward to understand but it gives the programmer more freedom because now there aren’t as many rules and restrictions. By loosening these rules it now allows constexpr to be more unrestricted at compile-time, meaning constexpr code can be passed multiple variables and still run at compile time. While C++11 allowed constexpr to be executed at compile time it could only pass in a single value and with the C++14 update it can pass in multiple values or variables while still running at compile time. This also makes a more a natural combination of expressions because now you don’t have to do anything different for constant expressions because it can handle multiple variables just like other expressions. This is a nice change because it is unifying C++ while making it easier to use with less rules.  

Now that constant expression can handle multiple variables or values, you can now change objects within the constant expression, which is known as Object Mutation. Object Mutation are objects that can be modified within the evaluation of a constant expression. Object Mutation will occur until the evaluation of the constant expression ends or the lifetime of the object ends; however, mutable objects cannot be modified by later constant expression evaluation. Consider the following snippets of code:
```C++
constexpr int f(int a) {
  int n = a;
  ++n;                 	  	// '++n' is not a constant expression
  return n * a;
}

int k = f(4);           		// OK, this is a constant expression
                        		// 'n' in 'f' can be modified because its lifetime
                        		// began during the evaluation of the expression.

constexpr int k2 = ++k; 	  // error, not a constant expression, cannot modify
                       		  // 'k' because its lifetime did not begin within
                        		// this expression.

struct X {
  constexpr X() : n(5) {
    n *= 2;             		// not a constant expression
  }
  int n;
};
constexpr int g() {
  X x;                  		// initialization of 'x' is a constant expression
  return x.n;
}
constexpr int k3 = g(); 	  // OK, this is a constant expression
                        		// 'x.n' can be modified because the lifetime of
                      		  // 'x' began during the evaluation of 'g()'.
  ```
This approach allows arbitrary variable mutation within an evaluation while keeping the essential properties of constant expression evaluation being independent from the mutable global state of the program. So a constant expression evaluates to the same value no matter when it is evaluated and excepting when the value is unspecified.
