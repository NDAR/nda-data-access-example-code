Package Service Documentation
=============================
- [Code Examples](example/README.md)
- [Endpoints](endpoints/README.md)
- [A note on using manifest files](MANIFEST_FILE_NOTE.md)

FAQ
---
### I keep getting an 'access denied' when querying a package I created today
This is probably caused by the database not being fully caught up, while
your package is technically ready to download, permissions updates haven't
updated fully, permissions are updated every night.

### What is a manifest file?
A manifest file is a file that is contained within a package that has the
express intent of serving as an extra layer of metadata for that specific
package. Manifest files tend to contain various forms of data and then a
S3 link to the file associated with that data, this effectively allows
advanced users to perform specific operations on specific files within a
package without having to operate on the entire set of data.