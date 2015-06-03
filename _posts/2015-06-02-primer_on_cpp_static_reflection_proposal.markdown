---
layout: post
title:  "A Primer on the Static Reflection Proposal"
date:   2015-06-02
categories: c++ standardization
---

N4451, the third revision since the original proposal by Matus Chochlik to
bring static reflection to C++, presents a very powerful and uniform set of
additions to enable reflection in C++.

The proposed additions are as follows:

1. Add a set of **metaobject concepts** in the form of anonymous types.
2. Represent the properties of each construct in the language using a
   subset of the set of metaobject concepts.
3. Add a new reflection keyword: `mirrored`.

For example, given the following snippet, we can find out the namespace
of which `Cat` belongs to, whether or not that namespace is in the global
namespace or not, whether or not `Cat` is a class, and if so, the members
it contains and their respective names.

{% highlight cpp %}
namespace foo {
    class Cat : public Feline, public IsDomestic {
    public:
        Cat(String name);
    private:
        int _age;
        string _name;
    }
}
{% endhighlight %}

During compile-time, the compiler will generate a set of anonymous template
instantiations to represent the properties of each of the constructs in the
above snippets. For example, `Cat` will have an instantiation of
`MetaClass` generated and each of its member will have an instantiation
of `MetaClassMember` generated. Namespace `foo` will have an instantiation
of `MetaNamespace` generated.

To find out the name of each of the members of `Cat`, we could potentially
write:

{% highlight cpp %}
for_each<members<mirrored(Cat)>>(
    [] (auto meta_cls_mem) {
        cout << "Member: " << base_name<decltype(meta_cls_mem)> << endl;
    }
);
{% endhighlight %}

To find out the name of the namespace that contains `Cat`, we could write:

{% highlight cpp %}
auto catScope = scope<mirrored(Cat)>;
cout << base_name<decltype(catScope)::namespace> << endl;
{% endhighlight %}

If adopted, this proposal should make the many tasks currently requiring
heavy use of macros and boilerplace code to replicate type information,
such as serialization and logging, much easier.

To learn more about the Static Reflection proposal, refer to:

* [N4451: Static reflection (rev. 3)][N4451]
* [N4452: A case for strong static reflection][N4452]

[N4451]: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4451.pdf
[N4452]: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4452.pdf
