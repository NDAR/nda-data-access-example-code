Java Code Example
=================
Some sample java code for retrieving files
from Package Service. The HTTP Client library
used in this example is the Apache HTTP Client,
and the JSON Library used in this example is Gson.

Authentication
--------------

You can verify if your authentication credentials work
by using the `/auth` endpoint:
```java
// Create the HttpClient with a customized User-Agent.
// Please be sure that you communicate to the Package Service
// using a custom User-Agent that helps us identify you.
// This will help protect against your address getting blocked
// in the case of an incident occurring.
HttpClient client = HttpClients.custom()
                        .setUserAgent("Example Client")
                        .build();

// Your credentials string should be Base64 encoded.
String credentials = Base64.getEncoder().encodeToString((username + ":" + password).getBytes());

// Create a basic get request and set the authorization
// and the accepted content type.
HttpGet authRequest = new HttpGet("https://nda.nih.gov/api/package/auth");
authRequest.setHeader("Accept", "application/json");
authRequest.setHeader("Authorization", "Basic " + credentials);

// Execute the request and store the response
HttpResponse response = client.execute(authRequest);

// Store the HTTP Response code
int status = response.getStatusLine().getStatusCode();

// Business logic
if (status == 200) {
    System.out.println("Successfully authenticated!");
} else {
    System.out.println("Authentication failed...");
    return;
}
```

The auth endpoint is not required in your usage though,
it is only helpful for validating your credentials.  

Retrieving Files
----------------
#### From Nothing:
If you want to get and download all files related to a
single package:
```java
// Assume code in authentication section is present.

// Make a Gson object for decoding the JSON responses.
final Gson GSON = new GsonBuilder()
                    .setPrettyPrinting()
                    .create();

final long packageId = 1234;

// Construct the request to get the files of package 1234
// URL structure is: https://nda.nih.gov/api/package/{packageId}/files
HttpGet filesRequest = new HttpGet("https://nda.nih.gov/api/package/" + packageId + "/files");
filesRequest.setHeader("Accept", "application/json");
filesRequest.setHeader("Authorization", "Basic " + credentials);

response = client.execute(filesRequest);

// Get the String response body.
String responseBody = EntityUtils.toString(response.getEntity(), StandardCharsets.UTF_8);

// Convert the response body String to a JsonObject.
// This endpoint will only return a JsonObject.
JsonObject object = GSON.fromJson(responseBody, JsonObject.class);

// The results property will always be a JsonArray.
JsonArray results = object.get("results").getAsJsonArray();

// Business Logic.

// In our example we want to parse all files in
// a package and store their IDs
Map<Long, String> files = new HashMap<>();

results.forEach(e -> {
    // The array will always contain JsonObjects.
    JsonObject arrayObject = e.getAsJsonObject();
    
    // Add the file's ID to the list of file IDs.
    files.put(arrayObject.get("package_file_id").getAsLong(), arrayObject.get("download_alias").getAsString());
});
```

