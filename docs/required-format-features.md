# NXRM required format features

Before Sonatype can officially support a contributed format there are a set of features and tasks that must be completed. 
The intent of this guide is to document those features and make it clear what you need to do so that your contributions 
can become a core part of Nexus Repository Manager 3.

## Checklists

### Principles
The following are a set of principles to keep in mind when developing a format. They are aspirational and not all
formats will fit nicely in to these boxes but we still try our best.

 - Usage should be transparent to developers
 - Require the minimum amount of developer setup
 - Design should ensure that NXRM is not circumvented, for example via metadata calls to outside sources directly
 - Cached packages should always be available to clients, even when removed from the remote
 - Builds that work online should be reproducible when the remote is offline
 - NXRM should only download from endpoints that are explicitly trusted by the end user
 - If your implementation does not support all client versions, make it clear which ones work

### Features
These are the minimum set of features required for a new format. It may not be possible to implement all of these 
features for some formats, those cases should be documented explicitly.

 - Search
 - Tree browse
 - Cleanup policies
 - The format should be disabled in HA-C mode
 - Routing rule support
 - API for provisioning repositories via Scripts
 - UI Upload (Hosted repositories only)
 - Database restore from blobs (Task: 'Repair - Reconcile component database from blob store')
 - Components exist for artifacts
 
### Deliverables
 - Help documentation
 - Manual test documentation
 - A KAR build published to Maven Central
 - Integration tests
 - The plugin should depend on the latest available SNAPSHOT of NXRM
