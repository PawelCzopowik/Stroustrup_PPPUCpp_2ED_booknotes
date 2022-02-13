
| [Part 2 - Input and Output >>](part2.md) | 


Part 1 - The Basics
===

These notes are for myself and are thus not a complete set of notes from the book, just reminders to self.

# General reminders:
- Remeber to use `{}` initialization notation.
- For `if then` statements use `condition ? true : false;`


# Chapter 3: Objects, Types, and Values


## Using cin to read multiple inputs separated by enter:

`cin >> x >> y >> z`

`while (cin>>current) { ... } // loop reading`

`for (double temp; cin>>temp; ) { // read temp`

## C++ 11 universal and uniform initialization:
initialize or assign with {} rather than = to prevent narrowing conversions.
```
int a {100}
char b {a} // error, might narrow
```

# Chapter 4: Computation


## Expressions:


Example: `10, 'a', "bob", int size = 11;

## Symbolic Constant
Evaluated at compile time. 

Named object that can't be changed after initialization.
`constexpr double pi = 3.14;`

## Constant
For values not known at compile time
`const int x = y + 1;`

## Statements
Statement from Wikipedia:
In computer programming a statement can be thought of as the smallest standalone element of an imperative programming language. A program is formed by a sequence of one or more statements. A statement will have internal components (e.g., expressions).

## Foreach loop 
```
vector<int> v = {5, 7, 9, 4, 6, 8};
for (int x : v) // for each x in v
  cout << x << '\n';
```



# Chapter 5: Errors



## Unless we specifically say otherwise, we will assume that your program
1. Should produce the desired results for all legal inputs
1. Should give reasonable error messages for all illegal inputs
1. Need not worry about misbehaving hardware
1. Need not worry about misbehaving system software
1. Is allowed to terminate after finding an error

## Basically, we offer three approaches to producing acceptable software:
- Organize software to minimize errors.
- Eliminate most of the errors we made through debugging and testing.
- Make sure the remaining errors are not serious.

## Here are some sources of errors:
- Poor specifi cation
- Incomplete programs
- Unexpected arguments
- Unexpected input
- Unexpected state
- Logical errors

## Callee checks - check arguments in functions
code for handling errors is in one place
Exceptions:
- We can’t modify the function defi nition
- The called function doesn’t know what to do in case of error
- The called function doesn’t know where it was called from
- Performance

## 5.6 Exceptions

### 5.6.1 Bad Arguments
```
class Bad_area_exception { }; // blank class, just to be able to throw it

int area (int length, int width)
{
  if (...) throw Bad_area_exception{};
}

...

