More on Manifest Files
======================
There are a few key differences between Package Service
and Data Manager, those moving your manifest file-based
downloaders between the two tools might have noticed this.
We have tried to make the service as 3rd party friendly as
possible, which includes changes to how files are indexed
and downloaded. So it's important to know a few key
differences that you'll encounter and how to 'work around'
them.  

The most important one of them is the indexing of files,
Package Service is very particular about how it keeps track
of file data. This means that if you use the S3 Url to Package
Service file conversion method as outlined in the [sample code segments](example/README.md)
the package being queried will need to have the files present
for the S3 conversion to work. What does this mean? Well let's
explore a hypothetical:  

Let's say you're operating a similar flow to
[DCAN Labs](https://github.com/DCAN-Labs/nda-abcd-s3-downloader),
you want to download your package somewhere remote, but you also
want to take advantage of the extra metadata located within your
manifest file. You can download the manifest file directly from
Package Service without having to jump through the hoop of
downloading said manifest file through Download Manager, instead
you can download it by interfacing with Package Service directly.
However, if you follow the instructions perfectly you'll notice that Package
Service cannot convert any of the S3 Urls to Package Service file references.
This is because the files are not associated with your package, as when creating
your package you didn't include the associated files, which results in the Package
Service being unable to locate the appropriate package file associations with that
S3 Url. What you can do to alleviate this is that when you're creating your package
you include associated files, this will ensure the associations are present for
Package Service to be able to resolve the S3 Urls, but this will make the package
more 'cluttered', so you'll probably have to iterate on all files checking their names
to ensure that you're downloading and processing the correct file. This is a tedious change and
requires one to iterate over all of the package's files to snag the appropriate ids,
so we have implemented a way to narrow down your bulk file requests to just a specific
type of file, so as long as you know what type of file you're looking for it'll allow
you to shave of thousands of files with a simple specification.  

You can read more on the specific endpoints of Package Service [here](endpoints/README.md).