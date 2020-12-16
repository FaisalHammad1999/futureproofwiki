The internet relies heavily on protocols - sets of rules which aid in the implementation of consistent patterns. HTTP and HTTPS are two protocols which websites on the world wide web must adhere to for transmission of information. When designing APIs there are a couple of implementation options. Having said that, they are rather different and despite it being a choice, you might say that it's comparing apples to oranges. Check out this [infographic from SoapUI](https://www.soapui.org/resources/infographic/api-testing/soap-vs-rest-infographic/) to see more about them side by side.

### SOAP
*Examples courtesy of [W3Schools](https://www.w3schools.com/xml/xml_soap.asp)*

**S**imple **O**bject **A**ccess **P**rotocol is a purely XML-based protocol for sending data.
[XML](https://www.w3.org/standards/xml/core) (Extensible Markup Language) is a markup language that can be used for various purposes. It is Microsoft's creation from 1998.

A SOAP message is an XML document that contains:
- An Envelope element that identifies the XML document as a SOAP message
- A Header element that contains header information
- A Body element that contains call and response information
- A Fault element containing errors and status information

A SOAP request for data on the IBM stock price from 'example.org'
```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:m="http://www.example.org">
  <soap:Header>
  </soap:Header>
  <soap:Body>
    <m:GetStockPrice>
      <m:StockName>IBM</m:StockName>
    </m:GetStockPrice>
  </soap:Body>
</soap:Envelope>
```
SOAP is very defined and in some ways restrictive. Many people dislike SOAP and it is less commonly used in more recent years.


### REST
**Re**presentational **S**tate **T**ranfer was first introduced in a dissertation by Roy Thomas Fielding in 2000. REST is an architectural style that is built on top of HTTP, rather than a protocol in itself. REST API’s are implementations of that style that can be used to transport data back and forth.

REST is stateless, meaning that the server doesn’t know anything about the client’s current state and vice versa. The only way data can be sent between the two is via an API.

REST requests contain:
- An HTTP verb to define what operation is to be performed
    - Our HTTP verbs are GET, POST, PUT, PATCH, DELETE

- A header which contains information about the request
    - Whereas SOAP works exclusively with XML, REST is more flexible in terms of what data types it can transport. Inside of the header each request contains an accept field which details what type of data the request expects back. This could be images, JSON, text, html, etc…

- A path
    - The path is the place where we are accepting data from. It normally contains parameters which detail which type of request we expect back these could be query parameters ( after a ?) or in the path itself.
  
- A body which contains data
    - When we send requests we will always include the data being sent back which is usually called a payload. We also detail the content type of the data we are sending back, which should match with an accept type of the request. We’ll also send a status code. 

***

The overarching difference between these two approaches is that REST is freeform whereas SOAP has an official standard that you can be in breach of. Another key difference is that SOAP can only send responses & requests in XML form, whereas REST allows us to send in multiple forms. Considering how much information is shared in JSON format now-a-days this can be a huge plus for REST. As an additional bonus, REST requests are generally smaller than SOAP, meaning they use less bandwidth.

***

## API Design
A huge proportion of products we use today will have some kind of touch point with an API. Either we will be calling an API to use another service, or we will be creating our own API to expose our service to other users. Jeff Bezos famously champions API services and his [2002 mandate](https://homepages.dcc.ufmg.br/~mtov/pmcc/modularization.pdf) is now taken as a popular blueprint for other companies. API’s are important and we’ll be designing them a lot so we are going to spend some more time now really honing in on good design.


### Paths
The first rule when designing an API is to keep it simple. The base of our url should always be a plural of the name of the resource we are returning or interacting with. When we make a GET request to that base url it should return all of that type, like an index. For example, if it is an API that returns holiday destinations, our base url should be `/destinations`

In order to get just 1 cat, we would expect to extend that path by adding a unique identifier eg `/destinations/1` or `/destinations/las-vegas`

We may want to dive even deeper into our base resource and API’s may be designed to restrict the information that is returned by adding in another noun as the next path e.g `/destinations/12/attractions`

There are 7 'official' RESTful routes - if you use all of them, you'll have made a full CRUD (**C**reate **Read** **U**pdate **D**elete) API:
| **Path** | **HTTP Verb** |  **Action**|
|------------|-------------|------------|
| /cats/         | GET       | index  
| /cats/new      | GET       | new   
| /cats          | POST      | create   
| /cats/:id      | GET       | show       
| /cats/:id/edit | GET       | edit       
| /cats/:id      | PATCH/PUT | update    
| /cats/:id      | DELETE    | destroy  


### Query Params
Users of our API might want to restrict what they GET back from the API more that just via an ID so we can offer support for query parameters. A common pitfall here is neglecting to handle a case where a request has more or less query params than anticipated.

A query parameter string starts with a `?` and is followed with `<key>=<value>` pairs. To chain multiple pairs, use the `&`:
- `/cats?age=4`
- `/cats?age=4&temperement=cuddly`
- `/cats?age=4&temperement=cuddly&limit=5` \
The final example here says 'I want to see a maximum of 5 cats who like cuddles'.

When sending data in a non-GET request, we would usually send this in a request body rather than query parameters.

### Status Codes
It's good to communicate the status of any HTTP request and there is an [extensive list of standard status codes](https://www.restapitutorial.com/httpstatuscodes.html) which are grouped into 5 categories:
- 1** Informational
- 2** Success
- 3** Redirect
- 4** Client Error
- 5** Server Error

### Pagination
Sometimes even well designed API’s can still allow users to request huge amounts of data, so much so that it can bring down servers. We use pagination to split the volume of data down into ‘pages’ to reduce the size of each response. This might mean that our API is setup by default to be limited to returning 50 entries, but it is also common to have parameters which allow consumers to change this limit such as `limit` and `page`.

### Documentation
The final piece of the puzzle is getting people to use our API. No developer will use an API if they aren’t properly told how. We make it easy for people to use our API’s through documentation. The best way to approach documentation is to take note of any great (and terrible!) documentation you have found yourself and consider what attributes contribute to that. Sometimes the hardest thing to find in an API documentation is the base url which is very frustrating! Great documentation might give code examples as well as explicit descriptions of accepted parameters and/or body values.
