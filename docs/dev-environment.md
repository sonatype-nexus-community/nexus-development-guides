# Development Environment Setup

Clone the [nexus-public](https://github.com/sonatype/nexus-public) repository using [git](https://git-scm.com/).

## Eclipse

Start with one of the distributions for Java Developers. To add a plugin under the *Help* menu choose *Install New Software*.

### Install m2e 

The m2e package should be available in the releases repository for your version of Eclipse which is available by default (e.g. for Oxygen - http://download.eclipse.org/releases/oxygen).

After the installation completes it is recommend to enable the *Hide folders of physically nested modules* preference which is available under *Windows* > *Preferences* on the *Maven* preference page.

### Install Eclipse Groovy Plugin

The [Groovy Eclipse](https://github.com/groovy/groovy-eclipse) plugin is a third party plugin part of the Apache Groovy project. Add the repository http://dist.springsource.org/release/GRECLIPSE/2.9.2/e4.7 and install *Groovy-Eclipse M2E integration* (it will transitively add the required Groovy components).

### Add projects to the workspace

From the *File* menu choose *Import*. Select *Existing Maven Projects* and browse to the directory [nexus-public](https://github.com/sonatype/nexus-public) was cloned to earlier. The initial import may take some time and Eclipse should prompt to install additional connectors for m2e.
