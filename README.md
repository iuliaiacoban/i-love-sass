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
