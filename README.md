Demonstrate dependency resolution with scopes.

* `mockito-core` is a dependency in scope `compile`
* `mockito-core` has a dependency on an old `objenesis` version
* `easymock` is a dependency in scope `test`
* `easymock` has a dependency on the newest `objenesis` version

If you do a `dependency:tree` you will see the following:

```log
[INFO] pro.tremblay:maven-scope:jar:1.0-SNAPSHOT
[INFO] +- org.easymock:easymock:jar:4.3:test
[INFO] |  \- org.objenesis:objenesis:jar:3.2:compile
[INFO] \- org.mockito:mockito-core:jar:3.1.0:compile
[INFO]    +- net.bytebuddy:byte-buddy:jar:1.9.10:compile
[INFO]    \- net.bytebuddy:byte-buddy-agent:jar:1.9.10:compile
```

The `objenesis` dependency commes from `easymock` because it has the highest version.
However, the `objenesis` scope is now `compile` because `mockito-core` would put it in that scope.

Bottom line, in the tree, you see the transitive dependency on the dependency giving the best version of it but the scope
is whatever is the less restrictive of all the dependencies.

# Additional quicks

If you modify the `pom.xml` to put `mockito-core` first in the list of dependencies, the tree is now:

```log
[INFO] pro.tremblay:maven-scope:jar:1.0-SNAPSHOT
[INFO] +- org.mockito:mockito-core:jar:3.1.0:compile
[INFO] |  +- net.bytebuddy:byte-buddy:jar:1.9.10:compile
[INFO] |  +- net.bytebuddy:byte-buddy-agent:jar:1.9.10:compile
[INFO] |  \- org.objenesis:objenesis:jar:2.6:compile
[INFO] \- org.easymock:easymock:jar:4.3:test
```

I am really surprised that it takes the low version in `compile` scope... and that the order matter.
For me this is a bug in Maven.

I use Maven 3.8.4 and maven-dependency-plugin 3.2.0.

