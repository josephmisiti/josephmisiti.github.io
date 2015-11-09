---
layout: post
title:  "Parsing JSON Responses In Golang"
date:   2015-11-05
categories: golang,ios
---

I have recently started integrating [golang]() into more of my projects. It is an amazing little lanaguage with cool syntax, a huge community, and it's a nice break from interpreted languages like Python and Ruby. In this tutorial, I am going to show you how to make an HTTP GET call to a public API, parse the ruturned JSON data, and store it all in golang structures.

Lets uses the [citibike API](https://www.citibikenyc.com/stations/json) as a simple example. As you can see, this API call does not require any authenication so no need to sign up for anything. The output should look something like the following:

{% highlight json %}

{
executionTime: "2015-11-09 12:47:01 PM",
stationBeanList: [
{
id: 72,
stationName: "W 52 St & 11 Ave",
availableDocks: 37,
totalDocks: 39,
latitude: 40.76727216,
longitude: -73.99392888,
statusValue: "In Service",
statusKey: 1,
availableBikes: 1,
stAddress1: "W 52 St & 11 Ave",
stAddress2: "",
city: "",
postalCode: "",
location: "",
altitude: "",
testStation: false,
lastCommunicationTime: "2015-11-09 12:46:53 PM",
landMark: ""
},

.....

{% endhighlight %}

The first step is to use golang's http module to get the response:

{% highlight go %}

res, err := http.Get("https://www.citibikenyc.com/stations/json")
if err != nil {
    panic(err.Error())
}

{% endhighlight %}

Assuming you didnt see a panic call, the response to this http call is being stored in the `res` variable. Next, we need to read the http body into a byte array for parsing/processing (using golangs [ioutil]() library):

{% highlight go %}

body, err := ioutil.ReadAll(res.Body)
if err != nil {
    panic(err.Error())
}

{% endhighlight %}


Now that you have the http body, we are going to extract the JSON parameters into go structs so they can be easily accessed in other go programs. There are many different ways to do this, but I am going to use two separate golang
structures `Station` and  `StationAPIResponse`

{% highlight go %}

type Station struct {
    Id int64 `json:"id"`
    StationName string `json:"stationName"`
    AvailableDocks int64 `json:"availableDocks"`
    TotalDocks int64 `json:"totalDocks"`
    Latitude float64 `json:"latitude"`
    Longitude float64 `json:"longitude"`
    StatusValue string `json:"statusValue"`
    StatusKey int64 `json:"statusKey"`
    AvailableBikes int64 `json:"availableBikes"`
    StAddress1 string `json:"stAddress1"`
    StAddress2 string `json:"stAddress2"`
    City string `json:"city"`
    PostalCode string `json:"postalCode"`
    Location string `json:"location"`
    Altitude string `json:"altitude"`
    TestStation bool `json:"testStation"`
    LastCommunicationTime string `json:"lastCommunicationTime"`
    LandMark string `json:"landMark"`
}

type StationAPIResponse struct {
    ExecutionTime string `json:"executionTime"`
    StationBeanList []Station `json:"stationBeanList"`
}

{% endhighlight %}

As you can see, `StationAPIResponse` contains an array of `Station` objects. All of the parameters coming from the citibike API can be accounted for here. Finally, I have a function that takes in the body, and returns a pointer to the array of StationAPIResponse`


{% highlight go %}

func getStations(body []byte) (*StationAPIResponse, error) {
    var s = new(StationAPIResponse)
    err := json.Unmarshal(body, &s)
    if(err != nil){
        fmt.Println("whoops:", err)
    }
    return s, err
}
{% endhighlight %}

Putting it all together, you can use the above code as follows:

{% highlight go %}

package main

func main() {
    
    res, err := http.Get("https://www.citibikenyc.com/stations/json")
    if err != nil {
        panic(err.Error())
    }

    body, err := ioutil.ReadAll(res.Body)
    if err != nil {
        panic(err.Error())
    }

    s, err := getStations([]byte(body))
}

{% endhighlight %}

If you want to use this code in another go program, I created a package you can pull down on [github](https://github.com/josephmisiti/go-citibike).

As always, if you are interseted in this type of content (python, go, machine learning, math), follow me on [twitter](https://twitter.com/josephmisiti). If you find any bugs, send me a pull request!
