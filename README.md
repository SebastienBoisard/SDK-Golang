# Recast.AI - SDK-Golang

[logo]: https://github.com/RecastAI/SDK-NodeJs/blob/master/misc/logo-inline.png "Recast.AI"

![Recast.AI Logo][logo]

Recast.AI official SDK in Golang.
Package documentation: https://godoc.org/github.com/RecastAI/SDK-Golang/recast

## Synospis

This module is a Golang interface to the [Recast.AI](https://recast.ai) API. It allows you to make request to your bots

## Installation


```bash
go get -t github.com/RecastAI/SDK-golang/recast
```

## Usage

### Module

```go
import "github.com/RecastAI/SDK-Golang/recast"

func main() {
	client := recast.NewClient(YOUR_TOKEN, "en")

    response, err := client.TextRequest(YOUR_TEXT, nil)
    if err != nil {
            // Handle error
    }

    intent, err := response.Intent()
    if err != nil {
            // No intent found
    } else if intent == YOUR_INTENT {
        // Do your code...
    }
}
```

## Specs

### Structs

This module contains 4 main structures, as follows:

* Client is the client allowing you to make requests.
* Response contains the response from [Recast.AI](https://recast.ai).
* Sentence represents a sentence of the response.
* Entity represents an entity found by Recast.AI in your user's input.

Don't hesitate to dive into the code, it's commented ;)

### Client

The Recast.AI Client can be instanciated with a token and provides the two following methods:

* textRequest(text string, opts map[string]string) *Performs a text request*
* fileRequest(file string, opts map[string]string) *Performs a voice file request*

*Accepted options are "token" to override the token of the client, and language, to specify the language of the input*

```go
import "github.com/RecastAI/SDK-Golang/recast"

func main() {
    var client *recast.Client
    var response *recast.Response

    client = &recast.Client{}

    response, err := client.FileRequest(YOUR_VOICE_FILE, map[string]string {
		"token": YOUR_TOKEN,
		"language": "en",
	})
    if err != nil {
		// Handle error
    }
}
```

### Response

The Recast.AI Response is generated after a call with the two previous methods and contains the following methods:
* Languages() *Returns the language of the input*
* Sentence()  *Returns the first detected sentence*
* Sentences() *Returns all sentences detected in the input*
* Intent()    *Returns the first matched intent*
* Intents() *Returns all intents detected, ordered by propability*
* Status() *Returns the status of the response*
* Timestamp() *Returns the timestamp of the request *
* Version() *Returns the version of the json*
* Entity(name string) *Returns the first entity matching -name-*
* Entities(name string) *Returns all entities matching -names-*
* AllEntities(names ...string) *Returns all entities matching -names-*

```go
response, err := client.TextRequest("Give me a recipe with asparagus. And a recipe with tomatoes.")
if err != nil {
// Handle error
}

intent, err := response.Intent()
if err != nil {
// No intent matches input
}

if intent == "recipe" {
	var entitiesMap map[string][]*recast.Entity
    var ingredients []*recast.Entity

    // get all 'ingredients' entities
    entitiesMap = response.AllEntities()
    ingredients = response.Entities("ingredients")
}
fmt.Printf("This request has been filled at %s\n", response.Timestamp())
```

### Sentence

The Recast.AI Sentence is generated by the Recast.AI Response constructor and provides the following methods:

* Source() *The source of the sentence*
* Type() *The type of the sentence*
* Action() *The action of the sentence*
* Agent() *The agent of the sentence*
* Polarity() *The polarity (negation or not) of the sentence*
* Entities() *All the entities detected in the sentence*
* Entities(name string) *Returns a slice of entities of type -name-*
* AllEntities(names ...string) *Returns a all entities matching one of -names-*

```go
resp, err := client.TextRequest("Tell me a joke.")
if err != nil {
	// Handle error
}
sentence := resp.Sentence()
if sentence.Action() == "tell" && sentence.Polarity() == "positive" {
	// Tell a joke...
}
```

### Entity

The Recast.AI Entity is generated by the Recast.AI Sentence and provides the following methods:

* Name() *The name of the entity*
* Raw() *The raw text on which the entity was detected*

In addition to Name and Raw, more attributes can be accessed by the Field method which can be one of the following:

* hex
* value
* deg
* formated
* lng
* lat
* unit
* code
* person
* number
* gender
* next
* grain
* order

```go
response, _ := client.TextRequest("What's the weather in San Francisco?")
var location *recast.Entity
location = response.Entity("location")
if response.Intent() == "weather" && location != nil {
	fmt.Printf("You asked me for the weather in %s\n", location.Field("formated").(string))
}
```
***Recast.AI entity fields type are dependant on the entity itself so Field returns an interface{}, you must do a type assertion as follows:***
* Numbers: float64
* Strings: string

Refer to [Recast.Ai Entities Manual](https://man.recast.ai/#list-of-entities) for details about entities



## More

You can view the whole API reference at [man.recast.ai](https://man.recast.ai).


## Author

François Triquet, francois.triquet@recast.ai

You can follow us on Twitter at [@recastai](https://twitter.com/recastai) for updates and releases.

## License

Copyright (c) [2016] [Recast.AI](https://recast.ai)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
