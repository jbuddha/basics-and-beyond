---
title: How to read S3 CSV files into hashmaps using OpenCSV
date: 2021-01-17
tags: ['java', 's3', 'aws', 'opencsv', 'data', 'files', 'example']
author: Jyothi Prasad Buddha
description: Gives an example of how to use opencsv in java to read csv files as list of hash maps from S3
---

In this world where large amounts of data is becoming a norm, it is very frequently stored in S3 in csv format for consumption through serverless database layers such as Athena. However, you often have to read the csv files without using Athena. In such cases, you can use ever useful libraries such as OpenCSV to read csv files.

This example shows how to use opencsv to quickly read the S3 files without the need to download them first. This helps when you do not have a way to save files locally of if you don't have enough hard disk space. The solution is quite simple. You just have to create an InputStream from an S3 object using getObject method on S3 client. Once the input stream is created, we can use this to create a CSVReader from it.
<!-- more -->
Assuming that the CSV files have a header row, you can use CSVReaderHeaderAware class to create a list of hashmaps by reading each record iteratively using readMap method. If readMap method returns null, this means that you have reached end of file. Here is a complete solution for your reference.

{% codeblock lang:java %}
import java.io.*;
import java.util.*;

import com.opencsv.*;
import com.opencsv.exceptions.CsvValidationException;

import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.S3Object;

public class S3CSVReader {

    public static void main(String... args) throws IOException, CsvValidationException {
        // Example Usage
        S3CSVReader reader = new S3CSVReader();
        List<Map<String, String>> records = reader.getS3Records("my-bucket", "input-data/file1.csv");
        System.out.println(records);
    }

    public List<Map<String, String>> getS3Records(String bucket, String key) throws IOException, CsvValidationException {
        List<Map<String, String>> records = new ArrayList<>();
        try (CSVReaderHeaderAware reader = getReader(bucket, key)) {
            Map<String, String> values;

            while ((values = reader.readMap()) != null) {
                records.add(values);
            }
            return records;
        }
    }

    private CSVReaderHeaderAware getReader(String bucket, String key) {
        CSVParser parser = new CSVParserBuilder().build();
        S3Object object = getS3().getObject(bucket, key);
        var br = new InputStreamReader(object.getObjectContent());
        return (CSVReaderHeaderAware) new CSVReaderHeaderAwareBuilder(br)
                .withCSVParser(parser)
                .build();
    }

    private AmazonS3 getS3() {
        return AmazonS3ClientBuilder.standard()
                .withCredentials(new ProfileCredentialsProvider("aws-profile"))
                .withRegion(Regions.US_WEST_2)
                .build();
    }
}
{% endcodeblock %}

 <!-- more -->

Running above code prints out all the records from the file. Sample output for the file I have used is as below.

``` Heading Input File - S3://my-bucket/input-data/file1.csv
col_1,col_2,col_3,col_4
1,large,2.12,NA
1,medium,3.13,NA
```

``` Heading Console Output
[{col_1=1, col_2=large, col_3=2.12, col_4=NA}, {col_1=1, col_2=medium, col_3=3.13, col_4=NA}]
```

If you want to understand the code here is the flowchart of the algorithm.

<img src="/assets/svgs/2021/01/read-s3-files-using-opencsv-algorithm.svg" alt="Algorithm of reading s3 csv files using OpenCSV">

In this snippet, we have two helper methods `getReader` to help with creation of the reader object that is aware of header row and 'getS3' to help us create an S3 client. Please change the aws credentials profile to the one you are using in your computer. If you configured default profile you can also use `AmazonS3ClientBuilder.defaultClient()` for creating S3 client.

If you want to create a reader for TSV files instead of CSV you can create a different parser object such as below. You can also use any custom separators while building the parser.

{% codeblock lang:java %}
CSVParser parser = new CSVParserBuilder().withSeparator('\t').build();
{% endcodeblock %}

In order to use this code, you can create an object of S3CSVReader class and invoke `getS3Records` method by passing the S3 bucket name and key path of the CSV file in S3. This method creates a reader object and iterates through all records to create a List of HashMaps and returns the result.

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

For understanding how to import dependencies using other build systems, such as gradle go to the corresponding maven artifact pages such as https://mvnrepository.com/artifact/com.opencsv/opencsv and select appropriate tab.dependencies

## Test Code

Assuming that you have configured the aws profile correctly and give the bucket name and s3, the following code should produce an output of all the records.

{% codeblock lang:java %}
import java.io.IOException;
import java.util.List;
import java.util.Map;

import org.junit.jupiter.api.Test;
import com.opencsv.exceptions.CsvValidationException;

public class S3CSVReaderTest {

    @Test
    public void simpleTest() throws IOException, CsvValidationException {
        S3CSVReader reader = new S3CSVReader();
        List<Map<String, String>> records = reader.getS3Records("my-bucket", "input-data/input_1.txt");
        System.out.println(records);
    }
}
{% endcodeblock %}


---
