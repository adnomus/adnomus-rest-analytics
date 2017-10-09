adNomus Real-Time Analytics APIs

## File Contents
 * Introduction
 * API Model
 * Content Targeting API
 * Content Relevance API
 * Content Search and Indexing API
 * Follow-up


## Introduction
adNomus Real-Time AI technologies deliver content targeting for advertising, content discovery and contextual search purposes. Our technology enables the instant and detailed matching of web/platform content to relevant 3rd party content (e.g. ads, content recommendations, search results). Our solution delivers within 1ms while guaranteeing user and content privacy. Our targeting capabilities serve web and mobile platforms and support interactive, real-time user experiences (e.g. messaging, chatbots, search).

This repository contains the documentation of our Analytics Rest APIs. We provide:
* **API Model:** We define a set of patterns of semantics that we use across our APIs.
* **Representative Examples:** We provide straightforward API usage examples for fast understanding and testing.


## API Model
The base url for our APIs is the following:
```url
https://api.adnomus.com/api/v1.0
```
We exclusively support the HTTPS protocol to ensure privacy and security.

We use a unified model across our APIs. The user sends a request and we return a response while we follow a set of patterns and semantics which are described below.

### Request
Each **Request** is an HTTPS post operation with a body containing JSON content of the following schema:

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "ReplacewithSpecificRequest": {
    "specific": "entries"
  }
}
```
The JSON content is a dictionary containing 2 nested dictionaries:
 *   **Authentication:** This dictionary provides your credentials for using the service and is required by every request. Here, we include testing credentials but you will need to get your production credentials.
 *   **Request Specific:** This dictionary is API specific.

**Note:** Our analytics support requests with diverse content sizes. In order to make service access more efficient for long content requests, we support compression. We support standard **gzip** compression.

### Response
Our service provides a response in JSON format. We differentiate for **successful** and **failed** response scenarios.

#### Successful Response
The HTTPS response code is 200.
The response body aligns with the following schema:

```json
{
  "ResponseStatus": {
    "response_type": "success"
  },
  "ReplacewithSpecificResponse": {
    "specific" :"entries"
  }
}
```

The JSON content is a dictionary containing 2 nested dictionaries:
* **Response Status:** The response status with an entry of "response_type":"success"
* **Response Specific:** This dictionary is API specific.

#### Failed Response
The HTTPS response code is 400.

The response body aligns with the following schema:

```json
{
  "ResponseStatus": {
    "response_type": "failure",
    "failure_type:": "reason of failure"
  }
}
```

The JSON content is a dictionary containing 1 nested dictionary:
* **Response Status:** The response status with an entry of "response_type":"failure" and a second entry describing the failure type.

## Content Targeting API
### Functionality
The user provides arbitrary content and we deliver
a set of descriptive terms. Those terms are either generic English terms
we generate or belong to a set of desired terms provided by the user. This
API is ideal for ad targeting (matching to keywords, categories etc) and
content characterization.

### Example Use 1 -- (generic targeting terms, no scores)
#### Request Example


```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "TargetingRequest": {
    "request_type": "terms",
    "content": "I am thinking to buy a new sports car, maybe a BMW or Corvette.",
    "num_req_terms": 5
  }
}
```
#### Service Response

```json
{
  "ResponseStatus": {
    "response_type": "success"
    },
    "TargetingResponse": {
      "terms": ["car", "bmw", "corvette", "camaro", "lfa"]
    }
}
```
#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/targeting" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"TargetingRequest\": { \"request_type\": \"terms\", \"content\": \"I am thinking to buy a new sports car, maybe a bmw or Corvette.\", \"num_req_terms\": 5 }}"
```



### Example Use 2 -- (generic targeting terms, with scores)
#### Request Example

```json
{
  "Authenticatinn": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "TargetingRequest": {
    "request_type": "terms+scores",
    "content": "I am thinking to buy a new sports car, maybe a bmw or Corvette.",
    "num_req_terms": 5
  }
}
```

#### Service Response

```json
{
  "ResponseStatus": {
    "response_type": "success"
    },
    "TargetingResponse": {
      "terms": ["car", "bmw", "corvette", "camaro", "lfa"], "scores": [1.0, 0.9694421291351318, 0.9000124335289001, 0.8997970819473267, 0.8463729619979858]
    }
}
```
#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/targeting" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"TargetingRequest\": { \"request_type\": \"terms+scores\", \"content\": \"I am thinking to buy a new sports car, maybe a bmw or Corvette.\", \"num_req_terms\": 5 }}"
```


### Example Use 3 -- (user defined targeting terms, no scores)
#### Request Example

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "TargetingRequest": {
    "request_type": "user_terms",
    "content": "I am thinking to buy a new sports car, maybe a bmw or Corvette.",
    "num_req_terms": 5,
    "user_terms": [
      "car",
      "bmw",
      "race",
      "speed",
      "auto-motive",
      "dealership",
      "art",
      "physics",
      "camping"
    ]
  }
}
```
#### Service Response

