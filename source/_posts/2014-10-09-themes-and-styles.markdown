---
layout: post
title: "Themes and Styles"
date: 2014-10-09 18:40:26 +0800
comments: true
categories: Sencha Touch
---
Mobile web applications should either look and behave `exactly` the same as native apps, or they shouldn’t look like native at all

#### Sencha Touch Stylesheets
In Sencha Touch version 2.3, the framework ships with stylesheets that mimic native designs and experiences of all modern major devices 

When you create a production build of your app and you want to host it on the Web, it’s even possible to make use of the Sencha platform switcher. This switcher can make sure that the correct stylesheet will be shown for each platform. For example, the BB10 theme will be used when the platform is BlackBerry and show the Windows theme when the app is being viewed on Internet Explorer 10.

#### Using Sass

Sass stands for “syntactically awesome style sheets,” a powerful CSS extension. And yes, it is awesome! I describe it as CSS infused with a lot of coffee and Red Bull, because it overcomes many of the shortcomings of CSS.
Sass is a preprocessor, which means that Sass files should be compiled to (minified) CSS files. A CSS file is the stylesheet your browser uses to display Sencha Touch components. (And that’s also the file that goes into your production build or server.) Therefore, Sass needs a compiler. You can compile Sencha Sass themes with Sass and Compass installed on top of Ruby (see Appendix A), but if you are not really into design, you can also use just the `Sencha Cmd build` process. A Sencha app build can compile Sass files too, but it just takes a little bit longer.

Sass has two syntaxes. 

* The most commonly used syntax is known as `SCSS` (which stands for Sassy CSS), and is a superset of CSS3’s syntax. This means that every valid CSS3 stylesheet is valid SCSS as well. SCSS files use the extension `.scss`.

* The second, older syntax is known as the indented syntax (or just .sass). Instead of brackets and semicolons, it uses line indentation to specify blocks. Files in the indented syntax use the extension `.sass`.

The Sencha themes are `Sassy CSS files`. You can combine CSS3 syntax with the amazing features of Sass.

In this chapter, you’ll learn how to:

* Use platform-specific out-of-the-box themes 
* Create your own custom theme* Incorporate custom fonts* Incorporate custom icons* Optimize your stylesheet for best performance

Sencha Cmd 4 and higher have the command `sencha app watch`, You can compare this with the Compass command `compass watch`,. Sencha Cmd watches the app and every time you hit Save, Sencha Cmd builds your app and compiles your Sass stylesheets. When changes are detected, only the minimum amount of work necessary is performed to bring your app and its CSS up to date, saving you from rebuilding your Sass. It is so quick that when you navigate to your browser, the latest version is present
After writing your Sass theme, use the following command from the resources/sass folder to compile to app.css:```
sencha ant sass```
`TIP`
There are some great resources on Sass and CSS at [the Sass website](http://sass-lang.com/). To learn more about Sass, see [The Sass Way. Learn Sass at Code School](http://vimeo.com/75258560), and check out “Unleash Your Inner Picasso: Advanced Theming by Platform,” an online video tutorial by Robert Dogan
#### Incorporating Custom Fonts
When you want to incorporate fonts in your theme, you can use `@font-face`. @font- face is a CSS technique that is often used to incorporate custom web fonts. Where system fonts will be visible only if they’re available on your OS, @font-face directly `downloads` the font from the Internet to display it

`TIP`

Read more about @font-face on [Paul Irish’s blog](http://www.paulirish.com/2010/font-face-gotchas/) and download HTML5 @font-face kits from [FontSquirrel](http://www.fontsquirrel.com/) or at font ex. You can also use a font service like Adobe [Typekit](https://typekit.com/) or [Google Fonts](http://www.google.com/fonts).Check out the [Pictos icon font](http://pictos.cc/font/), the [Ico Moon icon font](https://icomoon.io/), and the Fontello icon font. There’s more information about icon fonts at [CSS-Tricks](http://css-tricks.com/html-for-icon-font-usage/), and you can use [Branah’s tool](https://www.branah.com/unicode-converter) to convert text to unicodes and back.
#### Optimizing Your Stylesheet for Best Performance
* Minify your stylesheet* Import only the required mixins in your stylesheet * Exclude experimental support for uncommon browsers * Exclude unusable default fonts and icons
