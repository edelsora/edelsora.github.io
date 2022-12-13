---
title: Designing REST API with OpenAPI 3
description: How I easily designing, testing and documenting REST API for good
---

REST API is a way to transport data from a web server to serve web client needs, such as requesting new authentication tokens, asking for resources data and much more. The growing number of endpoints could overwhelm us to remember their purpose, our app maybe could grow to numbers up to a hundred or more.

Documenting an API is a good way to give information about our REST API server endpoints for use by third parties, help new coworkers get into the code base and of course for our future self. So this could help us not get overwhelmed by endpoints' purpose without needing to read the code of the API all time.
Designing API

How about designing an API? What its look like?

Okay, let back to the basic thing. API work by receiving requests and producing a response after being handled by our API implementation function.

So, in designing API we define how our API will respond if given a request, and define how the request and response shape:
```yaml
/users/login:
    post:
        description: Exchange user credential with credential token
        requestBody:
            content:
                  application/json:
                      type: object
                      properties:
                          email:
                              type: string
                          password:
                              type: string
        response:
            '200':
                description: Ok
                content:
                    application/json:
                          type: object
                          properties:
                              accessToken:
                                  type: string
                              refreshToken:
                                  type: string
            '401':
                description: Unauthorize
```

The code above states,

our API endpoint /users/login ,

- receive HTTP POST requests with content type application/json , with the body containing the object in JSON format and the object field consisting of:

    - email field with string type

    - password field with string type

- will have a possible response:

    - HTTP 200, with content type application/json , with object and field consisting of:

        - accessToken field with string type

        - refreshToken field with string type

    - HTTP 401 without body content.

For me, engaging myself to learn about OpenAPI is difficult at first like understanding the field for describing dynamic routes or shaping JSON in the request body.

But after a couple of hours, I get used and thought it wise to design an API first instead of jumping to the text editor and writing the spec there. Here is the resources link, I used when learning OpenAPI:

- https://swagger.io/docs/specification/basic-structure/

- https://swagger.io/docs/specification/api-host-and-base-path/

- https://www.youtube.com/watch?v=87ZFvJ7_-n0

To work better while designing API, I recommended a tool that is designed to handle this kind of task. I use Insomnia daily for designing API, here is what it looks like:
Appreance while I designing API

If you already finish your design and are done with writing the implementation code, you can test it with Insomnia too. If you see on the top right corner inside our API spec card, there is a "Try it out" button to test your API directly from there.

Now you already had design, document and testing scenarios in one go. How convenient it is, isn't it?

Exciting? Here basic template of OpenAPI spec for helping your start design now,
```YAML
openapi: 3.0.0
info:
  title: Change the title as you like
  description: Same as above but for definition
  version: 0.0.1
  
 # this will used when you testing
 # your api
servers:
  - url: http://localhost:8080
    description: "local development url"

 # this where most design occurred
paths:
    /users/login:
        post:
            description: Exchange user credential with credential token
            requestBody:
                content:
                      application/json:
                          type: object
                          properties:
                              email:
                                  type: string
                              password:
                                  type: string
            response:
                '200':
                    description: Ok
                    content:
                        application/json:
                              type: object
                              properties:
                                  accessToken:
                                      type: string
                                  refreshToken:
                                      type: string
                '401':
                    description: Unauthorize
        get:
              summary: "Get current user profiles"
              responses:
                  '200': 
                      description: A JSON array of user names
                      content:
                          application/json:
                              schema: 
                                  $ref: "#/components/schemas/UserProfile"

# This for request or response shape
# template, so you just use ref rather
# rewriting same definition twice.
components:
    schemas:
        UserProfile:
          type: object
          properties:
            id:
              type: integer
              example: 1
            nama:
              type: string
              example: yourname
            photo_url:
              type: string
              example: https://github.com/yourgitusername
```
Thank you for reading until the end.