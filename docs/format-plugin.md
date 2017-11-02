# Developing a Plugin for a new Format

This guide provides an introduction on how to build a plugin to add support for a new format to Nexus Repository Manager 3. It is meant to give you a quick introduction into how format support is coded but won't cover the nitty gritty of how to reverse engineer a format or any format specific implementation details.

# What you'll build

You'll build a simple plugin akin to the Raw format with support for Hosted, Proxy and Group repositories. More information about the different repository types can be found at https://help.sonatype.com

# Useful resources

- General information / help: https://help.sonatype.com
- Formats that ship with Nexus: https://help.sonatype.com/display/NXRM3M/Supported+Formats
- Nexus exchange for third party formats: http://exchange.sonatype.com/list
