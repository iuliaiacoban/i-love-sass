# Getting started with Sass

##Setup
##Gulp and Sass
##Syntax. Features

- [variables](#variables)
- [partials](#partials)
- [mixins](#mixins)
- [media queries](#media-queries)
- [operators](#operators)
- [functions](#functions)
- [inheritance](#inheritance)
- [conditional directives](#conditional-directives)
- [loops](#loops)


#Variables
- variables are used to store information for further reference
- in Sass, a variable is declared with a $ sign where the value is specified after a : colon
- when the scss is compiled, any reference to $variable-name is substitued for whatever is stored in the variable
- Sass can handle different types of variables: colors, lists, strings, numbers, boolean, map of key-value pairs 

```CSS
//Declaration
$primary-color: #fff;
$font: Helvetica, Arial, sans-serif;
$content: 'hi!';
$padding: 20px;
$hide-for-mobile: false;
$colors: (
	red: #f00,
	green: #cc3f85,
	blue: #045b9d,
);

//Usage
body {
	color: $primary-color;
}

//Output
body {
	color: #fff;
}
```

#Partials
Having a single CSS file leads to different issues such as: the time spent for searching a selector or the conflicts during changes made by 2 developers on the same time. Sass help us to structure the style by splitting the specific style into many small files named partials. 

A partial starts with an _ sign and lets Sass know that the files should not be generated into a CSS file. Partials have to be included in the main file with the @import directive. This is a nice way to modularize the CSS and help us to keep things easier to maintain.

```CSS
style.scss
----------
@import 'base';
@import 'colors';
@import 'buttons';
```

#Mixins
- when we want to reuse blocks of style in different places, we can use mixins or placeholders
- you can think of mixin as a function, with CSS content, with or without arguments
- placeholder is similar to a class declaration, except you have to declare it with % symbol and it won't be compiled in the CSS final file unless it has been extended (@extend).

```CSS
//Mixin declaration with arguments
@mixin border-radius($radius) {
	border-radius: $radius;
}

//Usage
.button {
	@include border-radius(10px);
}

//Output
.button {
	border-radius: 10px;
}

```
If you need variables the best advice would be to use mixins, otherwise use placeholders (placeholders do not know to work with passed variables). 
Sass will duplicate the output of the mixin and in this case the stylesheet will get bigger.

```CSS
//Mixin declaration
@mixin vertical-align {
    position: absolute;
    top: 50%;
    transform: translateY(-50%); 
}

//Mixin usage
.homepage-intro {
    @include vertical-align;
}

.homepage-gallery {
    @include vertical-align;
}

//Output
.homepage-intro {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}

.homepage-gallery {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}

//Placeholder declaration
%vertical-align {
    position: absolute;
    top: 50%;
    transform: translateY(-50%); 
}

//Placeholder usage
.homepage-intro {
    @extend vertical-align;
}

.homepage-gallery {
    @extend vertical-align;
}

//Output
.homepage-intro, homepage-gallery {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
```

#Media queries
As we know Sass allows variables to be interpolated. This means that we can move our media queries into variables and reuse them.
```CSS
$break-small: 320px;

p {
    font-size: 16px;

    @media screen and (max-width: $break-small) {
        font-size: 18px;
    }
}
```

To write code in a modular way we can use mixins, to group the style that we want to reuse it wherever we want, in this way:
```CSS
//Declaration
$break-small-portrait: 320px;
$break-medium-portrait: 480px;
$break-small-landscape: 750px;
$break-medium: 1024px;
$break-large: 1124px;

@mixin respond-to($media) {
    @if $media == small-phones {
        @media only screen and (min-width: $break-small-portrait) { @content; }
    }

    @if $media == medium-phones {
        @media only screen and (min-width: $break-medium-portrait) and (max-width: $break-small-landscape) { @content; }
    }

    @if $media == phones {
        @media only screen and (min-width: $break-small-portrait) and (max-width: $break-small-landscape) { @content; }
    }

    @else if $media == tablets {
        @media only screen and (min-width: $break-small-landscape + 1) and (max-width: $break-medium - 1) { @content; }
    }

    @else if $media == tablets-landscape {
        @media only screen and (min-width: $break-small-landscape + 1) and (max-width: $break-medium) and (orientation: landscape) { @content; }
    }

    @else if $media == phones-tablets {
        @media only screen and (min-width: $break-small-portrait) and (max-width: $break-medium - 1) { @content; }
    }

    @else if $media == wide-screens {
        @media only screen and (min-width: $break-large) { @content; }
    }
}

//Usage
p {
    font-size: 16px;
    @include respond-to(phones) {
        font-size: 18px;
    }
}
```

###Issues
There are several small issues related to this directive. For example  @extend doesn't behave properly inside @media.
```CSS
.red {
    color: red;
}
  
@media only screen and (max-width : 320px) {
    .blue {
        @extend .red;
	    margin: 10px;
    }
}
```
we will get 
```CCC
.red, .blue {
    color: red;
}

@media only screen and (max-width: 320px) {
    .blue {
        margin: 10px;
    }
}
```

#Operators
As in javascript we can do different operations in sass. Below are defined the operators that Sass supports: 
###a. Arithmetic
- used to perform arithmetic operations
- operators: +, -, *, /, &, % 
```CSS
h1 {
    font-size: 5px + 2em;    // error incompatible units
    font-size: 5px + 2;      // 7px
    font-size: 5px * 2px;    // 10px*px => invalid CSS
    font-size: 16px / 24px   // Outputs as CSS but
    font-size: (16px / 24px) // if you use parentheses, it will do a division operation
}
```

Other examples:
```CSS
#333 + #777 		// #aaaaaa
#090807 - ##030201      // #060606
#222 * #040404 		// #888
(222 => [22][22][22] * [04][04][04] => [8][8][8] => 888888) [r][g][b]
```
```CSS
Double ampersand:
.button {
	& + & { }    //Output: .button + .button { }
}
```
This slector will match any .button class that immediately follows another .buttons class. The trick can be used when we want to set a margin/padding to a group of buttons and we don't want to use :first-child and :last-child.
 
###b. Assignment :
- used for declaring variables
```CSS
$main-color: lightgray;
```

###c. Equality ==, !=
- used in conditional statements to test wheter 2 values are the same or not
- Sass doesn't support ===. In Sass ("5" == 5) returns false but in JS it will return true

```CSS
@mixin font-fl($font) {
    &:after {
        @if(type-of($font) == string) {
            content: 'My font is: #{$font}.';
        } @else {
            content: 'Sorry, the argument #{$font} is a #{type-of($font)}.';
        }
    }
}

h2{
    @include font-fl(sans-serif);
}
```
Output:
```CSS
h2:after {
    content: 'My font is: sans-serif.';
}
```

###d. Comparison
```CSS	
$padding: 50px;

h2 {
    @if($padding <= 20px) {
        padding: $padding;
    } @else {
        padding: $padding / 2;
    }
}
```
Output:
```CSS
h2 {
    padding: 25px;
}
```

###e. Logical operators: and, or, not
- used to test multiple conditions within a conditional expression

```CSS
$list-map: (success: lightgreen, alert: tomato, info: lightblue);

@mixin button-state($btn-state) {
    @if (length($list-map) > 2 or length($list-map) < 5) {
        background-color: map-get($list-map, $btn-state);
    }
}

.btn {
    @include button-state(success);
}
```

#Functions
###a. Built-in functions
A full list with all functions can be found here: http://sass-lang.com/documentation/Sass/Script/Functions.html
```CSS
p {
  color: hsl(0, 100%, 50%); //color: #ff0000
  border-bottom: 1px solid opacify(#fefefe, .5);
}
```
###b. Custom functions
- @function and @return directives

```CSS
@function em($pixels, $context: 16px) {
    @return ($pixels / $context) * 1em;
}

font-size: em(18px); // fonst-zize: 1.125em;
```
