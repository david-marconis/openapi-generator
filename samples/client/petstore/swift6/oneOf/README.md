# Swift6 API client for PetstoreClient

No description provided (generated by Openapi Generator https://github.com/openapitools/openapi-generator)

## Overview
This API client was generated by the [OpenAPI Generator](https://openapi-generator.tech) project.  By using the [openapi-spec](https://github.com/OAI/OpenAPI-Specification) from a remote server, you can easily generate an API client.

- API version: 0.0.1
- Package version: 
- Generator version: 7.10.0-SNAPSHOT
- Build package: org.openapitools.codegen.languages.Swift6ClientCodegen

## Installation

### Carthage

Run `carthage update`

### CocoaPods

Run `pod install`

## Documentation for API Endpoints

All URIs are relative to *http://localhost*

Class | Method | HTTP request | Description
------------ | ------------- | ------------- | -------------
*DefaultAPI* | [**rootGet**](docs/DefaultAPI.md#rootget) | **GET** / | 


## Documentation For Models

 - [Apple](docs/Apple.md)
 - [Banana](docs/Banana.md)
 - [Fruit](docs/Fruit.md)


<a id="documentation-for-authorization"></a>
## Documentation For Authorization

Endpoints do not require authorization.


### How do I implement bearer token authentication with URLSession on the Swift 6 API client?

First you implement the `OpenAPIInterceptor` protocol.
```
public class BearerOpenAPIInterceptor: OpenAPIInterceptor {
    public init() {}
    
    public func intercept(urlRequest: URLRequest, urlSession: URLSessionProtocol, openAPIClient: OpenAPIClient, completion: @escaping (Result<URLRequest, any Error>) -> Void) {
        refreshTokenIfDoesntExist { token in
            
            // Change the current url request
            var newUrlRequest = urlRequest
            newUrlRequest.setValue("Bearer \(token)", forHTTPHeaderField: "Authorization")
            
            // Change the global headers
            openAPIClient.customHeaders["Authorization"] = "Bearer \(token)"
            
            completion(.success(newUrlRequest))
        }
    }
    
    public func retry(urlRequest: URLRequest, urlSession: URLSessionProtocol, openAPIClient: OpenAPIClient, data: Data?, response: URLResponse, error: Error, completion: @escaping (OpenAPIInterceptorRetry) -> Void) {
        // We will analyse the response to see if it's a 401, and if it's a 401, we will refresh the token and retry the request
        refreshTokenIfUnauthorizedRequestResponse(
            data: data,
            response: response,
            error: error
        ) { (wasTokenRefreshed, newToken) in
            
            if wasTokenRefreshed, let newToken = newToken {
                
                // Change the global headers
                openAPIClient.customHeaders["Authorization"] = "Bearer \(newToken)"
                
                completion(.retry)
            } else {
                // If the token was not refreshed, it's because it was not a 401 error, so we send the response to the completion block
                completion(.dontRetry)
            }
        }
    }
    
    private var bearerToken: String? = nil
    
    func refreshTokenIfDoesntExist(completionHandler: @escaping (String) -> Void) {
        if let bearerToken = bearerToken {
            completionHandler(bearerToken)
        } else {
            startRefreshingToken { token in
                completionHandler(token)
            }
        }
    }
    
    func refreshTokenIfUnauthorizedRequestResponse(data: Data?, response: URLResponse, error: Error, completionHandler: @escaping (Bool, String?) -> Void) {
        if let response = response as? HTTPURLResponse, response.statusCode == 401 {
            startRefreshingToken { token in
                completionHandler(true, token)
            }
        } else {
            completionHandler(false, nil)
        }
    }
    
    private func startRefreshingToken(completionHandler: @escaping (String) -> Void) {
        // Get a bearer token
        let dummyBearerToken = "..."
        
        bearerToken = dummyBearerToken

        completionHandler(dummyBearerToken)
    }
}
```

Then you assign the `BearerOpenAPIInterceptor` to the property `OpenAPIClient.shared.interceptor`.

`OpenAPIClient.shared.interceptor = BearerOpenAPIInterceptor()`

Here is a working sample that put's together all of this.
[AppDelegate.swift](https://github.com/OpenAPITools/openapi-generator/blob/master/samples/client/petstore/swift6/urlsessionLibrary/SwaggerClientTests/SwaggerClient/AppDelegate.swift)
[BearerTokenHandler.swift](https://github.com/OpenAPITools/openapi-generator/blob/master/samples/client/petstore/swift5/urlsessionLibrary/SwaggerClientTests/SwaggerClient/BearerDecodableRequestBuilder.swift)

## Author



