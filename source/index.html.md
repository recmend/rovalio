---
title: Roval API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: curl

toc_footers:
- Backed by <a href='https://candor.ws' target='_blank'>Candor</a>
---

# Introduction

```shell
https://api.roval.io
```

Welcome to Roval! This API reference provides information on available endpoints and how to interact with it.

The Roval API is organized around [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer). Our API has predictable, resource-oriented URLs, and uses HTTP response codes to indicate API errors. All requests should be over SSL. All request and response bodies, including errors are encoded in JSON.

# Authentication

> Example Request

```shell
$ curl https://api.roval.io/avatar \
  -H "Authorization: your_api_key"
```

Authentication is done via the API key which you can find in your account settings. Your API keys carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

Authentication to the API is performed via [HTTP Basic Auth](http://en.wikipedia.org/wiki/Basic_access_authentication). Provide your API key as the basic auth username value. You do not need to provide a password.

Alternatively you can also pass your API key as a bearer token in an Authorization header.

`-H "Authorization: Bearer *your_api_key*"`

All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

# Errors

Our API returns standard HTTP success or error status codes. For errors, we will also include extra information about what went wrong encoded in the response as JSON. The various HTTP status codes we might return are listed below.

In general: Codes in the 2xx range indicate success. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a charge failed, etc.). Codes in the 5xx range indicate an error with Roval's servers (these are rare).

## HTTP Status codes

Code | Title | Description
---- | ----- | -----------
200 | OK | Everything worked as expected.
400 | Bad Request | The request was unacceptable, often due to missing a required parameter.
401 | Unauthorized | No valid API key provided.
402 | Request Failed | The parameters were valid but the request failed.
404 | Not Found | The requested resource doesn't exist.
409 | Conflict | The request conflicts with another request (perhaps due to using the same idempotent key).
429 | Too Many Request | Too many requests hit the API too quickly. We recommend an exponential backoff of your requests.
500,502,503,504 | Server Errors | Something went wrong on Roval's end. (These are rare.)

## Error types

> Example error response.

```json
{
  "error": {
    "type": "invalid_request",
    "message": "name is required"
  }
}
```

Type | Description
---- | -----------
api_connection | Failure to connect to Roval's API.
api_error | API errors cover any other type of problem (e.g., a temporary problem with our servers), and are extremely uncommon.
authentication_error | Failure to properly authenticate yourself in the request.
invalid_request | Invalid request errors arise when your request has invalid parameters.
rate_limit_exceeded | Too many requests hit the API too quickly.
validation_error | Unable to validate POST/PUT
not_found | Resource was not found.

# Rate limiting

> Example rate limit error response.

```json
{
  "error": [
    {
      "type": "rate_limit_exceeded",
      "message": "Too many requests"
    }
  ]
}
```

The Roval API is rate limited to prevent abuse that would degrade our ability to maintain consistent API performance for all users. By default, each API key or app is rate limited at 10,000 requests per hour. If your requests are being rate limited, HTTP response code 429 will be returned with an rate_limit_exceeded error.

# Avatar

The avatar API lets you retrieve a person's avatar using person's email address or name. The avatar API checks if an associated profile with Gravatar.

> Example Request

```shell
curl -X GET https://api.roval.io/avatar?email=john@example.org&name=John+Doe \
  -H "Bearer: sk_yourapikey" \
```

> You can call the API directly from an html img tag - images are returned inline. For example:

```html
<img src="https://api.roval.io/avatar?email=john@example.org&name=John+Doe">
```

> Return's person gravatar image

> <img src="https:\/\/randomuser.me\/api\/portraits\/men\/43.jpg" alt="John Doe Avatar" width="100" height="100">

> When no Gravatar profile is found, it generates the avatar image using person's initials.

> <img src="https://api.nexus150.com/avatar?email=john@example.org&name=John+Doe" alt="John Doe Avatar" width="100" height="100">

### HTTP Request

`GET https://api.roval.io/avatar?email={email}&name={name}`