```json
{
  "ResponseStatus": {
    "response_type": "success"
    },
    "TargetingResponse": {
      "terms": ["car", "bmw", "race", "auto-motive", "dealership"]
    }
}
```
#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/targeting" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"TargetingRequest\": { \"request_type\": \"user_terms\", \"content\": \"I am thinking to buy a new sports car, maybe a bmw or Corvette.\", \"num_req_terms\": 5, \"user_terms\": [ \"car\", \"bmw\", \"race\", \"speed\", \"auto-motive\", \"dealership\", \"art\", \"physics\", \"camping\" ] }}"

```

### Example Use 4 -- (user defined targeting terms, with scores)
#### Request Example

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "TargetingRequest": {
    "request_type": "user_terms+scores",
    "content": "I am thinking to buy a new sports car, maybe a bmw or Corvette",
    "num_req_terms": 5,
    "user_terms": [
      "car",
      "bmw",
      "race",
      "speed",
      "auto-motive",
      "dealership",
      "art",
      "physics",
      "camping"
    ]
  }
}
```

#### Service Response


```json
{
  "ResponseStatus": {
    "response_type": "success"},
     "TargetingResponse": {
       "terms": ["car", "bmw", "race", "auto-motive", "dealership"],
       "scores": [1.0, 0.9694421291351318, 0.7396454811096191, 0.7337338328361511, 0.6869076490402222]
     }
}
```
#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/targeting" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"TargetingRequest\": { \"request_type\": \"user_terms+scores\", \"content\": \"I am thinking to buy a new sports car, maybe a bmw or Corvette.\", \"num_req_terms\": 5, \"user_terms\": [ \"car\", \"bmw\", \"race\", \"speed\", \"auto-motive\", \"dealership\", \"art\", \"physics\", \"camping\" ] }}"

```

### Interesting Use Cases
**Contextual Ad Targeting:** Use the API to retrieve terms(keywords) that
characterize your content. You can rely on us to provide generic terms
or you can explicitly define the terms you are interested into and we
do the targeting.

**Content Characterization**: Retrieve terms that describe your content,
again you have the option to specify the set of terms we target.

**Content Classification:** Use this API to classify your content to
categories of your choice. Here, you can specify any category you require
in plain English e.g. "cars", "Italian food", "computer security news". A
category can be a single word or a phrase.


## Content Relevance API
### Functionality
The user provides a reference content and a set of
extrinsic (e.g. external/third party) contents. We deliver contextual
ordering (e.g. Extrinsic content A is more relevant to the Reference
Content than extrinsic content B). This API is ideal for content
recommendation and service discovery.

### Example Use 1 -- (relevance ordering, no scores)
#### Request Example

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "RelevanceRequest": {
    "request_type": "ordering",
    "reference_content": "I am thinking to buy a new sports car, maybe a BMW or Corvette.",
    "extrinsic_contents": {
      "id0": "I like travelling to the mountains and doing camping.",
      "id1": "New BMW and AUDI cars are amazing cars but American models like Camaro set the competition bar high.",
      "id2": "I am thinking of buying a car or a motorbike for racing.",
      "id3": "Pop music releases of the last years are pretty mediocre productions."
    }
  }
}
```
#### Service Response

```json
{
  "RelevanceResponse": {
    "ordering": ["id1", "id2", "id3", "id0"]}, "ResponseStatus": {
      "response_type": "success"
    }
}
```
#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/relevance" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"RelevanceRequest\": { \"request_type\": \"ordering\", \"reference_content\": \"I am thinking to buy a new sports car, maybe a BMW or Corvette.\", \"extrinsic_contents\": { \"id0\": \"I like travelling to the mountains and doing camping.\", \"id1\": \"New BMW and AUDI cars are amazing cars but American models like Camaro set the competition bar high.\", \"id2\": \"I am thinking of buying a car or a motorbike for racing.\", \"id3\": \"Pop music releases of the last years are pretty mediocre productions.\" } }}"
```

### Example Use 2 -- (relevance ordering, with scores)
#### Request Example

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "RelevanceRequest": {
    "request_type": "ordering+scores",
    "reference_content": "I am thinking to buy a new sports car, maybe a BMW or Corvette.",
    "extrinsic_contents": {
      "id0": "I like travelling to the mountains and doing camping.",
      "id1": "New BMW and AUDI cars are amazing cars but American models like Camaro set the competition bar high.",
      "id2": "I am thinking of buying a car or a motorbike for racing.",
      "id3": "Pop music releases of the last years are pretty mediocre productions."
    }
  }
}
```
#### Service Response

```json
{
  "RelevanceResponse": {
    "ordering": ["id1", "id2", "id3", "id0"],
    "scores": [1.0, 0.958858072757721, 0.31503477692604065, 0.2469681054353714]},
    "ResponseStatus": {
      "response_type": "success"
    }
}
```
#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/relevance" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"RelevanceRequest\": { \"request_type\": \"ordering+scores\", \"reference_content\": \"I am thinking to buy a new sports car, maybe a BMW or Corvette.\", \"extrinsic_contents\": { \"id0\": \"I like travelling to the mountains and doing camping.\", \"id1\": \"New BMW and AUDI cars are amazing cars but American models like Camaro set the competition bar high.\", \"id2\": \"I am thinking of buying a car or a motorbike for racing.\", \"id3\": \"Pop music releases of the last years are pretty mediocre productions.\" } }}"

