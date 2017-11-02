## Installing a custom Nexus 3 plugin

There are a range of options for installing the a plugin. You'll need to build it first, and
then install the plugin with the options shown below:

### Temporary Install

Installations done via the Karaf console will be wiped out with every restart of Nexus Repository. This is a
good installation path if you are just testing or doing development on the plugin.

* Enable Nexus console: edit `<nexus_dir>/bin/nexus.vmoptions` and change `karaf.startLocalConsole`  to `true`.

  More details here: https://help.sonatype.com/display/NXRM3/Bundle+Development#BundleDevelopment-InstallingBundles

* Run Nexus' console:
  ```
  # sudo su - nexus
  $ cd <nexus_dir>/bin
  $ ./nexus run
  > bundle:install file:///tmp/nexus-repository-foo-1.0.0.jar
  > bundle:list
  ```
  (look for org.sonatype.nexus.plugins:nexus-repository-foo ID, should be the last one)
  ```
  > bundle:start <org.sonatype.nexus.plugins:nexus-repository-foo ID>
  ```

### (more) Permanent Install

For more permanent installs of the custom plugin, follow these instructions:

* Copy the bundle (nexus-repository-foo-1.0.0.jar) into <nexus_dir>/deploy

This will cause the plugin to be loaded with each restart of Nexus Repository. As well, this folder is monitored
by Nexus Repository and the plugin should load within 60 seconds of being copied there if Nexus Repository
is running. You will still need to start the bundle using the karaf commands mentioned in the temporary install.

### (most) Permanent Install

If you are trying to use your new custom plugin permanently, it likely makes more sense to do the following:

* Copy the bundle into `<nexus_dir>/system/org/sonatype/nexus/plugins/nexus-repository-foo/1.0.0/nexus-repository-foo-1.0.0.jar`
* If you are using OSS edition, make these mods in: `<nexus_dir>/system/com/sonatype/nexus/assemblies/nexus-oss-feature/3.x.y/nexus-oss-feature-3.x.y-features.xml`
* If you are using PRO edition, make these mods in: `<nexus_dir>/system/com/sonatype/nexus/assemblies/nexus-pro-feature/3.x.y/nexus-pro-feature-3.x.y-features.xml`
   ```
         <feature version="3.x.y.xy" prerequisite="false" dependency="false">nexus-repository-rubygems</feature>
   +     <feature version="1.0.0" prerequisite="false" dependency="false">nexus-repository-foo</feature>
         <feature version="3.x.y.xy" prerequisite="false" dependency="false">nexus-repository-yum</feature>
     </feature>
   ```
   And
   ```
   + <feature name="nexus-repository-foo" description="org.sonatype.nexus.plugins:nexus-repository-foo" version="1.0.0">
   +     <details>org.sonatype.nexus.plugins:nexus-repository-foo</details>
   +     <bundle>mvn:org.sonatype.nexus.plugins/nexus-repository-foo/1.0.0</bundle>
   + </feature>
    </features>
   ```
This will cause the plugin to be loaded and started with each startup of Nexus Repository.