**ARGUMENTS**

Parameter | Type | Required
--------- | ---- | -----------
`email` | string | required
`name`   | string | optional

# Company

The company API lets you retrieve a company's social information.

> Example Request

```shell
curl -X GET https://api.roval.io/company?domain=example.com \
  -H "Bearer: sk_yourapikey" \
```

> Example Response

```json
{
  "type": "website",
  "url": "https://segment.com/",
  "root_url": "http://segment.com",
  "title": "Segment | Customer Data Infrastructure (CDI) ",
  "description": "Segment is a customer data infrastructure (CDI) platform that helps you collect, clean, and control your customer data.",
  "logo": "https://logo.clearbit.com/segment.com",
  "site_name": "Segment",
  "images": [
    {
      "url": "https://images.ctfassets.net/9u2t3ap6pctb/5UpSS2tTa6CfSbfwUjAdqL/2140169b9f7258ede8a839dc79766a26/OG-home.jpg"
    }
  ]
}
```

### HTTP Request

`GET https://api.roval.io/logo?domain={domain}`

**ARGUMENTS**

Parameter | Type | Required
--------- | ---- | -----------
`domain` | string | required

# Favicon

The favicon API lets you generate a website favicon icon.

> Example Request

```shell
curl -X GET https://api.roval.io/favicon?name=Segment \
  -H "Bearer: sk_yourapikey" \
```

> You can call the API directly from an html link tag - images are returned inline. For example:

```html
<link rel="shortcut icon" id="favicon" href="https://api.roval.io/favicon?name=Segment" />
```

> <img src="https://api.nexus150.com/favicon?name=Segment">

### HTTP Request

`GET https://api.roval.io/favicon?name={name}`

**ARGUMENTS**

Parameter | Type | Required
--------- | ---- | -----------
`name`   | string | required

# Extract

The extract API lets you retrieve important meta data for articles, videos, and links.

> Example Request

```shell
curl -X GET https://api.roval.io/extract?url=https://www.youtube.com/watch?v=tXqnRMU1fTs \
  -H "Bearer: sk_yourapikey" \
```

> Example Response

```json
{
  "type": "video.other",
  "url": "https://www.youtube.com/watch?v=tXqnRMU1fTs",
  "root_url": "https://www.youtube.com",
  "title": "Legal Immigration: Last Week Tonight with John Oliver (HBO)",
  "description": "John Oliver explains how our legal immigration system works, and how it often doesn’t. Connect with Last Week Tonight online... Subscribe to the Last Week To...",
  "logo": "https://logo.clearbit.com/www.youtube.com",
  "site_name": "YouTube",
  "images": [
    {
    "url": "https://i.ytimg.com/vi/tXqnRMU1fTs/maxresdefault.jpg",
    "width": 1280,
    "height": 720
    }
  ]
}
```

### HTTP Request

`GET https://api.roval.io/extract?url={url}`

**ARGUMENTS**

Parameter | Type | Required
--------- | ---- | -----------
`url` | string | required

# Logo

The avatar API lets you retrieve a person's avatar using person's email address or name. The avatar API checks if an associated profile with Gravatar.

> Example Request

```shell
curl -X GET https://api.roval.io/logo?domain=example.com&name=Company \
  -H "Bearer: sk_yourapikey" \
```

> You can call the API directly from an html img tag - images are returned inline. For example:

```html
<img src="https://api.roval.io/logo?domain=segment.com&name=Segment">
```

> Return's company logo image

> <img src="https://logo.clearbit.com/segment.com" alt="Segment" width="100" height="100">

> When no logo image is found, it generates the logo image using company's initials.

> <img src="https://api.nexus150.com/logo?domain=striping.com&name=Segment" alt="Segment" width="100" height="100">


### HTTP Request

`GET https://api.roval.io/logo?domain={domain}&name={company_name}`

**ARGUMENTS**

Parameter | Type | Required
--------- | ---- | -----------
`domain` | string | required
`name`   | string | optional
