N3652: Relaxing Requirements for ConstExpr Functions Steers the Future of C++ in a Novice-friendly Direction
================================================================================================================================
  
...We decided to explore the new C++14 update, implemented on August 18th of 2014.  According to Bjarne Stroustrup, one of the major players in the creation of C++14, “[It] is simply the completion of the work that became C++11.”  The improvements in this update, although seemingly minor, are actually quite important.  While deliberately small, they are the first big step towards making the language as a whole more appealing for novice users.  It patches up some issues encountered with C++11, while making it a cleaner, simpler, and faster language.  
  
  
  In particular, we focused on approved proposal N3597, selected for inclusion in the release of C++14.  This proposal aimed to relax a number of restrictions on constexpr functions. These changes all received overwhelmingly strong or unopposed support under review of the Evolution Working Group.  This update builds off of C++11, which introduced constexpr functions that were still rather restrictive.  C++11 constexpr functions are executed at compile time to produce a value to be used where a constant expression is required.  However, they could only contain a single expression that is returned.  C++14 will relax these restrictions according to the following four major categories: 
 
1. declaring a variable that is not static or local_thread 
2. the ability to use if (else/ if else) and switch (but no goto) 
3. the use of loops (for/ ranged-for, do/ do-while)
4. objects whose lifetime began within the constexpr evaluation can mutate.

  Taking such changes into consideration, the new proposed definition of a constexpr function is as follows.  First and foremost, it shall not be virtual.  Furthermore, its return type shall be a literal type, as shall each of its parameter types.  Its function body must be equal to delete, default, or a compound statement that cannot contain any of the following: an asm-definition, a goto statement, a try-block, or a definition of a variable of non-literal type or of static or thread storage duration or for which no initialization is performed.  This differs greatly from the old definition which required that constexpr functions must contain only: null statements, static_assert-declarations, typedef declarations and alias-declarations that do not define classes or enumerations, using-declarations, using-directives, and exactly one return statement.  Consider the following example code: 


constexpr int prev(int x)
  { return --x; }               		// OK error: use of increment

constexpr int g(int x, int n) { 	// OK error: body not just "return expr"
  int r = 1;
  while (--n > 42) r *= x;
  return r;
}


