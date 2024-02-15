
Reducing CSS Size
Mixins copy all of the properties into a selector, which can lead to unnecessary duplication. Therefore you can use extends instead of mixins to move the selector up to the properties you wish to use, which leads to less CSS being generated.

Example - with mixin:

.my-inline-block() {
  display: inline-block;
  font-size: 0;
}
.thing1 {
  .my-inline-block;
}
.thing2 {
  .my-inline-block;
}
编译后：

.thing1 {
  display: inline-block;
  font-size: 0;
}
.thing2 {
  display: inline-block;
  font-size: 0;
}
Example (with extends):

.my-inline-block {
  display: inline-block;
  font-size: 0;
}
.thing1 {
  &:extend(.my-inline-block);
}
.thing2 {
  &:extend(.my-inline-block);
}
编译后：

.my-inline-block,
.thing1,
.thing2 {
  display: inline-block;
  font-size: 0;
}
Combining Styles / A More Advanced Mixin
Another use-case is as an alternative for a mixin - because mixins can only be used with simple selectors, if you have two different blocks of html, but need to apply the same styles to both you can use extends to relate two areas.

举例：

li.list > a {
  // list styles
}
button.list-style {
  &:extend(li.list > a); // use the same list styles
}

Merge
Edit the markdown source for "merge" 
Combine properties

The merge feature allows for aggregating values from multiple properties into a comma or space separated list under a single property. merge is useful for properties such as background and transform.

Comma
Append property value with comma

Released v1.5.0

举例：

.mixin() {
  box-shadow+: inset 0 0 10px #555;
}
.myclass {
  .mixin();
  box-shadow+: 0 0 20px black;
}
编译后：

.myclass {
  box-shadow: inset 0 0 10px #555, 0 0 20px black;
}
Space
Append property value with space

Released v1.7.0

举例：

.mixin() {
  transform+_: scale(2);
}
.myclass {
  .mixin();
  transform+_: rotate(15deg);
}
编译后：

.myclass {
  transform: scale(2) rotate(15deg);
}
To avoid any unintentional joins, merge requires an explicit + or +_ flag on each join pending declaration.


Mixins
Edit the markdown source for "mixins" 
"mix-in" properties from existing styles

You can mix-in class selectors and id selectors, e.g.

.a, #b {
  color: red;
}
.mixin-class {
  .a();
}
.mixin-id {
  #b();
}
which 编译后：

.a, #b {
  color: red;
}
.mixin-class {
  color: red;
}
.mixin-id {
  color: red;
}
Historically, the parentheses in a mixin call are optional, but optional parentheses are deprecated and will be required in a future release.

.a(); 
.a;    // currently works, but deprecated; don't use
.a (); // white-space before parentheses is also deprecated
Mixins With Parentheses
If you want to create a mixin but you do not want that mixin to be in your CSS output, put parentheses after the mixin definition.

.my-mixin {
  color: black;
}
.my-other-mixin() {
  background: white;
}
.class {
  .my-mixin();
  .my-other-mixin();
}
编译后：

.my-mixin {
  color: black;
}
.class {
  color: black;
  background: white;
}
Selectors in Mixins
Mixins can contain more than just properties, they can contain selectors too.

For 举例：

.my-hover-mixin() {
  &:hover {
    border: 1px solid red;
  }
}
button {
  .my-hover-mixin();
}
编译后：

button:hover {
  border: 1px solid red;
}
Namespaces
If you want to mixin properties inside a more complicated selector, you can stack up multiple ids or classes.

#outer() {
  .inner {
    color: red;
  }
}

.c {
  #outer.inner();
}
Note: legacy Less syntax allows > and whitespace between namespaces and mixins. This syntax is deprecated and may be removed. Currently, these do the same thing.

#outer > .inner(); // deprecated
#outer .inner();   // deprecated
#outer.inner();    // preferred
Namespacing your mixins like this reduces conflicts with other library mixins or user mixins, but can also be a way to "organize" groups of mixins.

举例：

#my-library {
  .my-mixin() {
    color: black;
  }
}
// which can be used like this
.class {
  #my-library.my-mixin();
}
Guarded Namespaces
If a namespace has a guard, mixins defined by it are used only if the guard condition returns true. A namespace guard is evaluated exactly the same as a guard on a mixin, so the following two mixins work the same way:

#namespace when (@mode = huge) {
  .mixin() { /* */ }
}

#namespace {
  .mixin() when (@mode = huge) { /* */ }
}
The default function is assumed to have the same value for all nested namespaces and mixin. The following mixin is never evaluated; one of its guards is guaranteed to be false:

