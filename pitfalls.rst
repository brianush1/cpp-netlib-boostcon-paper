There are a number of common pitfalls one gets to when implementing a
header-only network library. Some of these are briefly discussed in this
section.

Linear Explosion
````````````````

The linear explosion comes up when you start supporting a lot of different tags
through the tag dispatch mechanism. There is a temptation to implement support 
a whole slew of tags and the associated variations brought about by these tags.
Because of this as soon as you start mixing and matching tags you get to a point
where where there can be a lot of tags and a lot of specializations.

To manage linear explosion, using a moderately robust template metaprogramming
library like Boost.MPL should be used to remove much of the repetition and
manage the growth of the variations/combinations.

Compile Time Toll
`````````````````

Due to the nature of template metaprogramming and the current state of compiler
technologies, the cost of going header-only can be considerable during
compilation. It is no secret that template metaprogramming takes a lot out of
the compiler because the template implementations of most of the popular
compilers were not built to handle template metaprogramming. The state of the
art when it comes to compiler technology is changing in C++ with the promise of
faster, more efficient, and more robust template metaprogramming support.

Aside from doing as much as you can to optimize the compilation times of
template-based programs, the only solution really is to wait for (or to actually
help) compilers to get faster and better when compiling template-heavy code.

Contribution Barrier
````````````````````

Currently there are not a lot of C++ programmers who understand how to do
template metaprogramming as well as implement efficient network utilities. There
are a lot of C++ programmers who are looking for an using libraries to make
network programming easier at a higher level. The disparity between the users
and the developers can be considerable, and using a relatively niche technique
like template metaprogramming in the implementation of the library proves to be
a significant contribution barrier.

Because cpp-netlib is a project that has in its stated goals the implementation
and the distribution of a header-only collection of network-based libraries, one
solution to the issue is in the education and dissemination (and documentation)
of the techniques used and the rationale for why these techniques are used in
cpp-netlib. This is the goal of this paper and in the future editions of this
paper.


