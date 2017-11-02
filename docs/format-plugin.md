# Developing a Plugin for a new Format

This guide provides an introduction on how to build a plugin to add support for a new format to Nexus Repository Manager 3. It is meant to give you a quick introduction into how format support is coded but won't cover the nitty gritty of how to reverse engineer a format or any format specific implementation details.

# What you'll build

You'll build a simple plugin akin to the Raw format with support for Hosted, Proxy and Group repositories. More information about the different repository types can be found at [Repository Management](https://help.sonatype.com/display/NXRM3/Repository+Management)

# Useful resources

- General information / help: https://help.sonatype.com
- Formats that ship with Nexus: https://help.sonatype.com/display/NXRM3M/Supported+Formats
- Nexus exchange for third party formats: http://exchange.sonatype.com/list

# Creating a project skeleton

First you create a new plugin project and the easiest way to do this is using the format-plugin archetype.

## Clone and build the archetype

1. git clone https://github.com/doddi/format-plugin.git
2. Change directory to format-plugin.
3. Build the archetype: ``` mvn clean install ```
4. Change directory to a new folder where you want the new plugin to live.
5. Generate the new plugin setting the following parameters to match the new format. (For more information on the parameters      refer to the archetype GitHub repository from step 1)

  ``` 
    mvn archetype:generate                              
    -DarchetypeArtifactId=format-plugin               
    -DarchetypeGroupId=org.sonatype.nexus.archetype   
    -DarchetypeVersion=1.0-SNAPSHOT                   
    -DgroupId=com.sonatype.repository                 
    -DartifactId=nexus-repository-foo                 
    -DpluginFormat=foo                                
    -DpluginClass=Food                                 
    -Dversion=1.0-SNAPSHOT 
  ```   
  
This will create a project with a skeleton format plugin infrastructure in the directory of your choosing. The above parameters would generate the plugin in nexus-repository-foo for the "Foo" format.

Open this project in the IDE / editor of your choice and you should have a project that looks like

![picture alt](initial-plugin-project-structure.png)

## Building and using the new plugin

Build the new plugin using: ``` mvn clean install ```

As you will be developing the plugin I suggest doing a temporary install as documented in [Intalling a custom Nexus 3 plugin](./plugin-install.html).