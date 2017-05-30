<!-- markdown version of sidemenu --> 
<!-- How to generate md menu from html side menu -->
<!-- 1. Convert html to md -- https://domchristie.github.io/to-markdown/ --> 
<!-- 2. Remove html entities form md -- http://www.unit-conversion.info/texttools/strip-tags/ -->
<!-- 3. Remove "\r" and duplicate "\n" --> 
<!-- 4. Make sure H3-s are also enumerated eg.: from "### Before starting" to "* ### Before starting" -->

* ### Before starting
* [Overview]({{ site.url }}/Overview/)
  * [Developing for DotKernel 3]({{ site.url }}/Overview/Developing-for-DotKernel-3.html)
  * [DotKernel 3 Overview]({{ site.url }}/Overview/DotKernel-3-Overview.html)
* [Prerequisites]({{ site.url }}/Prerequisites/)
  * [Closures and Callables]({{ site.url }}/Prerequisites/Closures-and-Callables.html)
  * PSR-4: Autoloading
  * [Composer Quickstart]({{ site.url }}/Prerequisites/Composer-Quickstart.html)
  * [Composer Package Versions]({{ site.url }}/Prerequisites/Composer-Package-Versions.html)
  * [PSR-7 Interfaces & Zend Diactoros]({{ site.url }}/Prerequisites/PSR-7.html)
  * PSR-11
  * [Understanding Middleware]({{ site.url }}/Prerequisites/Understanding-Middleware.html)
  * [Zend Stratigility]({{ site.url }}/Prerequisites/Zend-Stratigility.html)
  * Zend Expressive

* ### Introduction

* [Getting Started]({{ site.url }}/Getting-Started/)
  * [Server Requirements]({{ site.url }}/Getting-Started/Server-Requirements.html)
  * [Installing DotKernel 3 `frontend`]({{ site.url }}/Getting-Started/Installing-DotKernel-3-Frontend.html)
  * DotKernel 3 file structure
  * DotKernel 3 packages
  * DotKernel 3 features
* [Tutorials]({{ site.url }}/Tutorials/)
  * [Creating a Contact US Page]({{ site.url }}/Tutorials/Creating-a-Contact-Us-Page/)
  * More tutorials soon

* ### Working with DotKernel 3

* [Guidelines]({{ site.url }}/Guidelines/)
  * [PSR Guidelines]({{ site.url }}/Guidelines/PSR/)
  * [Coding Guidelines]({{ site.url }}/Guidelines/Coding-Guidelines/)
  * [Documentation Guidelines]({{ site.url }}/Guidelines/Documentation-Guidelines/)
  * Creating an open source DotKernel 3 package (soon)
  * Creating a private DotKernel 3 package (soon)
* [dot-* packages]({{ site.url }}/Packages/)
  * [View dot-* package list]({{ site.url }}/Packages/README.html)