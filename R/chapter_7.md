Tripping on Object Orientation
================

## S3 methods

S3 methods make the `class` attribute the driver of a OO sytsem and its
optional that is, S3 method is only effective for an object if that
object has class defined.

There are some function that are generic (e.g. print, plot, summary, lm
etc.). Generic functions look for whether its first argument has a class
argument and then look for a method of the generic function that matches
up the name of the class of the first argument. If such a method exits,
then that method function is used. But if no method is found or the
argument does not have a class, then the default method of generic
function is used.