#sp_1 when (default()) {
  #sp_2 when (default()) {
    .mixin() when not(default()) { /* */ }
  }
}
The !important keyword
Use the !important keyword after mixin call to mark all properties inherited by it as !important:

举例：

.foo (@bg: #f5f5f5; @color: #900) {
  background: @bg;
  color: @color;
}
.unimportant {
  .foo();
}
.important {
  .foo() !important;
}
编译后：

.unimportant {
  background: #f5f5f5;
  color: #900;
}
.important {
  background: #f5f5f5 !important;
  color: #900 !important;
}

Parametric Mixins
Edit the markdown source for "mixins-parametric" 
How to pass arguments to mixins

Mixins can also take arguments, which are variables passed to the block of selectors when it is mixed in.

For 举例：

.border-radius(@radius) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}
And here's how we can mix it into various rulesets:

#header {
  .border-radius(4px);
}
.button {
  .border-radius(6px);
}
Parametric mixins can also have default values for their parameters:

.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}
We can invoke it like this now:

#header {
  .border-radius();
}
And it will include a 5px border-radius.

You can also use parametric mixins which don't take parameters. This is useful if you want to hide the ruleset from the CSS output, but want to include its properties in other rulesets:

.wrap() {
  text-wrap: wrap;
  white-space: -moz-pre-wrap;
  white-space: pre-wrap;
  word-wrap: break-word;
}

pre { .wrap() }
Which would 编译后：

pre {
  text-wrap: wrap;
  white-space: -moz-pre-wrap;
  white-space: pre-wrap;
  word-wrap: break-word;
}
Parameter separators
Parameters are currently either semicolon or comma separated.

Originally, parameters were only separated by commas, but the semi-colon was later added to support passing comma-separated list values to single arguments.

two arguments and each contains comma separated list: .name(1, 2, 3; something, else),
three arguments and each contains one number: .name(1, 2, 3),
use dummy semicolon to create mixin call with one argument containing comma separated css list: .name(1, 2, 3;),
comma separated default value: .name(@param1: red, blue;).
As of Less 4.0, you can wrap a list value using a paren escape [~()], e.g. .name(@param1: ~(red, blue)). This is similar to the quote escape syntax: ~"quote"
Overloading mixins
It is legal to define multiple mixins with the same name and number of parameters. Less will use properties of all that can apply. If you used the mixin with one parameter e.g. .mixin(green);, then properties of all mixins with exactly one mandatory parameter will be used:

