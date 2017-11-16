# Developing a Plugin for a new Format

This guide provides an introduction on developing a plugin to add support for a new format to Nexus Repository Manager 3. It will give you a quick introduction into coding a format. It won't cover the nitty gritty of how to reverse engineer a format.

# What you'll build

You'll build a simple plugin with support for Proxy, Hosted and Group repositories. More information about the different repository types is [here](https://help.sonatype.com/display/NXRM3/Repository+Management).

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
5. Generate the new plugin setting the following parameters to match the new format. (See [Archetype GitHub Repository](https://github.com/sonatype-nexus-community/nexus-format-archetype) for more information).

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