```

### Interesting Use Cases
**Content Recommendation:** There are multiple use cases (e.g. social
media) where you need to recommend relevant content to your user (show
her something relevant to what she is reading or writing). This API
allows you to do that.

**Service Recommendation:** In many scenarios, you need to suggest your
user some actions such as i) listen to a song, ii) buy a concert ticket,
iii) take care of some task. The best way to do this is by naturally
connecting those actions to the content the user reads or writes.
Please, consider the following approach: The reference content here
is your user's content and the extrinsic contents the actions you want
to recommend, described in plain English e.g. "listen to the new Metallica
song", "get a ticket to Mexico for vacation".


## Content Search and Indexing API
### Functionality
Content Search and Indexing API: The user provides an inventory description
(e.g. marketplace/content items). We generate a contextual indexing and allow
the user to search their inventory in natural language. This is NLP driven
search that goes beyond naive keyword matching and typical pattern
matching found in standard search engines.


### Example Use 1 -- (search, no scores)
#### Request Example

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "SearchRequest": {
    "request_type": "search",
    "query": "I am looking for car related products.",
    "num_req_results": 3,
    "inventory_entries": {
      "id0": "$100 Giftcard, buy your favorite clothing online.",
      "id1": "This car tuner accessory gives your car additional velocity and smooth suspension.",
      "id2": "New phone device allows you to surf the internet with voice commands.",
      "id3": "The ultimate travelling guide for Africa, Europe and Asia.",
      "id4": "AUDI and BMW lease voucher, save 5k for your next car."
    }
  }
}
```
#### Service Response

```json
{
  "ResponseStatus": {
    "response_type": "success"
  },
    "SearchResponse": {"results": ["id4", "id1", "id0"]}
}
```

#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/search" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"SearchRequest\": { \"request_type\": \"search\", \"query\": \"I am looking for car related products.\", \"num_req_results\": 3, \"inventory_entries\": { \"id0\": \"$100 Giftcard, buy your favorite clothing online.\", \"id1\": \"This car tuner accessory gives your car additional velocity and smooth suspension.\", \"id2\": \"New phone device allows you to surf the internet with voice commands.\", \"id3\": \"The ultimate travelling guide for Africa, Europe and Asia.\", \"id4\": \"AUDI and BMW lease voucher, save 5k for your next car.\" } }}"
```

### Example Use 2 -- (search, with scores)
#### Request Example

```json
{
  "Authentication": {
    "network": "test_network",
    "key": "fff",
    "user": "ad_server"
  },
  "SearchRequest": {
    "request_type": "search+scores",
    "query": "I am looking for car related products.",
    "num_req_results": 3,
    "inventory_entries": {
      "id0": "$100 Giftcard, buy your favorite clothing online.",
      "id1": "This car tuner accessory gives your car additional velocity and smooth suspension.",
      "id2": "New phone device allows you to surf the internet with voice commands.",
      "id3": "The ultimate travelling guide for Africa, Europe and Asia.",
      "id4": "AUDI and BMW lease voucher, save 5k for your next car."
    }
  }
}
```
#### Service Response

```json
{
  "ResponseStatus": {
    "response_type": "success"
    },
    "SearchResponse": {
      "results": ["id4", "id1", "id0"], "scores": [1.0, 0.9779075980186462, 0.6858365535736084]
    }
}
```

#### Curl Example

```curl
curl -X POST "https://api.adnomus.com/api/v1.0/search" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"Authentication\": { \"network\": \"test_network\", \"key\": \"fff\", \"user\": \"ad_server\" }, \"SearchRequest\": { \"request_type\": \"search+scores\", \"query\": \"I am looking for car related products.\", \"num_req_results\": 3, \"inventory_entries\": { \"id0\": \"$100 Giftcard, buy your favorite clothing online.\", \"id1\": \"This car tuner accessory gives your car additional velocity and smooth suspension.\", \"id2\": \"New phone device allows you to surf the internet with voice commands.\", \"id3\": \"The ultimate travelling guide for Africa, Europe and Asia.\", \"id4\": \"AUDI and BMW lease voucher, save 5k for your next car.\" } }}"

```

### Interesting Use Cases
**Precise Content Search:** You can use this API to perform contextual
search on your content inventory (e.g. social media content). Our
search technology goes beyond keyword and pattern matching and delivers
high quality results.

**Marketplace Inventory Indexing and Search:** Modern marketplaces
have hundreds of millions of product entries. Traditional search
technologies fail in scaling and precision. This API fixes this.

**Contextual Indexing and Search of Ad Inventories:** Use this API
to contextually organize and search your advert inventory. We provide
you a solution for effective ad targeting and indexing.


## Follow-up
### Register with us
Register with us to receive your credentials and start using our service.

### In-house Solutions
Your service needs may require the transfer of terabytes
of data in daily base and you may require the caching of your API requests. We can
install, configure and support our services and technologies as part of your in-house
infrastructure or cloud.

### Contact information
Visit our [website](http://www.adnomus.com).
Email us at: contact@adnomus.com
