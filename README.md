# Serverless REST API in Java/Maven using DynamoDB


![image](https://user-images.githubusercontent.com/8188/38645675-ec708d0e-3db2-11e8-8f8b-a4a37ed612b9.png)


The sample serverless service will create a REST API for products. It will be deployed to AWS. The data will be stored in a DynamoDB table.

This is a companion app for the blog post [REST API in Java using DynamoDB and Serverless](https://serverless.com/blog/how-to-create-a-rest-api-in-java-using-dynamodb-and-serverless/).

## Install Pre-requisites

* `node` and `npm`
* Install the JDK and NOT the Java JRE from [Oracle JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html). And set the following:
`export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-10.jdk/Contents/Home`
* [Apache Maven](https://maven.apache.org/). After [downloading](https://maven.apache.org/download.html) and [installing](https://maven.apache.org/install.html) Apache Maven, please add the `apache-maven-x.x.x` folder to the `PATH` environment variable.

### Test Pre-requisites

Test Java installation:

```
$ java --version

java 10 2018-03-20
Java(TM) SE Runtime Environment 18.3 (build 10+46)
Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10+46, mixed mode)
```

Test Maven installation:

```
$ mvn -v

Apache Maven 3.5.3 (3383c37e1f9e9b3bc3df5050c29c8aff9f295297; 2018-02-24T14:49:05-05:00)
Maven home: /usr/local/apache-maven-3.5.3
Java version: 10, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk-10.jdk/Contents/Home
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.13.3", arch: "x86_64", family: "mac"
```

## Build the Java project

Create the java artifact (jar) by:

```
$ cd aws-java-products-api
$ mvn clean install

[INFO] Scanning for projects...
[INFO]
[INFO] --------------------< com.serverless:products-api >---------------------
[INFO] Building products-api dev
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ products-api ---
[INFO] Deleting /Users/rupakg/projects/svrless/apps/aws-java-products-api/target

...
...

[INFO] --- maven-install-plugin:2.4:install (default-install) @ products-api ---
[INFO] Installing /Users/rupakg/projects/svrless/apps/aws-java-products-api/target/products-api-dev.jar to /Users/rupakg/.m2/repository/com/serverless/products-api/dev/products-api-dev.jar
[INFO] Installing /Users/rupakg/projects/svrless/apps/aws-java-products-api/pom.xml to /Users/rupakg/.m2/repository/com/serverless/products-api/dev/products-api-dev.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.790 s
[INFO] Finished at: 2018-04-08T19:58:15-04:00
[INFO] ------------------------------------------------------------------------
```

We can see that we have an artifact in the `target` folder named `products-api-dev.jar`.

## Deploy the serverless app

```
$ sls deploy

Serverless: Packaging service...
Serverless: Creating Stack...
Serverless: Checking Stack create progress...
.....
Serverless: Stack create finished...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
..................................
Serverless: Stack update finished...
Service Information
service: products-api
stage: dev
region: us-east-1
stack: products-api-dev
api keys:
  None
endpoints:
  GET - https://xxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products
  GET - https://xxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products/{id}
  POST - https://xxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products
  DELETE - https://xxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products/{id}
functions:
  listProducts: products-api-dev-listProducts
  getProduct: products-api-dev-getProduct
  createProduct: products-api-dev-createProduct
  deleteProduct: products-api-dev-deleteProduct
```

## Test the API

Let's invoke each of the four functions that we created as part of the app.

### Create Product

```
$ curl -X POST https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products -d '{"name": "Product1", "price": 9.99}'

{"id":"ba04f16b-f346-4b54-9884-957c3dff8c0d","name":"Product1","price":9.99}
```

### List Products

```
$ curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products

[{"id":"dfe41235-0fe5-4e6f-9a9a-19b7b7ee79eb","name":"Product3","price":7.49},
{"id":"ba04f16b-f346-4b54-9884-957c3dff8c0d","name":"Product1","price":9.99},
{"id":"6db3efe0-f45c-4c5f-a73c-541a4857ae1d","name":"Product4","price":2.69},
{"id":"370015f8-a8b9-4498-bfe8-f005dbbb501f","name":"Product2","price":5.99},
{"id":"cb097196-d659-4ba5-b6b3-ead4c07a8428","name":"Product5","price":15.49}]
```

**No Product(s) Found:**

```
$ curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products

[]
```

### Get Product

```
$ curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products/ba04f16b-f346-4b54-9884-957c3dff8c0d

{"id":"ba04f16b-f346-4b54-9884-957c3dff8c0d","name":"Product1","price":9.99}
```

**Product Not Found:**

```
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products/xxxx

"Product with id: 'xxxx' not found."
```

### DeleteProduct

```
$ curl -X DELETE https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products/24ada348-07e8-4414-8a8f-7903a6cb0253
```

**Product Not Found:**

```
curl -X DELETE https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/dev/products/xxxx

"Product with id: 'xxxx' not found."
```

## View the CloudWatch Logs

```
$ serverless logs --function getProduct

START RequestId: 34f45684-3dd0-11e8-bf8a-7f961671b2de Version: $LATEST
...

2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> DEBUG org.apache.http.wire:86 - http-outgoing-0 >> "{"TableName":"java-products-dev","ConsistentRead":true,"ScanIndexForward":true,"KeyConditionExpression":"id = :v1","ExpressionAttributeValues":{":v1":{"S":"6f1dfeb9-ea08-4161-8877-f6cc724b39e3"}}}"

...

2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> DEBUG org.apache.http.wire:86 - http-outgoing-0 << "{"Count":1,"Items":[{"price":{"N":"9.99"},"id":{"S":"6f1dfeb9-ea08-4161-8877-f6cc724b39e3"},"name":{"S":"Product1"}}],"ScannedCount":1}"

...

2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> DEBUG org.apache.http.impl.conn.PoolingHttpClientConnectionManager:314 - Connection [id: 0][route: {s}->https://dynamodb.us-east-1.amazonaws.com:443] can be kept alive for 60.0 seconds
2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> DEBUG org.apache.http.impl.conn.PoolingHttpClientConnectionManager:320 - Connection released: [id: 0][route: {s}->https://dynamodb.us-east-1.amazonaws.com:443][total kept alive: 1; route allocated: 1 of 50; total allocated: 1 of 50]
2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> DEBUG com.amazonaws.request:87 - Received successful response: 200, AWS Request ID: MT1EV3AV07T9OD0MJH9VBJSIB7VV4KQNSO5AEMVJF66Q9ASUAAJG
2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> DEBUG com.amazonaws.requestId:136 - x-amzn-RequestId: MT1EV3AV07T9OD0MJH9VBJSIB7VV4KQNSO5AEMVJF66Q9ASUAAJG
2018-04-11 21:35:14 <34f45684-3dd0-11e8-bf8a-7f961671b2de> INFO  com.serverless.dal.Product:107 - Products - get(): product - Product [id=6f1dfeb9-ea08-4161-8877-f6cc724b39e3, name=Product1, price=$9.990000]
END RequestId: 34f45684-3dd0-11e8-bf8a-7f961671b2de
REPORT RequestId: 34f45684-3dd0-11e8-bf8a-7f961671b2de	Duration: 5147.00 ms	Billed Duration: 5200 ms 	Memory Size: 1024 MB	Max Memory Used: 97 MB
```

## View the Metrics

View the metrics for the service:

```
$ serverless metrics

Service wide metrics
April 2, 2018 2:11 PM - April 3, 2018 2:11 PM

Invocations: 2
Throttles: 0
Errors: 0
Duration (avg.): 331.23ms
```

Or, view the metrics for only one function:

```
$ serverless metrics --function hello

hello
April 2, 2018 2:13 PM - April 3, 2018 2:13 PM

Invocations: 2
Throttles: 0
Errors: 0
Duration (avg.): 331.23ms
```
