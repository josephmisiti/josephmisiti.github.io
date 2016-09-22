---
layout: post
title:  'Validation using Django Rest Framework and Django Forms'
date:   2016-09-22
categories: drf, validation,
---

As a web developer, you are going to spend a decent amount of your time building and update API endpoints. 
If you are using Django, I recommend using Django's excellent Django Rest Framework.

There are three places you can add valiation

#### Adding Validation at DRF/Django View-Level

An easy place to add valiation is directly in the view.

{% highlight python %}

from rest_framework.exceptions import APIException

class InvalidAPIQuery(APIException):
    status_code = 400
    default_detail = 'An invalid query parameter was provided'

{% endhighlight %}

{% highlight python %}

class DocumentUploadView(UserAuditLogMixin, APIView):
    permission_classes = (IsAuthenticated, )
    parser_classes = (MultiPartParser, FormParser,)

    def post(self, request, format=None):
        if 'submission_id' not in request.POST:
            raise InvalidAPIQuery("`submission_id` is required")

        document = models.Document.create_document_from_request(request)

        return Response({
            'document': serializers.DocumentSerializer(document['document']).data
        }, status=201)



{% endhighlight %}


#### Adding at DRF Serializer-Level

DRF serializers provide their own validation mechanism.


{% highlight python %}

from rest_framework import serializers

class BuildingSerializer(serializers.ModelSerializer):

    def validate(self, data):
        errors = {}
        year_built = data.get('year_built')
        year_rennovated = data.get('year_rennovated')
        
        if year_rennovated < year_built:
            errors['error'] = u'You\'re an idiot!'
            raise serializers.ValidationError(errors)
            
        return data


{% endhighlight %}

#### Adding at Django Form-Level

Django forms, and specifically Django model forms are an decent place to add valiation.

{% highlight python %}

from django import forms

class BuildingForm(forms.Form):

    year_built = forms.IntegerField(required=True)
    year_rennovated = forms.IntegerField(required=True)
                            
    def clean(self):
        cleaned = super(BuildingForm, self).clean()
        year_built =  cleaned.get('year_built')
        year_rennovated =  cleaned.get('year_rennovated')

        if year_rennovated < year_built:
            raise forms.ValidationError(u'You\'re an idiot!')
     
{% endhighlight %}

And then in your view:

{% highlight python %}

class BuildingView(viewsets.ModelViewSet):
    authentication_classes = (CustomSessionAuthentication,)
    queryset = models.Building.objects.all()
    serializer_class = serializers.BuildingSerializer

    def create(self, request):
        model_form = forms.BuildingForm(request.data)
        if model_form.is_valid():
            data = serializers.BuildingSerializer(obj).data
            return Response(data, status=201)
        else:
            return Response({'errors': model_form.errors}, status=400)


{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

