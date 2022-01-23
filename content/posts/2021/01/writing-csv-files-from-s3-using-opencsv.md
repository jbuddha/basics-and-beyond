---
title: How to use OpenCSV to write CSV files to S3
date: 2021-01-24
tags: ['java', 's3', 'aws', 'opencsv', 'data', 'files', 'example']
author: Jyothi Prasad Buddha
description: Gives an example of how to use opencsv in java to write csv files to S3
---

In the previous post about [How to read csv files from S3 using OpenCSV](/2021/01/reading-csv-files-from-s3-using-opencsv/), we have seen how to open and files on S3 and read the comma separated data into list of hashmaps. In this article, we will see how to perform the reverse, writing data to the files.

Most of the fundamental concepts do not change. You need to create S3 client if you want to do anything with S3. The AWS profile configured should be of the user or role that has permissions to write to S3. The bucket policy should allow writing files. I'm not going to cover how to setup aws credentials and IAM policies in this post. The method getS3() in the complete code snippet below is going to return an S3 client just like in the previous post.
<!-- more -->
In order for us to write CSV files using OpenCSV, you need to create a `CSVWriter` object. It allows us to write text to a stream and redirect them to a csv file. Here is how you can create a writer.

{% codeblock lang:java %}
private CSVWriter buildCSVWriter(OutputStreamWriter streamWriter) {
    return new CSVWriter(streamWriter, ',', Character.MIN_VALUE, '"', System.lineSeparator());
}
{% endcodeblock %}

In this code, we create a CSVWriter by using the constructor that accepts an output stream writer, comma as separator, `\u00000 or Character.MIN_VALUE` as quote character, `"` as escape character and new line as the separator between lines. If you want to use a different separators such as TAB instead of Comma, you can do so by changing the second parameter. Once the writer is created, we can send the data to output stream and the data will be automatically written to the file. Following snippet achieves the same.

{% codeblock lang:java %}
public void writeRecords(List<String[]> lines) throws IOException {
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    OutputStreamWriter streamWriter = new OutputStreamWriter(stream, StandardCharsets.UTF_8);
    try (CSVWriter writer = buildCSVWriter(streamWriter)) {
        writer.writeAll(lines);
    }
}
{% endcodeblock %}

In above code snippet, you can see that we accept list of string arrays. If you want to write headers as well, the first string array of the list must include headers. Once all the data has been written, you can use putObject of the S3 client to upload the content as a file to the given path and bucket. However the putObject method of S3 client doesn't accept an output stream, it accepts an input stream so you can just wrap the previously created output stream into an input stream. You also need to set metadata such as Content length yourself. Following snippet provides you code for how to do that.

{% codeblock lang:java %}
ObjectMetadata meta = new ObjectMetadata();
meta.setContentLength(stream.toByteArray().length);
getS3().putObject(BUCKET, PATH, new ByteArrayInputStream(stream.toByteArray()), meta);
{% endcodeblock %}

Putting everything together, following code code combines all the snippets we have seen before.

{% codeblock lang:java %}
import java.io.*;
import java.util.*;
import java.nio.charset.StandardCharsets;

import com.opencsv.CSVWriter;

import com.amazonaws.services.s3.*;
import com.amazonaws.regions.Regions;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.s3.model.ObjectMetadata;

public class S3CSVWriter {

    private final static String BUCKET = "buddha-test-bucket";
    private final static String PATH = "buddha/output/out.txt";
    private final static String AWS_PROFILE = "aws-profile";

    public static void main(String... args) throws IOException {
        // Example Usage
        S3CSVWriter writer = new S3CSVWriter();

        List<String[]> lines = Arrays.asList(
                new String[] { "col1", "col2", "col3" },
                new String[] { "1", "large", "5" },
                new String[] { "2", "small", "2" }
        );
        writer.writeRecords(lines);
    }

    public void writeRecords(List<String[]> lines) throws IOException {
        ByteArrayOutputStream stream = new ByteArrayOutputStream();
        OutputStreamWriter streamWriter = new OutputStreamWriter(stream, StandardCharsets.UTF_8);
        try (CSVWriter writer = buildCSVWriter(streamWriter)) {
            writer.writeAll(lines);
            writer.flush();
            ObjectMetadata meta = new ObjectMetadata();
            meta.setContentLength(stream.toByteArray().length);
            getS3().putObject(BUCKET, PATH, new ByteArrayInputStream(stream.toByteArray()), meta);
        }
    }

    private CSVWriter buildCSVWriter(OutputStreamWriter streamWriter) {
        return new CSVWriter(streamWriter, ',', Character.MIN_VALUE, '"', System.lineSeparator());
    }

    private AmazonS3 getS3() {
        return AmazonS3ClientBuilder.standard()
                                    .withCredentials(new ProfileCredentialsProvider(AWS_PROFILE))
                                    .withRegion(Regions.US_WEST_2)
                                    .build();
    }
}
{% endcodeblock %}

In the above code, the main function creates a list of String arrays with first array as column headers and next two lines as the data. An object of `S3CSVWriter` has been created and it's writeRecords method is invoked by passing the array list. Another change in writeRecords method is that we used `writer.flush()` before attempting to write data to S3. This will ensure that the data that has been written using `writeAll` method is not retained in buffer and instead written to the stream.

## Dependencies

Assuming that you are using Maven, you need to add the following dependencies to your pom.xml to add opencsv to your project irrespective of what browser you may use.

{% codeblock lang:xml %}
<!-- https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-s3 -->
<dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-java-sdk-s3</artifactId>
    <version>1.11.939</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.opencsv/opencsv -->
<dependency>
    <groupId>com.opencsv</groupId>
    <artifactId>opencsv</artifactId>
    <version>5.3</version>
</dependency>
{% endcodeblock %}

For understanding how to import dependencies using other build systems, such as gradle go to the corresponding maven artifact pages such as https://mvnrepository.com/artifact/com.opencsv/opencsv and select appropriate tab.

---
