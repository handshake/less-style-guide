# Less Style Guide

This guide presents a collection of best-practices and coding conventions for the [Less][less] language.

This guide is intended to be community-driven, and contributions are highly encouraged.

Please note that this is a work-in-progress: there is much more that can be specified, and some of the guidelines that have been specified may not be deemed to be idiomatic by the community (in which case, these offending guidelines will be modified or removed, as appropriate).

## Inspiration

The details in this guide have been very heavily inspired by several existing style guides and other resources. In particular:

- [Github][githubcss]: Github's CSS Styleguide
- [KSS][kss]: A methodology for documenting CSS and generating styleguides
- [Idiomatic CSS][idiomatic]: Principles of writing consistent, idiomatic CSS

## Table of Contents

* [The Less Style Guide](#guide)
    * [Layout](#layout)
        * [Tabs or Spaces?](#tabs_or_spaces)
        * [Maximum Line Length](#maximum_line_length)
        * [Blank Lines](#blank_lines)
        * [Whitespace](#whitespace)
        * [Trailing Whitespace](#trailing_whitespace)
        * [Encoding](#encoding)
    * [Imports](#imports)
    * [Mixins](#mixins)
        * [Style Mixins](#style_mixins)
        * [Helpers](#helpers)
        * [Factories](#factories)
    * [Ordering](#ordering)
    * [Nesting](#nesting)
    * [Comments](#comments)
        * [Block Comments](#block_comments)
        * [Inline Comments](#inline_comments)
    * [Naming Conventions](#naming_conventions)
    * [Strings](#strings)
    * [Color](#color)
    * [Documentation](#documentation)
    * [Miscellaneous](#misc)

<a name="layout"/>
## Code layout

<a name="tabs_or_spaces"/>
### Tabs or Spaces?

Use **spaces only**, with **4 spaces** per indentation level. Never mix tabs and spaces.

<a name="maximum_line_length"/>
### Maximum Line Length

Limit all lines to a maximum of 100 characters.

<a name="blank_lines"/>
### Blank Lines

Separate all rulesets with a single blank line.

Separate nested rulesets from properties mixins with a single blank line.

Separate style mixins from properties with a single blank line.

Do **not** separate helper mixins from properties.

```less
.foo {
    .bar(); // A style mixin

    background-color: #f00;
    .border-radius( 5px ); // a helper mixin
    width: 50%;

    // A nested ruleset:
    .baz {

    }
}
```

<a name="whitespace"/>
### Whitespace

Separate the opening brace of a ruleset from a selector or mixin name with a single space.

```less
.foo{} // No
.foo {} // Yes
```

Do not separate a mixin name from its parameter list.

```less
.foo () {} // No
.foo() {} // Yes
```

Separate a property from a value with a single space after the colon.

```less
width : 5px; // No
width :5px; // No
width:5px; // No
width: 5px; // Yes
```

Pad the inside of a mixin parameter list or inline ruleset with a single space at each end.

```less
.foo(@width: 5px) {width: @width;} // No
.foo( @width: 5px ) { width: @width; } // Yes
```

Also pad the inside of arguments to mixins and functions (both less and css)

```less
.foo( 10px );
color: hsla( 120, 50%, 75%, 75% );
```

Avoid extraneous whitespace in the following situations:

- Immediately inside parentheses, brackets or braces

```less
( 4px * 2 ) // No
(4px * 2) // Yes
data[ foo = "bar" ] // No
data[foo = "bar"] // Yes
.foo-@{ bar } // No
.foo-@{bar} // Yes
```

- Immediately before a comma or colon

```less
.foo( 4px , 10px ); // No
.foo( 4px, 10px ); // Yes
.foo {
    width : 4px; // No
    width: 4px; // Yes
}
```

<a name="trailing_whitespace"/>
### Trailing Whitespace

Do not include trailing whitespace on any lines.

<a name="encoding"/>
### Encoding

UTF-8 is the preferred source file encoding.

<a name="imports"/>
## Imports

Each @import statement should be placed on separate lines.

All @import statements should be at the top of the file.

Unless required due to overrides, order all @import statements in alphabetical order.
Comment the exceptions.

<a name="mixins"/>
## Mixins

A [mixin][mixin] is a Less specific feature. [Sass][sass] has something very similar.

If you're new to Less, think of mixin's as functions.

For our purposes, we partition all mixins into three categories, based on usage.

All mixins should have a parameter list, even if it is empty, to indicate (to the less compiler, if no one else) that it IS a mixin, as opposed to a ruleset.

<a name="style_mixins"/>
### Style Mixins

A style mixin is a set of styles and nested rulesets that are intended to be mixed into another ruleset.

The name of a style mixin must **not** be that of a css property.

Commons examples include: `.button()` or `.list()`

```less
.button( @bgColor ) {
    background-color: @color;
    .border-radius( 5px );
    .box-shadow();
    font-size: 16px;
    font-weight: bold;
    margin: 10px;
    min-width: 100px;
    text-align: center;
}

.blue-button {
    .button( hsl( 240, 100%, 50% ) );
}

.big-red-button {
    .button( hsl( 0, 100%, 50% ) );

    font-size: 24px;
}
```

<a name="helpers"/>
### Helpers
A helper mixin contains ONLY a single css property, with intelligent default values, and/or cross-browser compatibility fallbacks.

The name of a helper mixin **must** be the same as the css property it replaces.

Common examples include: `.border-radius()` or `.transition()`

```less

.border-radius( @radius: 5px ) {
    -wekbit-border-radius: @radius;
       -moz-border-radius: @radius;
        -ms-border-radius: @radius;
         -o-border-radius: @radius;
            border-radius: @radius;
}
```

Helpers can also use pattern matching to switch between different implementations, and can also call other helpers and factories as needed.

```less
.border-radius( top-left, @radius: 5px ) {
    .background-clip();
    .browser-prefix( border-top-left-radius, @radius );
}
```

<a name="factories"/>
### Factories

A factory is a mixin that generates entire rulesets.

Factories are also often recursive.

Factories should be named using CamelCase.

```less
.MyFactory( @n ) when (@n > 0) {
    .my-foo-@{n} {
        background-position: 0 (@n * -22px);
    }

    .MyFactory( @n - 1 );
}

.MyFactory( 10 );
```

<a name="ordering"/>
## Ordering

All css properties should be ordered alphabetically. Helper mixins should be sorted along with the css properties

```less
.foo {
    background-color: hsl( 0, 100%, 50% );
    .border-radius( 5px );
    cursor: pointer
}
```

If possible, order style mixins alphabetically also. Exceptions apply.

```less
.foo {
    .clearfix(); // out of order due to conflicting css property "overflow" in .bar()
    .bar();
    .button();
```

Order selectors alphabetically within grouped selectors

```less
.bar,
.baz,
.bug,
.foo,
.hum {
    ...
}
```

Try to order rulesets in the same order as the corresponding html elements.

```html
<nav class="main-navigation">...</nav>
<nav class="secondary-navigation">...</nav>
<header class-"page-header">...</header>
<section class="content">...</section>
<footer class="page-footer">...</footer>
```

```less
.main-navigation { ... }
.secondary-navigation { ... }
.page-header { ... }
.content { ... }
.page-footer { ... }
```

If the order on the page is uncertain, order rulesets alphabetically by the selector (or first selector, in the case of grouped selectors).

```less
.primary-header { ... }
.quaternary-header { ... }
.secondary-header { ... }
.tertiary-header { ... }
```

<a name="nesting"/>
## Nesting

Avoid unnecessary nesting. Nesting increases the [specificity][specificity], which makes it harder to override later on.

Instead of traditional nesting, use concatenated selectors, using the parent selector ("&") to build up a longer (and to a human, more specific) class name, without actually increasing the CSS specificity.

```less
// No
.foo { // .foo (specificity: 0-1-0)
    .bar { // .foo .bar (specificity: 0-2-0)
        .baz {} // .foo .bar .baz (specificity: 0-3-0)
    }
}

// Yes
.foo { // .foo (specificity: 0-1-0)
    &-bar { // .foo-bar (specificity: 0-1-0)
        &-baz { // .foo-bar-baz (specificity: 0-1-0)

        }
    }
}
```

You can also use concatenation, in combination with [Modernizr][modernizr] to add feature detected overrides

```less
.foo {
    margin: 5px; // purely for visual appeal

    .touch & {
        margin: 25px; // make the touch target bigger, so users with big fingers can use it.
    }
}
```

<a name="comments"/>
## Comments

If modifying code that is described by an existing comment, update the comment such that it accurately reflects the new code. (Ideally, improve the code to obviate the need for the comment, and delete the comment entirely.)

The first word of the comment should be capitalized, unless the first word is an identifier that begins with a lower-case letter.

There should be one space of padding preceding the comment.

If a comment is short, the period at the end can be omitted.

```less
// Yes
//no.
```

As a general rule, use inline comments instead of block comments. Block comments are **included** in the compiled css (unless you minify also) whereas inline comments are stripped out. Use block comments only to document modules, and other large chunks of code.

<a name="block_comments"/>
### Block Comments

Block comments apply to the block of code that follows them.

Each line of a block comment should be indented at the same level of the code that it describes.

<a name="inline_comments"/>
### Inline Comments

Inline comments are placed on the line immediately above the statement that they are describing. If the inline comment is sufficiently short, it can be placed on the same line as the statement (separated by a single space from the end of the statement).

All inline comments should start with a "//" and a single space.

The use of inline comments should be limited, because their existence is typically a sign of a code smell.

Do not use inline comments when they state the obvious:

```less
    // No
    @width: 1004; // Set width to 1024
```

However, inline comments can be useful in certain scenarios:

```less
    # Yes
    @width: 1004; // Width of an iPad, minus room for a scrollbar
```

<a name="naming_conventions"/>
## Naming Conventions

Use `hyphen-case` (with all lowercase characters) in all selectors.

Use `camelCase` (with a leading lowercase character) to name all variables and parameters.

Use `CamelCase` (with a leading uppercase character) to name all factories. _(This style is also commonly referred to as `PascalCase`, `CamelCaps`, or `CapWords`, among [other alternatives][camel-case-variations].)_

Use `SNAKE_CASE` (with all uppercase characters) to name all constants.

Mixins and variables that are intended to be "private" should begin with a leading underscore

<a name="strings"/>
## Strings

Use double quoted (`""`) strings.

<a name="documentation"/>
## Documentation

Use [Knyle Style Sheets][kss] format

```less
// A button suitable for giving stars to someone.
//
// :hover             - Subtle hover highlight.
// .stars-given       - A highlight indicating you've already given a star.
// .stars-given:hover - Subtle hover highlight on top of stars-given styling.
// .disabled          - Dims the button to indicate it cannot be used.
//
// Styleguide 2.1.3.
.button.star {
    ...

    &.stars-given {
        ...

        &:hover {
            ...
        }
    }

    &.disabled {
        ...
    }
}

// Creates a linear gradient background, from top to bottom.
//
// @start - The color hex at the top.
// @end   - The color hex at the bottom.
//
// Compatible in IE6+, Firefox 2+, Safari 4+.
.gradient( @start, @end ) {
    ...
}
```

<a name="misc"/>
## Miscellaneous

Elements that occur exactly once inside a page should use IDs, otherwise, use classes. When in doubt, use a class name.

Avoid specifying units for zero values.

```less
margin: 0px; // No
margin: 0; // Yes
```

Strive to limit use of shorthand declarations to instances where you must explicitly set all the available values.

```less
background: hsl( 0, 100%, 50% ); // No
background-color: hsl( 0, 100%, 50% ); // Yes
```

Use [HSL][hsl] whenever possible instead of RGB, hex colors or named colors. HSL has the benefit of being modeled more closely on how humans perceive color. If you have no choice but to use hex, use lowercase hex.

```less
color: #f00; // No
color: red; // No
color: rgb( 255, 0, 0 ); // No
color: hsl( 0, 100%, 50% ); // Yes
```

Namespace your classes as much as possible.

```less
.item {} // No
.category-listing-item {} // Yes
```

Opening braces for a ruleset should be at the end of the last line of the selector. Closing braces for a ruleset should be in the same column as the first character of the selector.

```less
// No
.foo,
.bar
    {

    }

// Yes
.foo,
.bar {

}
```

Long, comma-separated property values - such as collections of gradients or shadows - can be arranged across multiple lines in an effort to improve readability and produce more useful diffs.

```less
background-image:
    linear-gradient( hsl( 120, 50%, 50% ), hsl( 180, 40%, 50% ) ),
    linear-gradient( hsl( 0, 100%, 100% ), hsl( 0, 0%, 0% ) );
```

[less]: http://lesscss.org/
[githubcss]: https://github.com/styleguide/css
[kss]: http://warpspire.com/kss/
[idiomatic]: https://github.com/necolas/idiomatic-css
[mixin]: http://lesscss.org/features/#mixins-feature
[sass]: http://sass-lang.com/guide
[specificity]: http://css-tricks.com/specifics-on-css-specificity/
[modernizr]: http://modernizr.com/
[camel-case-variations]: http://en.wikipedia.org/wiki/CamelCase#Variations_and_synonyms
[hsl]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#hsl%28%29