#### From S3 URL
This is a use-case that was inspired by
the [DCAN Labs NDA ABCD Downloader](https://github.com/DCAN-Labs/nda-abcd-s3-downloader).
([More on this workflow](../MANIFEST_FILE_NOTE.md))
This method allows you to convert pre-existing s3
file references to Package Service files:
```java
// Assume code in authentication section is present.

// Handle reading in the file and adding the urls to a list.
ArrayList<String> s3Urls = new ArrayList<>();

// Not all manifest files are the same, all that really matters
// for this approach is loading the S3 paths, and having a package
// that has all of the S3 files associated with it.
String rows;
// Open reader and read in data.
BufferedReader reader = new BufferedReader(new FileReader("datastructure_manifest.txt"));
while ((rows = reader.readLine()) != null) {
    String[] data = rows.split("\t");

    for (String row : data) {
        // Sanatize
        String s3 = row.replace("\"", "");

        if (!s3.startsWith("s3://")) {
            continue;
        }

        s3Urls.add(s3);
    }
}

// Make a Gson object for decoding the JSON responses.
final Gson GSON = new GsonBuilder()
                    .setPrettyPrinting()
                    .create();

final long packageId = 1234;

// Fetch files from package 1234
HttpPost urlRequest = new HttpPost("https://nda.nih.gov/api/package/" + packageId + "/files");
urlRequest.setHeader("Accept", "application/json");
urlRequest.setHeader("Authorization", "Basic " + credentials);

// Create our post data.
JsonArray filesArray = new JsonArray();

// Populate post data.
s3Urls.forEach(filesArray::add);

// Set post data.
urlRequest.setEntity(new ByteArrayEntity(GSON.toJson(filesArray).getBytes(StandardCharsets.UTF_8)));

response = client.execute(urlRequest);

// Business Logic.

Map<Long, String> files = new HashMap<>();

results.forEach(e -> {
    // The array will always contain JsonObjects.
    JsonObject arrayObject = e.getAsJsonObject();

    // Add the file's ID to the list of file IDs.
    files.put(arrayObject.get("package_file_id").getAsLong(), arrayObject.get("download_alias").getAsString());
});
```

Downloading Files
-----------------
After receiving files using one of the above methods you will have a map of
all file ids in a package and their names. If you were to want to then move
to downloading said files, you'd do something along the lines of:
```java
// Assume code in authentication section is present.
// Assume that one of the retrieving files implementations is present too

// Create a post request to the batch generate presigned urls endpoint.
HttpPost urlRequest = new HttpPost("https://nda.nih.gov/api/package/" + packageId + "/files/batchGeneratePresignedUrls");
urlRequest.setHeader("Accept", "application/json");
urlRequest.setHeader("Authorization", "Basic " + credentials);

// Create our post data.
JsonArray filesArray = new JsonArray();

// Populate post data.
files.keySet().forEach(filesArray::add);

// Set post data.
urlRequest.setEntity(new ByteArrayEntity(GSON.toJson(filesArray).getBytes(StandardCharsets.UTF_8)));

response = client.execute(urlRequest);

responseBody = EntityUtils.toString(response.getEntity(), StandardCharsets.UTF_8);
object = GSON.fromJson(responseBody, JsonObject.class);

// Business Logic.

// Get the presignedUrls array from response.
JsonArray urlArray = object.get("presignedUrls").getAsJsonArray();

// Iterate on urls.
urlArray.forEach(e -> {
    // Always will be an array of JsonObjects.
    JsonObject downloadObject = e.getAsJsonObject();
    
    Long fileId = downloadObject.get("package_file_id").getAsLong();

    // You should catch an IOException around all of the following code.
    // It's not caught here for brevity.
    URL downloadUrl = new URL(downloadObject.get("downloadURL").getAsString());
    String name = files.get(fileId);
    
    // Begin the download.
    ReadableByteChannel channel = Channels.newChannel(downloadUrl.openStream());
    
    // Process file name to make this multiplat
    name = name.replace("/", File.seperator);
    File result = new File("somedownloaddir", name);
    
    // Since packages have their own file structures, having
    // this here will ensure that all required directories are
    // created based off of the remote.
    if (!result.getParentFile().exists()) {
        result.getParentFile().mkdirs();
    }
    
    // Actually write the data to the file.
    FileOutputStream fileOut = new FileOutputStream(result);
    fileOut.getChannel().transferFrom(channel, 0, Long.MAX_VALUE);
});
```

Endpoints used in this example:
- [Authentication](../endpoints/AUTHENTICATION.md)
- [Get Package Files](../endpoints/GET_PACKAGE_FILES.md)
- [Batch Generate Presigned URLS](../endpoints/BATCH_GENERATE_PRESIGNED_URLS.md)
- [Get Package Files from S3 Link](../endpoints/GET_PACKAGE_FILES_FROM_S3.md)