.mixin(@color) {
  color-1: @color;
}
.mixin(@color, @padding: 2) {
  color-2: @color;
  padding-2: @padding;
}
.mixin(@color, @padding, @margin: 2) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}
.some .selector div {
  .mixin(#008000);
}
compiles into:

.some .selector div {
  color-1: #008000;
  color-2: #008000;
  padding-2: 2;
}
Named Parameters
A mixin reference can supply parameters values by their names instead of just positions. Any parameter can be referenced by its name and they do not have to be in any special order:

.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin: 20px; @color: #33acfe);
}
.class2 {
  .mixin(#efca44; @padding: 40px);
}
compiles into:

.class1 {
  color: #33acfe;
  margin: 20px;
  padding: 20px;
}
.class2 {
  color: #efca44;
  margin: 10px;
  padding: 40px;
}
The @arguments Variable
@arguments has a special meaning inside mixins, it contains all the arguments passed, when the mixin was called. This is useful if you don't want to deal with individual parameters:

.box-shadow(@x: 0, @y: 0, @blur: 1px, @color: #000) {
  -webkit-box-shadow: @arguments;
     -moz-box-shadow: @arguments;
          box-shadow: @arguments;
}
.big-block {
  .box-shadow(2px, 5px);
}
Which 编译后：

.big-block {
  -webkit-box-shadow: 2px 5px 1px #000;
     -moz-box-shadow: 2px 5px 1px #000;
          box-shadow: 2px 5px 1px #000;
}
Advanced Arguments and the @rest Variable
You can use ... if you want your mixin to take a variable number of arguments. Using this after a variable name will assign those arguments to the variable.

.mixin(...) {        // matches 0-N arguments
.mixin() {           // matches exactly 0 arguments
.mixin(@a: 1) {      // matches 0-1 arguments
.mixin(@a: 1, ...) { // matches 0-N arguments
.mixin(@a, ...) {    // matches 1-N arguments
Furthermore:

.mixin(@a, @rest...) {
   // @rest is bound to arguments after @a
   // @arguments is bound to all arguments
}
Pattern-matching
Sometimes, you may want to change the behavior of a mixin, based on the parameters you pass to it. Let's start with something basic:

.mixin(@s, @color) { ... }

.class {
  .mixin(@switch, #888);
}
Now let's say we want .mixin to behave differently, based on the value of @switch, we could define .mixin as such:

.mixin(dark, @color) {
  color: darken(@color, 10%);
}
.mixin(light, @color) {
  color: lighten(@color, 10%);
}
.mixin(@_, @color) {
  display: block;
}
Now, if we run:

@switch: light;

.class {
  .mixin(@switch, #888);
}
We will get the following CSS:

.class {
  color: #a2a2a2;
  display: block;
}
Where the color passed to .mixin was lightened. If the value of @switch was dark, the result would be a darker color.

Here's what happened:

The first mixin definition didn't match because it expected dark as the first argument.
The second mixin definition matched, because it expected light.
The third mixin definition matched because it expected any value.
Only mixin definitions which matched were used. Variables match and bind to any value. Anything other than a variable matches only with a value equal to itself.

We can also match on arity, here's an 举例：

.mixin(@a) {
  color: @a;
}
.mixin(@a, @b) {
  color: fade(@a, @b);
}
Now if we call .mixin with a single argument, we will get the output of the first definition, but if we call it with two arguments, we will get the second definition, namely @a faded to @b.


Using Mixins as Functions
Edit the markdown source for "mixins-as-functions" 
Selecting properties and variables from mixin calls

Property / value accessors
Released v3.5.0

Starting in Less 3.5, you can use property/variable accessors to select a value from an evaluated mixin's rules. This can allow you to use mixins similar to functions.

举例：

.average(@x, @y) {
  @result: ((@x + @y) / 2);
}

div {
  // call a mixin and look up its "@result" value
  padding: .average(16px, 50px)[@result];
}
编译后：

div {
  padding: 33px;
}
Overriding mixin values
If you have multiple matching mixins, all rules are evaluated and merged, and the last matching value with that identifier is returned. This is similar to the cascade in CSS, and it allows you to "override" mixin values.

// library.less
#library() {
  .mixin() {
    prop: foo;
  }
}

// customize.less
@import "library";
#library() {
  .mixin() {
    prop: bar;
  }
}

.box {
  my-value: #library.mixin[prop];
}
编译后：:

.box {
  my-value: bar;
}
Unnamed lookups
If you don't specify a lookup value in [@lookup] and instead write [] after a mixin or ruleset call, all values will cascade and the last declared value will be selected.

Meaning: the averaging mixin from the above example could be written as:

.average(@x, @y) {
  @result: ((@x + @y) / 2);
}

div {
  // call a mixin and look up its final value
  padding: .average(16px, 50px)[];
}
The output is the same:

div {
  padding: 33px;
}
The same cascading behavior is true for rulesets or variables aliased to mixin calls.

@dr: {
  value: foo;
}
.box {
  my-value: @dr[];
}
This 编译后：:

.box {
  my-value: foo;
}
Unlocking mixins & variables into caller scope
DEPRECATED - Use Property / Value Accessors

Variables and mixins defined in a mixin are visible and can be used in caller's scope. There is only one exception: a variable is not copied if the caller contains a variable with the same name (that includes variables defined by another mixin call). Only variables present in callers local scope are protected. Variables inherited from parent scopes are overridden.

Note: this behavior is deprecated, and in the future, variables and mixins will not be merged into the caller scope in this way.

举例：

.mixin() {
  @width:  100%;
  @height: 200px;
}

.caller {
  .mixin();
  width:  @width;
  height: @height;
}

编译后：

.caller {
  width:  100%;
  height: 200px;
}
Variables defined directly in callers scope cannot be overridden. However, variables defined in callers parent scope is not protected and will be overridden:

.mixin() {
  @size: in-mixin;
  @definedOnlyInMixin: in-mixin;
}

.class {
  margin: @size @definedOnlyInMixin;
  .mixin();
}

@size: globaly-defined-value; // callers parent scope - no protection
编译后：

.class {
  margin: in-mixin in-mixin;
}
Finally, mixin defined in mixin acts as return value too:

.unlock(@value) { // outer mixin
  .doSomething() { // nested mixin
    declaration: @value;
  }
}

#namespace {
  .unlock(5); // unlock doSomething mixin
  .doSomething(); //nested mixin was copied here and is usable
}
编译后：

#namespace {
  declaration: 5;
}

Recursive Mixins
Edit the markdown source for "mixin-loops" 
Creating loops

In Less a mixin can call itself. Such recursive mixins, when combined with Guard Expressions and Pattern Matching, can be used to create various iterative/loop structures.

举例：

.loop(@counter) when (@counter > 0) {
  .loop((@counter - 1));    // next iteration
  width: (10px * @counter); // code for each iteration
}

div {
  .loop(5); // launch the loop
}
编译后：

div {
  width: 10px;
  width: 20px;
  width: 30px;
  width: 40px;
  width: 50px;
}
A generic example of using a recursive loop to generate CSS grid classes:

.generate-columns(4);

.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);
  }
  .generate-columns(@n, (@i + 1));
}
编译后：

.column-1 {
  width: 25%;
}
.column-2 {
  width: 50%;
}
.column-3 {
  width: 75%;
}
.column-4 {
  width: 100%;
}

Mixin Guards
Edit the markdown source for "mixin-guards" 
Guards are useful when you want to match on expressions, as opposed to simple values or arity. If you are familiar with functional programming, you have probably encountered them already.

In trying to stay as close as possible to the declarative nature of CSS, Less has opted to implement conditional execution via guarded mixins instead of if/else statements, in the vein of @media query feature specifications.

Let's start with an 举例：

.mixin(@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin(@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin(@a) {
  color: @a;
}
The key is the when keyword, which introduces a guard sequence (here with only one guard). Now if we run the following code:

.class1 { .mixin(#ddd) }
.class2 { .mixin(#555) }
Here's what we'll get:

.class1 {
  background-color: black;
  color: #ddd;
}
.class2 {
  background-color: white;
  color: #555;
}
Guard Comparison Operators
The full list of comparison operators usable in guards are: >, >=, =, =<, <. Additionally, the keyword true is the only truthy value, making these two mixins equivalent:

.truth(@a) when (@a) { ... }
.truth(@a) when (@a = true) { ... }
Any value other than the keyword true is falsy:

.class {
  .truth(40); // Will not match any of the above definitions.
}
Note that you can also compare arguments with each other, or with non-arguments:

@media: mobile;

.mixin(@a) when (@media = mobile) { ... }
.mixin(@a) when (@media = desktop) { ... }

.max(@a; @b) when (@a > @b) { width: @a }
.max(@a; @b) when (@a < @b) { width: @b }
Guard Logical Operators
You can use logical operators with guards. The syntax is based on CSS media queries.

Use the and keyword to combine guards:

.mixin(@a) when (isnumber(@a)) and (@a > 0) { ... }
You can emulate the or operator by separating guards with a comma ,. If any of the guards evaluate to true, it's considered a match:

.mixin(@a) when (@a > 10), (@a < -10) { ... }
Use the not keyword to negate conditions:

.mixin(@b) when not (@b > 0) { ... }
Type Checking Functions
Lastly, if you want to match mixins based on value type, you can use the is functions:

.mixin(@a; @b: 0) when (isnumber(@b)) { ... }
.mixin(@a; @b: black) when (iscolor(@b)) { ... }
Here are the basic type checking functions:

iscolor
isnumber
isstring
iskeyword
isurl
If you want to check if a value is in a specific unit in addition to being a number, you may use one of:

ispixel
ispercentage
isem
isunit

Aliasing Mixins
Edit the markdown source for "mixins-aliasing" 
Released v3.5.0

Assigning mixin calls to a variable

Mixins can be assigned to a variable to be called as a variable call, or can be used for map lookup.

#theme.dark.navbar {
  .colors(light) {
    primary: purple;
  }
  .colors(dark) {
    primary: black;
    secondary: grey;
  }
}

.navbar {
  @colors: #theme.dark.navbar.colors(dark);
  background: @colors[primary];
  border: 1px solid @colors[secondary];
}
This would 编译后：

.navbar {
  background: black;
  border: 1px solid grey;
}
Variable calls
Entire mixin calls can be aliased and called as variable calls. As in:

#library() {
  .colors() {
    background: green;
  }
}
.box {
  @alias: #library.colors();
  @alias();
}
编译后：:

.box {
  background: green;
}
Note, unlike mixins used in root, mixin calls assigned to variables and called with no arguments always require parentheses. The following is not valid.

#library() {
  .colors() {
    background: green;
  }
}
.box {
  @alias: #library.colors;
  @alias();   // ERROR: Could not evaluate variable call @alias
}
This is because it's ambiguous if variable is assigned a list of selectors or a mixin call. For example, in Less 3.5+, this variable could be used this way.

.box {
  @alias: #library.colors;
  @{alias} {
    a: b;
  }
}
The above would 编译后：

.box #library.colors {
  a: b;
}

CSS Guards
Edit the markdown source for "css-guards" 
"if"'s around selectors

Released v1.5.0

Like Mixin Guards, guards can also be applied to css selectors, which is syntactic sugar for declaring the mixin and then calling it immediately.

For instance, before 1.5.0 you would have had to do this:

.my-optional-style() when (@my-option = true) {
  button {
    color: white;
  }
}
.my-optional-style();
Now, you can apply the guard directly to a style.

button when (@my-option = true) {
  color: white;
}
You can also achieve an if type statement by combining this with the & feature, allowing you to group multiple guards.

& when (@my-option = true) {
  button {
    color: white;
  }
  a {
    color: blue;
  }
}
Note that you can also achieve a similar pattern by using the actual if() function and a variable call. As in:

@dr: if(@my-option = true, {
  button {
    color: white;
  }
  a {
    color: blue;
  }
});
@dr();

Detached Rulesets
Edit the markdown source for "detached-rulesets" 
Assign a ruleset to a variable

Released v1.7.0

A detached ruleset is a group of css properties, nested rulesets, media declarations or anything else stored in a variable. You can include it into a ruleset or another structure and all its properties are going to be copied there. You can also use it as a mixin argument and pass it around as any other variable.

Simple 举例：

// declare detached ruleset
@detached-ruleset: { background: red; }; // semi-colon is optional in 3.5.0+

// use detached ruleset
.top {
    @detached-ruleset(); 
}
compiles into:

.top {
  background: red;
}
Parentheses after a detached ruleset call are mandatory (except when followed by a lookup value). The call @detached-ruleset; would not work.

It is useful when you want to define a mixin that abstracts out either wrapping a piece of code in a media query or a non-supported browser class name. The rulesets can be passed to mixin so that the mixin can wrap the content, e.g.

.desktop-and-old-ie(@rules) {
  @media screen and (min-width: 1200px) { @rules(); }
  html.lt-ie9 &                         { @rules(); }
}

header {
  background-color: blue;

  .desktop-and-old-ie({
    background-color: red;
  });
}
Here the desktop-and-old-ie mixin defines the media query and root class so that you can use a mixin to wrap a piece of code. This will output

header {
  background-color: blue;
}
@media screen and (min-width: 1200px) {
  header {
    background-color: red;
  }
}
html.lt-ie9 header {
  background-color: red;
}
A ruleset can be now assigned to a variable or passed in to a mixin and can contain the full set of Less features, e.g.

@my-ruleset: {
    .my-selector {
      background-color: black;
    }
  };
You can even take advantage of media query bubbling, for instance

@my-ruleset: {
    .my-selector {
      @media tv {
        background-color: black;
      }
    }
  };
@media (orientation:portrait) {
    @my-ruleset();
}
which will output

@media (orientation: portrait) and tv {
  .my-selector {
    background-color: black;
  }
}
A detached ruleset call unlocks (returns) all its mixins into caller the same way as mixin calls do. However, it does not return variables.

Returned mixin:

// detached ruleset with a mixin
@detached-ruleset: { 
    .mixin() {
        color: blue;
    }
};
// call detached ruleset
.caller {
    @detached-ruleset(); 
    .mixin();
}
编译后：

.caller {
  color: blue;
}
Private variables:

@detached-ruleset: { 
    @color:blue; // this variable is private
};
.caller {
    color: @color; // syntax error
}
Scoping
A detached ruleset can use all variables and mixins accessible where it is defined and where it is called. Otherwise said, both definition and caller scopes are available to it. If both scopes contains the same variable or mixin, declaration scope value takes precedence.

Declaration scope is the one where detached ruleset body is defined. Copying a detached ruleset from one variable into another cannot modify its scope. The ruleset does not gain access to new scopes just by being referenced there.

Lastly, a detached ruleset can gain access to scope by being unlocked (imported) into it.

Note: unlocking variables into scope via a called mixin is deprecated. Use property / variable accessors.

Definition and Caller Scope Visibility
A detached ruleset sees the caller's variables and mixins:

@detached-ruleset: {
  caller-variable: @caller-variable; // variable is undefined here
  .caller-mixin(); // mixin is undefined here
};

selector {
  // use detached ruleset
  @detached-ruleset(); 

  // define variable and mixin needed inside the detached ruleset
  @caller-variable: value;
  .caller-mixin() {
    variable: declaration;
  }
}
compiles into:

selector {
  caller-variable: value;
  variable: declaration;
}
Variable and mixins accessible from definition win over those available in the caller:

@variable: global;
@detached-ruleset: {
  // will use global variable, because it is accessible
  // from detached-ruleset definition
  variable: @variable; 
};

selector {
  @detached-ruleset();
  @variable: value; // variable defined in caller - will be ignored
}
compiles into:

selector {
  variable: global;
}
Referencing Won't Modify Detached Ruleset Scope
A ruleset does not gain access to new scopes just by being referenced there:

@detached-1: { scope-detached: @one @two; };
.one {
  @one: visible;
  .two {
    @detached-2: @detached-1; // copying/renaming ruleset 
    @two: visible; // ruleset can not see this variable
  }
}

.use-place {
  .one > .two(); 
  @detached-2();
}
throws an error:

ERROR 1:32 The variable "@one" was not declared.
Unlocking Will Modify Detached Ruleset Scope
A detached ruleset gains access by being unlocked (imported) inside a scope:

#space {
  .importer-1() {
    @detached: { scope-detached: @variable; }; // define detached ruleset
  }
}

.importer-2() {
  @variable: value; // unlocked detached ruleset CAN see this variable
  #space > .importer-1(); // unlock/import detached ruleset
}

.use-place {
  .importer-2(); // unlock/import detached ruleset second time
   @detached();
}
compiles into:

.use-place {
  scope-detached: value;
}
Property / variable accessors
(Lookup values)
Released v3.5.0

Starting in Less 3.5, you can use property/variable accessors (also called "lookups") to select a value from variable (detached) rulesets.

@config: {
  option1: true;
  option2: false;
}

.mixin() when (@config[option1] = true) {
  selected: value;
}

.box {
  .mixin();
}
编译后：:

.box {
  selected: value;
}
If what is returned from a lookup is another detached ruleset, you can use a second lookup to get that value.

@config: {
  @colors: {
    primary: blue;
  }
}

.box {
  color: @config[@colors][primary];
}
Variable variables in lookups
The lookup value that is returned can itself be variable. As in, you can write:

@config: {
  @dark: {
    primary: darkblue;
  }
  @light: {
    primary: lightblue;
  }
}

.box {
  @lookup: dark;
  color: @config[@@lookup][primary];
}
This will 编译后：

.box {
  color: darkblue;
}

@import At-Rules
Edit the markdown source for "imports" 
Import styles from other style sheets

In standard CSS, @import at-rules must precede all other types of rules. But Less doesn't care where you put @import statements.

举例：

.foo {
  background: #900;
}
@import "this-is-valid.less";
File Extensions
@import statements may be treated differently by Less depending on the file extension:

If the file has a .css extension it will be treated as CSS and the @import statement left as-is (see the inline option below).
If it has any other extension it will be treated as Less and imported.
If it does not have an extension, .less will be appended and it will be included as a imported Less file.
Examples:

@import "foo";      // foo.less is imported
@import "foo.less"; // foo.less is imported
@import "foo.php";  // foo.php imported as a Less file
@import "foo.css";  // statement left in place, as-is
The following options can be used to override this behavior.

Import Options
Less offers several extensions to the CSS @import CSS at-rule to provide more flexibility over what you can do with external files.

Syntax: @import (keyword) "filename";

The following import options have been implemented:

reference: use a Less file but do not output it
inline: include the source file in the output but do not process it
less: treat the file as a Less file, no matter what the file extension
css: treat the file as a CSS file, no matter what the file extension
once: only include the file once (this is default behavior)
multiple: include the file multiple times
optional: continue compiling when file is not found
More than one keyword per @import is allowed, you will have to use commas to separate the keywords:

举例： @import (optional, reference) "foo.less";

reference
Use @import (reference) to import external files, but without adding the imported styles to the compiled output unless referenced.

Released v1.5.0

举例： @import (reference) "foo.less";

Imagine that reference marks every at-rule and selector with a reference flag in the imported file, imports as normal, but when the CSS is generated, "reference" selectors (as well as any media queries containing only reference selectors) are not output. reference styles will not show up in your generated CSS unless the reference styles are used as mixins or extended.

Additionally, reference produces different results depending on which method was used (mixin or extend):

extend: When a selector is extended, only the new selector is marked as not referenced, and it is pulled in at the position of the reference @import statement.
mixins: When a reference style is used as an implicit mixin, its rules are mixed-in, marked "not reference", and appear in the referenced place as normal.
reference example
This allows you to pull in only specific, targeted styles from a library such as Bootstrap by doing something like this:

.navbar:extend(.navbar all) {}
And you will pull in only .navbar related styles from Bootstrap.

inline
Use @import (inline) to include external files, but not process them.

Released v1.5.0

举例： @import (inline) "not-less-compatible.css";

You will use this when a CSS file may not be Less compatible; this is because although Less supports most known standards CSS, it does not support comments in some places and does not support all known CSS hacks without modifying the CSS.

So you can use this to include the file in the output so that all CSS will be in one file.

less
Use @import (less) to treat imported files as Less, regardless of file extension.

Released v1.4.0

举例：

@import (less) "foo.css";
css
Use @import (css) to treat imported files as regular CSS, regardless of file extension. This means the import statement will be left as it is.

Released v1.4.0

举例：

@import (css) "foo.less";
编译后：

@import "foo.less";
once
The default behavior of @import statements. It means the file is imported only once and subsequent import statements for that file will be ignored.

Released v1.4.0

This is the default behavior of @import statements.

举例：

@import (once) "foo.less";
@import (once) "foo.less"; // this statement will be ignored
multiple
Use @import (multiple) to allow importing of multiple files with the same name. This is the opposite behavior to once.

Released v1.4.0

举例：

// file: foo.less
.a {
  color: green;
}
// file: main.less
@import (multiple) "foo.less";
@import (multiple) "foo.less";
编译后：

.a {
  color: green;
}
.a {
  color: green;
}
optional
Use @import (optional) to allow importing of a file only when it exists. Without the optional keyword Less throws a FileError and stops compiling when importing a file that can not be found.

Released v2.3.0


@plugin At-Rules
Edit the markdown source for "plugins" 
Released v2.5.0

Import JavaScript plugins to add Less.js functions and features

Writing your first plugin
Using a @plugin at-rule is similar to using an @import for your .less files.

@plugin "my-plugin";  // automatically appends .js if no extension
Since Less plugins are evaluated within the Less scope, the plugin definition can be quite simple.

registerPlugin({
    install: function(less, pluginManager, functions) {
        functions.add('pi', function() {
            return Math.PI;
        });
    }
})
or you can use module.exports (shimmed to work in browser as well as Node.js).

module.exports = {
    install: function(less, pluginManager, functions) {
        functions.add('pi', function() {
            return Math.PI;
        });
    }
};
Note that other Node.js CommonJS conventions, like require() are not available in the browser. Keep this in mind when writing cross-platform plugins.

What can you do with a plugin? A lot, but let's start with the basics. We'll focus first on what you might put inside the install function. Let's say you write this:

// my-plugin.js
install: function(less, pluginManager, functions) {
    functions.add('pi', function() {
        return Math.PI;
    });
}
// etc
Congratulations! You've written a Less plugin!

If you were to use this in your stylesheet:

@plugin "my-plugin";
.show-me-pi {
  value: pi();
}
You would get:

.show-me-pi {
  value: 3.141592653589793;
}
However, you would need to return a proper Less node if you wanted to, say, multiply that against other values or do other Less operations. Otherwise the output in your stylesheet is plain text (which may be fine for your purposes).

Meaning, this is more correct:

functions.add('pi', function() {
    return new tree.Dimension(Math.PI);
});
Note: A dimension is a number with or without a unit, like "10px", which would be less.Dimension(10, "px"). For a list of units, see the Less API.

Now you can use your function in operations.

@plugin "my-plugin";
.show-me-pi {
  value: pi() * 2;
}
You may have noticed that there are available globals for your plugin file, namely a function registry (functions object), and the less object. These are there for convenience.

Plugin Scope
Functions added by a @plugin at-rule adheres to Less scoping rules. This is great for Less library authors that want to add functionality without introducing naming conflicts.

For instance, say you have 2 plugins from two third-party libraries that both have a function named "foo".

// lib1.js
// ...
    functions.add('foo', function() {
        return "foo";
    });
// ...

// lib2.js
// ...
    functions.add('foo', function() {
        return "bar";
    });
// ...
That's ok! You can choose which library's function creates which output.

.el-1 {
    @plugin "lib1";
    value: foo();
}
.el-2 {
    @plugin "lib2";
    value: foo();
}
This will produce:

.el-1 {
    value: foo;
}
.el-2 {
    value: bar;
}
For plugin authors sharing their plugins, that means you can also effectively make private functions by placing them in a particular scope. As in, this will cause an error:

.el {
    @plugin "lib1";
}
@value: foo();
As of Less 3.0, functions can return any kind of Node type, and can be called at any level.

Meaning, this would throw an error in 2.x, as functions had to be part of the value of a property or variable assignment:

.block {
    color: blue;
    my-function-rules();
}
In 3.x, that's no longer the case, and functions can return At-Rules, Rulesets, any other Less node, strings, and numbers (the latter two are converted to Anonymous nodes).

Null Functions
There are times when you may want to call a function, but you don't want anything output (such as storing a value for later use). In that case, you just need to return false from the function.

var collection = [];

functions.add('store', function(val) {
    collection.push(val);  // imma store this for later
    return false;
});
@plugin "collections";
@var: 32;
store(@var);
Later you could do something like:

functions.add('retrieve', function(val) {
    return new tree.Value(collection);
});
.get-my-values {
    @plugin "collections";
    values: retrieve();   
}
The Less.js Plugin Object
A Less.js plugin should export an object that has one or more of these properties.

{
    /* Called immediately after the plugin is 
     * first imported, only once. */
    install: function(less, pluginManager, functions) { },

    /* Called for each instance of your @plugin. */
    use: function(context) { },

    /* Called for each instance of your @plugin, 
     * when rules are being evaluated.
     * It's just later in the evaluation lifecycle */
    eval: function(context) { },

    /* Passes an arbitrary string to your plugin 
     * e.g. @plugin (args) "file";
     * This string is not parsed for you, 
     * so it can contain (almost) anything */
    setOptions: function(argumentString) { },

    /* Set a minimum Less compatibility string
     * You can also use an array, as in [3, 0] */
    minVersion: ['3.0'],

    /* Used for lessc only, to explain 
     * options in a Terminal */
    printUsage: function() { },

}
The PluginManager instance for the install() function provides methods for adding visitors, file managers, and post-processors.

Here are some example repos showing the different plugin types.
post-processor: https://github.com/less/less-plugin-clean-css
visitor: https://github.com/less/less-plugin-inline-urls
file-manager: https://github.com/less/less-plugin-npm-import
Pre-Loaded Plugins
While a @plugin call works well for most scenarios, there are times when you might want to load a plugin before parsing starts.

See: Pre-Loaded Plugins in the "Using Less.js" section for how to do that.


Maps (NEW!)
Edit the markdown source for "maps" 
Released v3.5.0

Use rulesets and mixins as maps of values

By combining namespacing with the lookup [] syntax, you can turn your rulesets / mixins into maps.

@sizes: {
  mobile: 320px;
  tablet: 768px;
  desktop: 1024px;
}

.navbar {
  display: block;

  @media (min-width: @sizes[tablet]) {
    display: inline-block;
  }
}
编译后：:

.navbar {
  display: block;
}
@media (min-width: 768px) {
  .navbar {
    display: inline-block;
  }
}
Mixins are a little more versatile as maps because of namespacing and the ability to overload mixins.

#library() {
  .colors() {
    primary: green;
    secondary: blue;
  }
}

#library() {
  .colors() { primary: grey; }
}

