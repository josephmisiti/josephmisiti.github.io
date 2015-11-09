---
layout: post
title:  "How to Upload a Photo To Django Using iOS and Django Rest Framework"
date:   2015-10-11
categories: django,ios
---

I recently built an iOS application for fun that I use to take pictures of pages in a book, upload them to [Django](https://www.djangoproject.com/), and then [ocr](https://en.wikipedia.org/wiki/Optical_character_recognition) them for easy retrival. I was frustrated by the lack of documentation on how to upload an image to Django using the [Django Rest Framework (DRF)](http://www.django-rest-framework.org/), so hopefully this tutorial will clear things up.


This tutorial assumes you have `django<1.8`, `djangorestframework==2.4.2`, and `django-filter` installed.

#### The Backend

First, lets create the [model](https://docs.djangoproject.com/en/1.8/topics/db/models/) that will store uploads:

{% highlight python %}

class Upload(TimeStampedModel):
    
    owner = models.ForeignKey(settings.AUTH_USER_MODEL)
    path_to_image = models.CharField(null=True,max_length=100)
    datafile = models.FileField(null=True)
    
    url = models.URLField(null=True)
    
    title = models.CharField(max_length=50)
    text = models.TextField(null=True, max_length=500, 
            help_text="The OCRed text")
            
    ocr_successful = models.BooleanField(default=False, 
        help_text="was the text successfully OCRed?")
    
{% endhighlight %}

As you can see, this model has multiple fields that are not really important for this tutorial. All you need to know here is that the `owner` comes from `request.user` (the client logged in uploading the image) and that the final image is stored as a url in the `url` field.

Now lets create the [serializer](http://www.django-rest-framework.org/api-guide/serializers/) that is going to be used by DRF to handle the upload.

{% highlight python %}
from rest_framework import serializers

class UploadSerializer(serializers.ModelSerializer):
    
    class Meta:
        model = Upload
        read_only_fields = ('created', 'text','owner', 'url', 'title')

{% endhighlight %}

This is a very standard DRF serializer. You can read more about how they work [here](http://www.django-rest-framework.org/api-guide/serializers/), but basically serializer can be used to validate data (which I am not doing in this tutorial) and also used to tell the DRF the format and composition of the JSON data returned from the API.

Next, lets create the DSR view to handle the image upload:

{% highlight python %}

import datetime

class UploadList(APIView):

    authentication_classes = (TokenAuthentication,)
    permission_classes = (IsAuthenticated,)
    parser_classes = (MultiPartParser, FormParser,)
    
    def post(self, request, format=None):
        """ handle upload, save it to local file system, start
        ocr celery tasks """
        upload = request.FILES['file']
        fh = tempfile.NamedTemporaryFile(delete=False)

        extension =  upload.name.split(".")[1]
        filename = "{}.{}".format(fh.name,extension)

        with BufferedWriter( FileIO( filename, "w" ) ) as dest:
            for c in upload.chunks():
                dest.write(c)
        
        now = datetime.datetime.now().strftime(UPLOAD_TITLE_NAME)        
        upload = Upload.objects.create(
            owner=request.user, 
            title="Uploaded @ %s" %(now),
            url="http://example.com/uploads/{name}".format(
                name=filename.split("/")[2]),
            path_to_image=filename)
        upload.start_ocr_tasks()

        return Response({}, status=201)

{% endhighlight %}

This view should be fairly self-explanatory. I'm using `TokenAuthentication` rather than `SessionAuthentication` for all of my APIs (I will convert to OAuth and some point). I'm extracting the photo data from the `upload` in `request.FILES`. After I find the data, I figured out the file extension being used , and save the file to my local hard drive by writing chucks of the image to a file handle using `BufferedWriter`. Once the data is on my hard drive, I create a new `Upload` in the database, and start an OCR task and return `HTTP 201` because I just created a new database entry.

The important part of this piece of code are the [parser_classes](http://www.django-rest-framework.org/api-guide/parsers/#multipartparser), which tell DRF that I'm uploading using `multipart` rather than it expecting a regular `HTTP POST`.

{% highlight python %}
parser_classes = (MultiPartParser, FormParser,)
{% endhighlight %}


Finally, here is a unit test I used to confirm that the view I just outlined accepts the image data, downloads it locally to a temp file, and returns an `HTTP 201`.

{% highlight python %}

    def test_upload_image(self):
        """ unit tests for testing image uploads using DRF"""
        path_to_image = os.path.join('path','to','image.jpg')
        data = {
            'file': open(path_to_image, 'rb')
        }
        response = self.client.post(reverse(self.URL), data ,format='multipart')
        self.assertEquals(response.status_code,401)
        self.client.force_authenticate(user=self.user)
        data = {
            'file': open(path_to_image, 'rb')
        }
        response = self.client.post(reverse(self.URL), data ,format='multipart')
        self.assertEquals(response.status_code,201)
  
{% endhighlight %}


#### The Client

The actual `HTTP POST` that uploads the image is utilizing the excellent [AFNetworking](https://github.com/AFNetworking/AFNetworking) library.

{% highlight objc %}

- (void)uploadImage:(NSData*)imageData
                    params:(NSDictionary*)params
                   success:(TFHTTPClientSuccess)success
                   failure:(TFHTTPClientFailure)failure;

{% endhighlight %}

{% highlight objc %}

- (void)uploadImage:(NSData*)imageData
             params:(NSDictionary*)params
            success:(TFHTTPClientSuccess)success
            failure:(TFHTTPClientFailure)failure{

    NSString* url = [NSString stringWithFormat:@"%@%@",
    BASE_URL,@"/api/upload/image"];
    [self POST:url parameters:params 
    constructingBodyWithBlock:^(id<AFMultipartFormData> formData) {
        [formData appendPartWithFileData:imageData 
        name:@"file" fileName:@"photo.jpg" 
        mimeType:@"image/jpeg"];
    } success:^(AFHTTPRequestOperation *operation, 
    id responseObject) {
        if(success){
            success((AFHTTPRequestOperation *)operation, responseObject);
        }
    } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
        if (failure) {
            failure((AFHTTPRequestOperation *)operation, error);
        }
    }];

{% endhighlight %}

The most important part of this code is the following

{% highlight objc %}

constructingBodyWithBlock:^(id<AFMultipartFormData> formData) {
    [formData appendPartWithFileData:imageData 
    name:@"file" fileName:@"photo.jpg" 
    mimeType:@"image/jpeg"];

{% endhighlight %}

Which is taking the image data from my iPhone that is stored in an `NSData` object, sticking it in the `file` parameter in the body (request), and then performing a [multipart request](http://stackoverflow.com/questions/16958448/what-is-http-multipart-request)

The code that calls this function looks something like this:

{% highlight objc %}

- (void) camera:(id)cameraViewController didFinishWithImage:(UIImage *)image
                withMetadata:(NSDictionary *)metadata
{
    NSData *imageData = UIImageJPEGRepresentation(image, 0.5);
    TFHTTPCLient* sharedClient = [TFHTTPCLient sharedClient];
    NSDictionary *params = @{};
    NSString* token = [[UserManager sharedClient] getAuthToken];
    [sharedClient.requestSerializer
     setValue:[NSString stringWithFormat:@"Token %@", token]
     forHTTPHeaderField:@"Authorization"];
    [MRProgressOverlayView showOverlayAddedTo:self.view animated:YES];
    [sharedClient uploadImage:imageData params:params
                success:^(AFHTTPRequestOperation *operation, id responseObject)
     {
         [MRProgressOverlayView dismissOverlayForView:self.view animated:YES];
     } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
         [MRProgressOverlayView dismissOverlayForView:self.view animated:YES];
    }];
    [self.presentedViewController dismissViewControllerAnimated:YES completion:nil];
}

{% endhighlight %}

I'm using [DBCamera](https://github.com/danielebogo/DBCamera) to take the photo and initiate the upload. `cameraViewController` is a method that is called on by `DBCamera` when the camera has successfully taken the photo. This method simply sets up the HTTP client, adds the current token I have stored in `UserDefaults` (hacky, I know), and sticks it in an `NSData` object. One important thing to notice here is that I downsample the image by 50% using `UIImageJPEGRepresentation` before uploading it, to insure faster uploads (and man did they become much faster using this method).

That is about it. Everything here was taken directly from my project and is working as of 10/11/2015. If you have any questions, feel free to reach out to me on [twitter](https://twitter.com/josephmisiti) and if you need iOS help, please reach me at `joseph.misiti@mathandpencil.com`
