---
layout: post
title:  "Creating Image Thumbnails and Crops In Python Using S3 and PIL"
date:   2014-10-04
categories: image-processing
---

If you ever plan on designing a web application that requires image uploading, you will probably need to create image thumbnails. Amazon S3 is a great place to store images because it is very cheap, reliable, and has a robust API (in python accessible via boto). Although creating thumbnails is pretty easy, I believe having both scenarios described below in a single place is useful, as I have had to look at multiple stackoverflow posts to find these solutions.


The two different scenarios we are going to go over here are the following

1. The images are already on the internet, so you need to download them, resize them, and upload them to Amazon S3
1. The images are on a local hard drive, so we need to handle image upload to the webserver, processing, and then uploading to Amazon S3.


### The Images  Already Reside On The Internet

If you're images already reside on the internet, you do not need to handle the image upload mechanism. Your software needs to be able to download the image, perform and processing on the image, and then upload the results to Amazon S3.

Downloading an image from the internet is pretty easy using Python's excellent requests library:

```python
response = requests.get(self.high_res)
return Image.open(StringIO.StringIO(response.content))
```

It is important you store the image in a `StringIO` object, because then you can read it into PIL without saving it to disk. Once you have the image data you can create a thumbnail using the following method:

{% highlight python %}

def reduce_image_resolution(img, basewidth = 400):
	""" reduce image resolution to <constant> precent of what is uploaded """
	wpercent = (basewidth/float(img.size[0]))
	hsize = int((float(img.size[1])*float(wpercent)))
	img = img.resize((basewidth,hsize), PIL.Image.ANTIALIAS)
	
	file_name, extension = os.path.splitext(image_path)[0],os.path.splitext(image_path)[1]
	new_file_name = "%s_REDUCED_%s" %(file_name, extension)
	if extension is '.JPG' or extension is '.JPEG':
		img.save(new_file_name, "JPEG")
	else:
		img.save(new_file_name, "PNG")
		
	logging.debug("~FR image reduced %s " % new_file_name)
	return (new_file_name, basewidth, hsize)
{% endhighlight %}

The method above forced the width of the image to be `basewidth`, which for my application is 400 pixels. If you have x,y,w,h, and you want to crop the image instead, you can use a method like the following

{% highlight python %}
def crop_image(self, x, y, w, h):
	"""
		Upload image to S3 via a string buffer
		http://stackoverflow.com/questions/6685500/upload-resized-image-to-s3
	"""
	left = x
	top = y
	right = x + w
	bottom = y+ h
	im  = self._read_from_web()
	output = StringIO.StringIO()
	im.crop((left, top, right, bottom)).save(output, 'PNG')
	output_file_name = "%s_THUMB.PNG" % uuid.uuid4()
	return S3.upload_to_s3_from_buffer(file_name=output_file_name, buffer=output)
{% endhighlight %}

You need a special function for uploading an image to S3 using boto via a `StringIO`. That method looks like the following:

{% highlight python %}
def upload_to_s3_from_buffer(file_name, buffer):
	"""
		Upload image to S3 via a string buffer (instead of reading off your local hd)
	"""
	connection 	= boto.connect_s3(settings.AWS_ACCESS_KEY, settings.AWS_SECRET_KEY)	
	bucket = connection.lookup( settings.AWS_BUCKET_NAME )
	key = boto.s3.key.Key(bucket)
	key.name = file_name
	key.set_contents_from_string(buffer.getvalue(), headers={"Content-Type": "image/png"})
	key.make_public()
	url = key.generate_url(expires_in=0, query_auth=False)
	logging.debug("~FM [URL %s]" % url )
	return url

{% endhighlight %}


### The Images Resize On The Customer's Local Hard drive

If the image does not resize on the internet, you're going to have to handle the upload in your web application also. There are a variety of different jquery uploaders that can be used to upload files to a web server. My favorite at the moment is dropzone.js, but really anything will work. Using django, you can handle an AJAX request for a file upload as follows:

{% highlight python %}
results = {}
is_raw = False
if len(request.FILES) == 1:
	upload = request.FILES.values()[0]
	filename = upload.name
else:
	raise Exception("Bad Upload")

(success, url, path_to_file) = save_upload( upload, filename, is_raw, 'misc' )
fileName, fileExtension = os.path.splitext(path_to_file)
{% endhighlight %}

And the `save_upload` method used above performs the following:

{% highlight python %}
def save_upload( uploaded, filename, raw_data, folder ):

	uid			= str(uuid.uuid4())
	extension	= os.path.splitext(filename)[1]	 
	cloud_name	= uid + extension
		
	try:
		os.mkdir(os.path.join(settings.BASE_DIR, folder))
	except:
		pass
	
	local_file_path		= os.path.join( settings.BASE_DIR, folder, cloud_name )
	non_absolute_path	= os.path.join( '/', folder, cloud_name  )
			
	with BufferedWriter( FileIO( local_file_path, "w" ) ) as dest:
		if raw_data:
			foo = uploaded.read( 1024 )
			while foo:
				dest.write( foo )
				foo = uploaded.read( 1024 ) 
		else:
			for c in uploaded.chunks():
				dest.write( c )
		return (True, None, local_file_path)

	return (False, None, None)
{% endhighlight %}