.button {
  color: #library.colors[primary];
  border-color: #library.colors[secondary];
}
编译后：:

.button {
  color: grey;
  border-color: blue;
}
You can also make this easier by aliasing mixins. That is:

.button {
  @colors: #library.colors();
  color: @colors[primary];
  border-color: @colors[secondary];
}
Note, if a lookup value produces another ruleset, you can append a second [] lookup, as in:

@config: {
  @options: {
    library-on: true
  }
}

& when (@config[@options][library-on] = true) {
  .produce-ruleset {
    prop: val;
  }
}
In this way, rulesets and variable calls can emulate a type of "namespacing", similar to mixins.

As far as whether to use mixins or rulesets assigned to variables as maps, it's up to you. You may want to replace entire maps by re-declaring a variable assigned to a rulset. Or you may want to "merge" individual key/value pairs, in which case mixins as maps might be more appropriate.

Using variable variables in lookups
One important thing to notice is that the value in [@lookup] is the key (variable) name @lookup, and is not evaluated as a variable. If you want the key name itself to be variable, you can use the @@variable syntax.

E.g.

.foods() {
  @dessert: ice cream;
}

@key-to-lookup: dessert;

.lunch {
  treat: .foods[@@key-to-lookup];
}
This would 编译后：

.lunch {
  treat: ice cream;
}

