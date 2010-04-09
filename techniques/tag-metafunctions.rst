
Tag Metafunctions
`````````````````

Sometimes you want to vary a function or a type's behavior based on a statically
defined input. In cpp-netlib there are a number of things you might want to
change based on some statically defined inputs -- like what the string type to
be used should be, how large a buffer should be, among other things. The primary
way to define this in a header-only manner is to use tag-based metafunctions.

The skeleton of the approach hinges on a similar technique for defining type
traits [#]_. In cpp-netlib however the type traits are defined on opaque tag
types which serve to associate results to a family of metafunctions.

.. [#] Boost.Type_traits is one example of a library that uses metafunctions to
   define type variations at compile-time.

To illustrate this point, let's define a tag ``default_`` which we use to denote
the default implementation of a certain type ``foo``. For instance we decide
that the default string type we will use for ``default_`` tagged ``foo``
specializations will be an ``std::string``.

In cpp-netlib this is done by defining a ``string`` metafunction type that is
specialized on the tag ``default_`` whose nested ``type`` result is the type
``std::string``. In code this would translate to:

::

    template <class Tag>
    struct string {
        typedef void type;
    };

    struct default_;

    template <>
    struct string_<default_> {
        typedef std::string type;
    };

Then in the definition of the type ``foo`` we use this type function ``string``
and pass the tag type parameter to determine what to use as the string type in
the context of the type ``foo``. In code this would translate into:

::

    template <class Tag>
    struct foo {
        typedef typename string<Tag>::type  string_type;

        // .. use string_type where you need a string.
    };

Using this approach we can support different types of strings for different tags
on the type ``foo``. In case we want to use a different type of string for the
tag ``default_`` we only change the specialization of ``string<default_>``. In
the other case that we want to use a different tag to define the functionality
of ``foo``, all we have to do is implement specializations of the type functions
like ``string`` for that different tag.

The approach also allows for the control of the structure and features of types
like ``foo`` based on the specialization of the tag. Whole type function
families can be defined on tags where they are supported and ignored in cases
where they are not.

To illustrate let's define a new tag ``swappable`` and the new type ``bar``.
Given the above definition of ``foo``, we'd want to make the
``swappable``-tagged ``foo`` define a ``swap`` function that extends the original 
``default_``-tagged ``foo``. In code this would look like:

::

    struct swappable;

    template <>
    struct foo<swappable> : foo<default_> {
        void swap(foo<swappable> & other) {
            // ...
        }
    };

We also for example want to enable an ADL-reachable ``swap`` function:

::

    struct swappable;

    inline void swap(foo<swappable> & left, foo<swappable> & right) {
        left.swap(right);
    }

Overall what the tag-based definition approach allows is for static definition
of extension points that ensures type-safety and invariants. This keeps the
whole extension mechanism static and yet flexible.

The only drawback with this approach is the verbosity at which extensions and
specializations are to be done which introduces additional pressure on the
compiler at compile-time computations. Becase this technique relies heavily on
the C++ template mechanism, compile times may be greatly affected.

The potential for tag and implementation explosion is also high. If the
implementor is not careful in controlling the number of tags and type functions
that take the tag as an input, it can get out of hand quickly. The suggestion is
to define acceptable defaults and leverage re-use of existing tags as much as
possible. Using Boost.MPL data structures like map's and/or control structures
like ``if_`` and ``switch_`` can also help with heavily-used type functions.

