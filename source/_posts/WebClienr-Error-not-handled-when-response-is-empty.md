---
title: 'WebClienr: Error not handled when response is empty'
date: 2025-08-20 17:21:42
tags: spring, java
---

Got an issue when using Spring WebClient to consume API.

Code is

```java
        WebClient.RequestBodySpec requestBodySpec = webClientBuilder.baseUrl(baseUrl).build().post().uri(uri);
        return requestBodySpec.exchangeToMono(clientResponse -> {
            if (clientResponse.statusCode().is2xxSuccessful()) {
                log.info("Successful {} for {}", clientResponse.statusCode().value(), clientResponse.request().getURI());
                return clientResponse.bodyToMono(String.class);
            } else {
                log.warn("Non-successful code {} for {}", clientResponse.statusCode().value(), clientResponse.request().getURI());
                return clientResponse.bodyToMono(String.class)
                        .flatMap(error -> Mono.error(new HttpException(HttpStatus.valueOf(clientResponse.statusCode().value()),
                                        "Upstream Error",
                                        clientResponse.statusCode().toString(),
                                        error)
                ));
            }
        });
```

After testing, the behavior is:

- For 2XX response, no prolem and can get the response.
- Some 4XX/5XX response, some requeses can get the HttpException with details
- Some 4XX/5XX response, some requests can NOT get the HttpException.

After some checkings, it shows that

- If the remote server responses a response with body (Like an error message), no probme;
- If the remote server response an error status but no body, this issue happens.



After some tries, the solution is that to provide a default response when the real response is empty (To let the reactive flow to move on):

```java
return clientResponse.bodyToMono(String.class)
                     .defaultIfEmpty("<Empty>")
                     .flatMap(error -> Mono.error(new HttpException(HttpStatus.valueOf(clientResponse.statusCode().value()),
                                        "Upstream Error",
                                        clientResponse.statusCode().toString(),
                                        error)
                ));
```

