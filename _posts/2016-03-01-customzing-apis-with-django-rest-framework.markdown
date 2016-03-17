---
layout: post
title:  "Customizing REST APIs using Django Rest Framework"
date:   2016-03-01
---

If you are new to Django and need to create RESTful APIs, using Django Rest Framework (avoid Piston or Tastypie). 
It's fairly easy to get rest APIs up and running, but things can get a little confusing when you need to customize
functionality.

Two problems I seem to solve a lot are adding filters via query parameters and excluding fields via query parameters. 

Let's say you have an API call that lists all the buildings in your database:


```json
https://www.somesite.com/api/v1/buildings
```

returns

{% highlight json %}

{
    "id": 110,
    "submission": 120,
    "description": "A Building",
    "street": "701 Peach Blvd",
    "city": "West Palm Beach",
    "state": "FL",
    "zipcode": "33401",
    "county": "Orleans",
    "latitude": 24.706871,
    "longitude": -81.0611,
},
{
    "id": 111,
    "submission": 120,
    "description": "A Building",
    "street": "701 Peach Blvd",
    "city": "West Palm Beach",
    "state": "FL",
    "zipcode": "33401",
    "county": "Orleans",
    "latitude": 24.706871,
    "longitude": -81.0611,
},

{
    "id": 112,
    "submission": 122,
    "description": "A Building",
    "street": "701 Peach Blvd",
    "city": "West Palm Beach",
    "state": "FL",
    "zipcode": "33401",
    "county": "Orleans",
    "latitude": 24.706871,
    "longitude": -81.0611,
},
                
{% endhighlight %}

Now lets say I want to be able to filter out only data points which have `submission=120`. Unfortunately DRF 
does not offer this functionality out of the box. To do this, you need to add some code to `APIView`:


{% highlight python %}

class BuildingView(viewsets.ModelViewSet):
    permission_classes = (IsAuthenticated, )
    queryset = models.Building.objects.all()
    serializer_class = serializers.BuildingSerializer

    def get_queryset(self):
        """ allow rest api to filter by submissions """
        queryset = models.Building.objects.all()
        submission = self.request.query_params.get('submission', None)
        if submission is not None:
            queryset = queryset.filter(submission=submission)
        
        return queryset
        
{% endhighlight %}

As you can see, I am overriding `get_queryset` and checking to see if the `submission` get query parameter
is being passed. If it is, then I return a filter queryset rather than the queryset containing all objects
in the database.

Sometimes it is also useful to be able to filter the json responses. Say I only way to see the `id`, `latitude`, and 
`longitude` from the above json response. You can easily provide this functionality by updating your `Serializer`


{% highlight python %}

class BuildingSerializer(serializers.ModelSerializer):
    """ `Building` model serializer """
    
    def __init__(self, *args, **kwargs):
        
        super(BuildingSerializer, self).__init__(*args, **kwargs)
        request = self.context.get("request")
        if request and request.query_params.get('fields'):
            fields = request.query_params.get('fields')
            if fields:
                fields = fields.split(',')
                allowed = set(fields)
                existing = set(self.fields.keys())
                for field_name in existing - allowed:
                    self.fields.pop(field_name)
{% endhighlight %}

As you can see, here I am overriding the `BuildingSerializer` constructor. Again, I am checking to see if `fields` query
parameter is being passed, and if it is, I filter the response by the fields that are provided (comma separated list). This
code will enable API calls as follows:

```json
https://www.somesite.com/api/v1/buildings?fields=id,latitude,longitude
```

returns

{% highlight json %}

{
    "id": 110,
    "latitude": 24.706871,
    "longitude": -81.0611,
},
{
    "id": 111,
    "latitude": 24.706871,
    "longitude": -81.0611,
},

{
    "id": 112,
    "latitude": 24.706871,
    "longitude": -81.0611,
},
                
{% endhighlight %}

You can also combine both methods:


```json
https://www.somesite.com/api/v1/buildings?fields=id,latitude,longitude&submission=120
```

returns

{% highlight json %}

{
    "id": 110,
    "submission": 120,
    "latitude": 24.706871,
    "longitude": -81.0611,
},
{
    "id": 111,
    "submission": 120,
    "latitude": 24.706871,
    "longitude": -81.0611,
},
                
{% endhighlight %}

DRF is really an awesome piece of software. You just need to get used to some of these edge cases.

