# Developing a Plugin for a new Format

This guide provides an introduction on developing a plugin to add support for a new format to Nexus Repository Manager 3. 
It will give you a quick introduction into coding a format. It won't cover the nitty gritty of how to reverse engineer a format.

# What you'll build

You'll build a simple plugin with support for Proxy, Hosted and Group repositories. More information about the different repository types is 
[here](https://help.sonatype.com/display/NXRM3/Repository+Management).

# Useful resources

- [Nexus help](https://help.sonatype.com)
- [Supported formats](https://help.sonatype.com/display/NXRM3M/Supported+Formats)
- [Nexus exchange](http://exchange.sonatype.com/list)

# Creating a project skeleton

First create a new plugin project. The simplest way is to use the format-plugin archetype.

## Clone and build the archetype

1. Clone nexus-format-archetype ```git clone https://github.com/sonatype-nexus-community/nexus-format-archetype```.
2. Change directory to nexus-format-archetype ```cd nexus-format-archetype```.
3. Build the archetype ``` mvn clean install ```.
4. Change directory to a folder where you want the new plugin to live ```cd ../nexus-repository-foo```.
5. Generate the new plugin setting the following parameters to match the new format. 
(See [Archetype GitHub Repository](https://github.com/sonatype-nexus-community/nexus-format-archetype) for more information).

  ``` 
    mvn archetype:generate                              
    -DarchetypeArtifactId=format-plugin               
    -DarchetypeGroupId=org.sonatype.nexus.archetype   
    -DarchetypeVersion=1.0-SNAPSHOT                   
    -DgroupId=com.sonatype.repository                 
    -DartifactId=nexus-repository-foo                 
    -DpluginFormat=foo                                
    -DpluginClass=Foo                                 
    -Dversion=1.0-SNAPSHOT 
  ```   
  
This will create a project with a skeleton plugin infrastructure. The above parameters will generate the plugin in nexus-repository-foo for the "Foo" format.

Open this project in the IDE / editor of your choice and you should have a project that looks like

![picture alt](initial-plugin-project-structure.png)

## Building and using the new plugin

Build the new plugin ```mvn clean install```.

While developing a plugin it is recommended to use a temporary install. See [Installing a custom Nexus 3 plugin](./plugin-install.html).

# Implementing a Proxy Repository

Each format has one more remote repositories that are used for fetching packages and metadata (e.g. Maven has [Maven Central](https://search.maven.org) , 
npm has [npmjs.org](https://www.npmjs.com)). A Proxy repository allows you to create a cache of a remote repository. They increase the speed of your builds 
and give you access to packages when the remote is unavailable.

## Proxy Recipe

Each repository type for each format has its own "Recipe". A Recipe class defines the endpoints that are available for the format, the routes each 
endpoint can take and what functionality is available to the repository. 

The archetype you used to initialise your new plugin has a RecipeSupport class that comes with some pre-configured functionality.

Create a recipe for a Proxy repository for the Foo format.

```
com.sonatype.repository.foo.internal.proxy.FooProxyRecipe.groovy
```

```groovy
package com.sonatype.repository.foo.internal.proxy

import javax.annotation.Nonnull
import javax.inject.Inject
import javax.inject.Named
import javax.inject.Singleton

import com.sonatype.repository.foo.internal.FooFormat
import com.sonatype.repository.foo.internal.FooRecipeSupport

import org.sonatype.nexus.repository.Format
import org.sonatype.nexus.repository.Repository
import org.sonatype.nexus.repository.Type
import org.sonatype.nexus.repository.types.ProxyType

@Named(FooProxyRecipe.NAME)
@Singleton
class FooProxyRecipe
   extends FooRecipeSupport
{
  public static final String NAME = 'foo-proxy'

  @Inject
  ProxyHandler proxyHandler

  @Inject
  public FooProxyRecipe(final @Named(ProxyType.NAME) Type type,
                        final @Named(FooFormat.NAME) Format format)
  {
    super(type, format)
  }

  @Override
  void apply(final @Nonnull Repository repository) throws Exception {
    repository.attach(securityFacet.get())
    def viewFacet = configure(viewFacet.get())
    repository.attach(viewFacet)
    repository.attach(httpClientFacet.get())
    repository.attach(negativeCacheFacet.get())
    repository.attach(storageFacet.get())
    repository.attach(attributesFacet.get())
    repository.attach(searchFacet.get())
    repository.attach(purgeUnusedFacet.get())
  }

  private ViewFacet configure(final ConfigurableViewFacet facet) {
    Router.Builder builder = new Router.Builder()

    builder.route(new Route.Builder()
        .matcher(new TokenMatcher('/{name:.+}'))
        .handler(timingHandler)
        .handler(securityHandler)
        .handler(exceptionHandler)
        .handler(handlerContributor)
        .handler(negativeCacheHandler)
        .handler(conditionalRequestHandler)
        .handler(partialFetchHandler)
        .handler(contentHeadersHandler)
        .handler(unitOfWorkHandler)
        .handler(proxyHandler)
        .create())

    builder.defaultHandlers(notFound())

    facet.configure(builder.create())

    return facet
  }
}
```

The class is flagged as ```@Named``` and implements ```Recipe``` via the support class and therefore will be detected by Nexus. 
When a proxy repository of 
format Foo is created this recipe will be used and the ```apply()``` method will be called.

The ```apply()``` method adds functionality to the repository. The ```configure()``` method defines the endpoints and what route 
a request will take through Nexus.

## The facets

Each of the "Facets" that get attached to the repository provide functionality.

| Facet                 | Definition                                                   |
|-----------------------|--------------------------------------------------------------|
| SecurityFacet         | Adds authorization to the repository. (Defined by the format)|
| ViewFacet             | Provides the http endpoint.                                  |
| HttpClientFacet       | Allows the repository to make http requests.                 |
| NegativeCacheFacet    | Caches 404 responses for performance.                        |
| StorageFacet          | Gives access to the blob store and databases.                |
| AttributesFacet       | Handles attributes of the repository itself.                 |
| SearchFacet           | Adds search functionality.                                   |               
| PurgeUnusedFacet      | Allows the repository to be used with the Purge unused task. |

The security facet must be defined per-format.

Create a class for the ```SecurityFacet```.

```
com.sonatype.repository.foo.internal.FooSecurityFacet.java
```

```java
package com.sonatype.repository.foo.internal;

import javax.inject.Inject;
import javax.inject.Named;

import org.sonatype.nexus.repository.security.ContentPermissionChecker;
import org.sonatype.nexus.repository.security.SecurityFacetSupport;
import org.sonatype.nexus.repository.security.VariableResolverAdapter;

@Named
public class FooSecurityFacet
    extends SecurityFacetSupport
{
  @Inject
  public FooSecurityFacet(final FooFormatSecurityContributor securityContributor,
                          @Named("simple") final VariableResolverAdapter variableResolverAdapter,
                          final ContentPermissionChecker contentPermissionChecker)
  {
    super(securityContributor, variableResolverAdapter, contentPermissionChecker);
  }
}
```

You will also need to create a ```SecurityContributor```.

```java
package com.sonatype.repository.foo.internal;

import javax.inject.Inject;
import javax.inject.Named;
import javax.inject.Singleton;

import org.sonatype.nexus.repository.Format;
import org.sonatype.nexus.repository.security.RepositoryFormatSecurityContributor;

@Named
@Singleton
public class FooFormatSecurityContributor
    extends RepositoryFormatSecurityContributor
{
  @Inject
  public FooFormatSecurityContributor(@Named(FooFormat.NAME) final Format format) {
    super(format);
  }
}

```