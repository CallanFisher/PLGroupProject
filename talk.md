# C++14’S Relaxing Requirements for constexpr Functions

## Michelle Bray, Callan Fisher, Marc Simpson

### December 2014

------------------

# From C++11…

- Constant expression functions restrictive

- Executed at compile time

- Only contain a single expression

------------------

# …To C++14

- Relaxes restrictions for constexpr

- Allows variable mutation

- Additional restrictions for static and local_thread variables

- Still executed at compile time

------------------


------------------
#Static Local Variables

- Relaxing rules for constexpr leads to increased restrictions for static local variables
*- Prevents side effects

- Additional restrictions to ensure evaluation runs correctly
------------------
# Example Code

```C++
constexpr int first_val ( int n ) {
  static int value = n;            	 // error: not a constant expression
  return value;
}

const int N = first_val(42);
int arr[first_val(422)];
```
------------------
# Example Code(2)

```C++
constexpr int first_val ( int n ) {
  static int value = n;            	 // error: not a constant expression
  return value;
}

const int N = first_val(42);
int arr[first_val(422)];
```
------------------
#Novice Friendly

*“I hope that the tide has turned so that C++ is becoming more novice friendly."* – Bjarne Stroustrup

- Less restrictions = more intuitive for beginners

 
------------------
# Personal Opinion

- Beneficial update

- Simplifies language

- Natural combination and flow 

------------------
# Future of C++

- Less error prone

- Easier for beginners to learn

- What do you think?

------------------

# Sources

http://meetingcpp.com/index.php/br/items/looking-at-c14.html
 
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3597.html
 
http://www.infoq.com/news/2014/08/cpp14-here-features

http://electronicdesign.com/dev-tools/bjarne-stroustrup-talks-about-c14

http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3652.html