try { ... }
catch (Bad_area_exception) { ... } // specific
catch (...) { ... }  // catch-all
```
### 5.6.3 Bad Input

```
double d = 0;
cin >> d;
if (cin) {// all is well, can read again }
else { //last read didn't succeed, take some acttion ... }
```

### Runtime Error
```
int main()
try {
  // . . . our program . . .
  return 0; // 0 indicates success
}
  catch (runtime_error& e) {
  cerr << "runtime error: " << e.what() << '\n';
  keep_window_open();
  return 1; // 1 indicates failure
}
```

## 5.6.4 Narrowing Errors: Narrow Cast
Will throw exception if it has to truncate. 

`// defined in std_lib_facilities.h`
`int x = narrow_cast<int>(2.9); // throws on 2.9, OK on 2.0`

## 5.9 Debugging

Debugging works roughly like this:
1. Get the program to compile.
2. Get the program to link.
3. Get the program to do what it is supposed to do.

### 5.9.1 Practical debug advice

1. Comment the code well, name, purpose, who, when, version, assumptions, unhandled things, 


## 5.10 Pre- and post-conditions

A pre-condition must be true for a function to work correctly.
If its violated we can eithr ignore it or check it and report the error somehow.

```
int my_complicated_function(int a, int b, int c)
// the arguments are positive and a < b < c
{
  if (!(0<a && a<b && b<c)) // ! means “not” and && means “and”
  error("bad arguments for mcf");
  // . . .
}
```

Post condition tests the return value. For example, when calculating area, the input may be positive but if there's overflow in the calculation, it may return a negative. However, we promise to return positive only.

```
int area(int length, int width)
// calculate area of a rectangle;
// pre-conditions: length and width are positive
// post-condition: returns a positive value that is the area
{
  if (length<=0 || width <=0) error("area() pre-condition");
  int a = length*width;
  if (a<=0) error("area() post-condition");
  return a;
}
```


# Chapter 8

## 8.2 Declarations and definitions
Declarations that are NOT definitions:
```
double sqrt(double); // no function body here
extern int a; // “extern plus no initializer” means “not definition”
```


## 8.3 Header files
Header files contain the declarations, source files contain the definitions.

## 8.4 Scope
- Global
- Namespace
- Class
- Local `{..}`
- Statement (for-statements)

## 8.5 Function call and return
### 8.5.3 Pass by value
### 8.5.4 Pass by const reference
Prevents modification of parameter in function (Read only)

`void print(const vector<double>& v) // pass by const reference`

### 8.5.5 Pass by reference
Allows read and write to parameter.
Useful for large amounts of data or working around multiple return values.

### 8.5.6 Pass by value vs. pass by reference
Thumb rule:
1. Use PBV for small data (1 - 2 elements).
1. Use PBCV for large objects you don't want to modify.
1. Return a result rather than modification through reference.
1. Use PBR only when you have to.

### 8.5.9 `constexpr` functions
Function evaluated at compile time, avoiding doing the same calculation many times.
Example is a scaling factor in a point.
A constexpr function may not have side effects.
```
constexpr double xscale = 10; // scaling factors
constexpr double yscale = 0.8;
constexpr Point scale(Point p) { return {xscale*p.x,yscale*p.y}; };

void user(Point p1)
{
  Point p2 {10,10};
  Point p3 = scale(p1); // OK: p3 == {100,8}; run-time evaluation is fine
  Point p4 = scale(p2); // p4 == {100,8}
  constexpr Point p5 = scale(p1); // error: scale (p1) is not a constant
  // expression
  constexpr Point p6 = scale(p2); // p6 == {100,8}
  // . . .
}
```
Here is an example of a function that violates those rules for simplicity:
```
int gob = 9;
constexpr void bad(int & arg) // error: no return value
{
  ++arg; // error: modifies caller through argument
  glob = 7; // error: modifies nonlocal variable
}
```

## 8.6 Order of evaluation
### 8.6.1 Expression evaluation
> **Rule**: If you change the value of a variable in an expression, don't read or write it twice in the same expression.

Example:
```
v[i] = ++i;     // don’t: undefined order of evaluation
v[++i] = i;     // don’t: undefined order of evaluation
int x = ++i + ++i; // don’t: undefined order of evaluation
cout << ++i << ' ' << i << '\n';  // don’t: undefined order of evaluation
f(++i,++i);     // don’t: undefined order of evaluation
```

### 8.6.2 Global initialization
Global initialization takes place before `main()` is called.
Globals in two different files have undefined initialization order! file1.cpp may be initialized after file2.cpp.
Consider any initializer that isn't a constant expression complicated (avoid)!
If complicated initialization is needed use a function:
```
const Date default_date() // return the default date
{
  return Date(1970,1,1);
}
```

This constructs the `Date` every time we call `default_date()`. That is often fine, but
if `default_date()` is called often and it is expensive to construct `Date`, we’d like to
construct the `Date` once only. That is done like this:

```
const Date& default_date()
{
  static const Date dd(1970,1,1); // initialize dd first time we get here
  return dd;
}
```

## 8.7 Namespaces
To avoid name collision.
FQN: Namespace::Variable

### 8.7.1 `using` declarations and `using` directives

```
using std::string; // string means std::string
using namespace std; // make names from std directly accessible
```


# Chapter 9 Technicalities: Classes, etc.

## 9.1 User-defined types
- Representation: A type “knows” how to represent the data needed in an object.
- Operations: A type “knows” what operations can be applied to objects.

C++ provides two kinds of user-defined types: classes and enumerations.



## 9.2 Classes and members
## 9.3 Interface and implementation
`structs` are primarily used for data structures where the members can take any value; that is, we can’t define any meaningful invariant (§9.4.3).

Class members are private by default.

## 9.4 Evolving a class
### 9.4.1 struct and functions
### 9.4.2 Member functions and constructors
Validity checking functions are of little use if we fail to use them.
A member function with the same name as its class is special. It is called a constructor
and will be used for initialization of objects of the class.
### 9.4.3 Keep details private
A rule for what constitutes a valid value is called an *invariant*.
### 9.4.4 Defining member functions
The compiler doesn’t care about the order of class function and data members;
it takes the declarations in any order you care to present them.

The `:y{yy}, m{mm}, d{dd}` notation is how we initialize members

```
int x; // first define the variable x
x = 2; // later assign to x
int x {2}; // define and immediately initialize with 2
```

Writing the definition of a member function within the class definition has
three effects:
1. The function will be inline.
1. All uses of class will be recompiled whenever we make a change to the body of the function.
1. Class definition gets larger.


### 9.4.5 Referring to the current object
### 9.4.6 Reporting errors
We put the testing of validity into a separate is_valid() function because checking
for validity is logically distinct from initialization and because we might want to
have several constructors.

```
Date::Date(int yy, int mm, int dd)
: y{yy}, m{mm}, d{dd} // initialize data members
{
  if (!is_valid()) throw Invalid{}; // check for validity
}
bool Date::is_valid() // return true if date is valid
{
  if (m<1 || 12<m) return false;
  // . . .
}
```

## 9.5 Enumerations
Prefer the simpler and safer enum classes to “plain” enums.

### 9.5.1 “Plain” enumerations
Plain enums can pollute the scope.

## 9.6 Operator overloading
Overloadable operators:
`+, –, *, /, %, [ ], ( ), ^, !, &, <, <=, >,` and `>=.`

An overloaded operator must have at least one user-defined type as operand.

Define operators only with their conventional meaning.

## 9.7 Class interfaces
**General principles:**
- Keep interfaces complete.
- Keep interfaces minimal.
- Provide constructors.
- Support copying (or prohibit it) (see §14.2.4).
- Use types to provide good argument checking.
- Identify nonmodifying member functions (see §9.7.4).
- Free all resources in the destructor (see §17.5).

### 9.7.1 Argument types
Instead of using int for month, use an enum in the class:
```
enum class Month {
  jan=1, feb, mar, apr, may, jun, jul, aug, sep, oct, nov, dec
  };
```
And, for Year, use a class:
```
class Year { // year in [min:max) range
  static const int min = 1800;
  static const int max = 2200;
  //...
```
Note the use of `static const`, this is the way we define symbolic constants of integer types within classes.
In this case, because the initializer is a constant expression, we could have used `constexpr` instead of const.

### 9.7.2 Copying
We can disable copying - `delete` the copy constructor and copy assignment (see §14.2.4).

### 9.7.3 Default constructors
**in-class initializer** Instead of placing the default values for members in the constructor, we could
place them on the members themselves:
```
class Date {
public:
  // . . .
  Date(); // default constructor
  Date(year, Month, day);
  Date(int y); // January 1 of year y
  // . . .
private:
  int y {2001};
  Month m {Month::jan};
  int d {1};
};
```
!!! That way, the default values are available to every constructor. For example:
```
Date::Date(int y) // January 1 of year y
  :y{yy}
{
  if (!is_valid()) throw Invalid{}; // check for validity
}
```
If we didn’t like to build the default value right into the constructor code, we could use a constant (or a variable). To avoid a global variable and its associated initialization problems, we use the technique from §8.6.2:
```
const Date& default_date()
{
  static Date dd {2001,Month::jan,1};
  return dd;
}

Date::Date()
  :y{default_date().year()},
  m{default_date().month()},
  d{default_date().day()}
{}
```

Note that the default constructor does not need to check its value; the constructor for `default_date` already did that.

### 9.7.4 const member functions
```
void some_function(Date& d, const Date& start_of_term)
{
  int a = d.day(); // OK
  int b = start_of_term.day(); // should be OK (why?)
  d.add_day(3); // fine
  start_of_term.add_day(3); // error
}
```

`start_of_term` is intended to be immutable, so another funtion cannot change it. How will the compiler know that?
`start_of_term.day()` is an error because the compiler does not know that `day()` doesn’t change its `Date`.
We can deal with this problem by classifying operations on a class as modifying and nonmodifying.
Operations that do not modify the object can be invoked for const objects.
```
class Date {
public:
  // . . .
  int day() const; // const member: can’t modify the object
  Month month() const; // const member: can’t modify the object
  int year() const; // const member: can’t modify the object
  void add_day(int n); // non-const member: can modify the object
  void add_month(int n); // non-const member: can modify the object
  void add_year(int n); // non-const member: can modify the object
private:
  int y; // year
  Month m;
  int d; // day of month
};

Date d {2000, Month::jan, 20};
const Date cd {2001, Month::feb, 21};

cout << d.day() << " — " << cd.day() << '\n'; // OK
d.add_day(1);     // OK
cd.add_day(1);    // error: cd is a const
```

Once we declared a member function const, the compiler holds us to our promise not to
modify the object. For example:
```
int Date::day() const
{
  ++d; // error: attempt to change object from const member function
  return d;
}
```
### 9.7.5 Members and “helper functions”
A function that can be simply, elegantly, and efficiently implemented as a freestanding function (that is, as a **nonmember function**) should be implemented outside the class. That way, a bug in
that function cannot directly corrupt the data in a class object. This helps with debugging as we have less suspects modifying our class members. Note also that if the representation changes, only the functions that directly access the representation need to be rewritten.

Often, we use namespaces to identify a group of helper functions; see §8.7:
```
namespace Chrono {
  enum class Month { /* … */ };
  class Date { /* . . . */ };
  bool is_date(int y, Month m, int d); // true for valid date
  Date next_Sunday(const Date& d) { /* . . . */ }
  Date next_weekday(const Date& d) { /* . . . */ }
  bool leapyear(int y) { /* . . . */ } // see exercise 10
  bool operator==(const Date& a, const Date& b) { /* . . . */ }
  // . . .
}
```
!!! Note the == and != functions. They are typical helpers.
Note also that we introduced a helper `function is_date()`. That function replaces
`Date::is_valid()` because checking whether a date is valid is largely independent
of the representation of a Date.

## 9.8 The `Date` class

```
// file Chrono.h

namespace Chrono {

enum class Month {
  jan=1, feb, mar, apr, may, jun, jul, aug, sep, oct, nov, dec
};
class Date {
public:
  class Invalid { }; // to throw as exception
  Date(int y, Month m, int d); // check for valid date and initialize
  Date(); // default constructor
  // the default copy operations are fine
  // nonmodifying operations:
  int day() const { return d; }
  Month month() const { return m; }
  int year() const { return y; }
  // modifying operations:
  void add_day(int n);
  void add_month(int n);
  void add_year(int n);
private:
  int y;
  Month m;
  int d;
};
bool is_date(int y, Month m, int d); // true for valid date
bool leapyear(int y); // true if y is a leap year
bool operator==(const Date& a, const Date& b);
bool operator!=(const Date& a, const Date& b);
ostream& operator<<(ostream& os, const Date& d);
istream& operator>>(istream& is, Date& dd);
} // Chrono
```

The definitions go into `Chrono.cpp`:

```
// Chrono.cpp
#include "Chrono.h"

namespace Chrono {
// member function definitions:

Date::Date(int yy, Month mm, int dd)
  : y{yy}, m{mm}, d{dd}
{
  if (!is_date(yy,mm,dd)) throw Invalid{};
}
const Date& default_date()
{
  static Date dd {2001,Month::jan,1}; // start of 21st century
  return dd;
}
Date::Date()
  :y{default_date().year()},
  m{default_date().month()},
  d{default_date().day()}
{
}
void Date:: add_day(int n)
{
  // . . .
}
void Date::add_month(int n)
{
  // . . .
}
void Date::add_year(int n)
{
  if (m==feb && d==29 && !leapyear(y+n)) { // beware of leap years!
    m = mar; // use March 1 instead of February 29
    d = 1;
  }
  y+=n;
}
// helper functions:

bool is_date(int y, Month m, int d)
{
  // assume that y is valid
  if (d<=0) return false; // d must be positive
  if (m<Month::jan || Month::dec<m) return false;
  int days_in_month = 31; // most months have 31 days
  switch (m) {
  case Month::feb: // the length of February varies
    days_in_month = (leapyear(y))?29:28;
    break;
  case Month::apr: case Month::jun: case Month::sep: case Month::nov:
    days_in_month = 30; // the rest have 30 days
    break;
  }
  if (days_in_month<d) return false;
  
  return true;
}
bool leapyear(int y)
{
  // see exercise 10
}
bool operator==(const Date& a, const Date& b)
{
  return a.year()==b.year()
    && a.month()==b.month()
    && a.day()==b.day();
}
bool operator!=(const Date& a, const Date& b)
{
  return !(a==b);
}
ostream& operator<<(ostream& os, const Date& d)
{
  return os << '(' << d.year()
            << ',' << d.month()
            << ',' << d.day() << ')';
}

istream& operator>>(istream& is, Date& dd)
{
  int y, m, d;
  char ch1, ch2, ch3, ch4;
  is >> ch1 >> y >> ch2 >> m >> ch3 >> d >> ch4;
  if (!is) return is;
  if (ch1!= '(' || ch2!=',' || ch3!=',' || ch4!=')') { // oops: format error
    is.clear(ios_base::failbit); // set the fail bit
    return is;
    }
    dd = Date(y, Month(m),d); // update dd
    
    return is;
}

enum class Day {
  sunday, monday, tuesday, wednesday, thursday, friday, saturday
};

Day day_of_week(const Date& d)
{
  // . . .
}
Date next_Sunday(const Date& d)
{
  // ...
}
Date next_weekday(const Date& d)
{
  // . . .
}

} // Chrono
```

| [Part 2 - Input and Output >>](part2.md) | 
