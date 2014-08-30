---
layout: post
title:  "Unit Testing Best Practices In Django"
date:   2014-07-08
---


[Math and Pencil](http://www.mathandpencil.com) writes a lot of unit tests in Django. Over the years, we have compiled a number of helpers we use across multiple projects. Here is a list of "best practices" we have compiled.

#### Directory Structure

Split your unit tests into separate files. It makes maintaining them much easier, and it also allows you to run a single test or a set of tests from the command line. The following is an example directory structure from a Math & Pencil



![My helpful screenshot]({{ baseurl.url }}/images/posts/unit_test_directories.png)


#### Writing Unit Tests With Session Data

In general, avoid storing state-based data in a session. If you are going to store something in a session, and you want a unit test to make sure the view or code is working properly, you can bootstrap your session in your setUp function as follows:

{% highlight python %}
def setUp(self):
 self.c = Client()
 engine = import_module(settings.SESSION_ENGINE)
 store = engine.SessionStore()
 store.save()  
 self.c.cookies[settings.SESSION_COOKIE_NAME] = store.session_key
 session = self.c.session
 session['page_id'] = test_settings.TEST_PAGE_ID
 session.save()	
{% endhighlight %}

In the example above, I am bootstrapping my session with the variable `page_id`.


#### Using Django.Request

Sometimes private functions in Django can take requests as input parameters. If you need to unit test these
functions, you are going to need access to a Django request. You can use this class to gain access to the request object

{% highlight python %}

class RequestFactory(Client):  
	"""	 
	Class that lets you create mock Request objects for use in testing.	
	Helpful when trying add data to a sessions 

	Usage:	

	rf = RequestFactory()  
	get_request = rf.get('/hello/')	 
	post_request = rf.post('/submit/', {'foo': 'bar'})	

	This class re-uses the django.test.client.Client interface, docs here:	
	http://www.djangoproject.com/documentation/testing/#the-test-client	 

	Once you have a request object you can pass it to any view function,   
	just as if that view had been hooked up using a URLconf.  

	"""	 
	def request(self, **request):  
		"""	 
		Similar to parent class, but returns the request object as soon as it  
		has created it.	 
		"""	 
		environ = {
		'HTTP_COOKIE':       self.cookies.output(header='', sep='; '),
		'REQUEST_METHOD':    'GET',
		'SERVER_NAME':       'testserver',
		'SERVER_PORT':       '80',
		'SERVER_PROTOCOL':   'HTTP/1.1',
		'wsgi.version':      (1,0),
		'wsgi.url_scheme':   'http',
		'wsgi.input':        FakePayload(''),
		'wsgi.errors':       self.errors,
		'wsgi.multiprocess': True,
		'wsgi.multithread':  False,
		'wsgi.run_once':     False,
		}		

		environ.update(self.defaults)  
		environ.update(request)	 
		request = WSGIRequest(environ)	
		handler = BaseHandler()	 
		handler.load_middleware()  
		for middleware_method in handler._request_middleware:  
			if middleware_method(request):	
				raise Exception("Couldn't create request mock object - "  
								 "request middleware returned a response")	
		return request	

{% endhighlight %}

Your unit test would look something like this:

{% highlight python %}

def setUp():
	self.rf = RequestFactory()
...

request = self.rf.get( '/hello_world')

{% endhighlight %}

#### Use Python Mock

The [mock module](https://pypi.python.org/pypi/mock) has a number of built in methods that enhance unit testing. You can use the module to mock api calls, patch classes/functions, deleting attributes, etc. Add this to your arsenal as soon as possible.

#### Faker

If you have account data, user data, company data, address, etc ... you no longer have to hard code them thanks to [Faker](https://github.com/joke2k/faker). Faker generates a variety of different data, randomly, and is extremely useful in unit tests and for bootstrapping data.


#### Testing AJAX Calls

Testing AJAX calls isn't hard, but you should always load the response as JSON (using your favorite JSON module) and then write test to ensure the correct data is returned.

{% highlight python %}

response = self.c.get(reverse('add-question'), {}, HTTP_X_REQUESTED='XMLHttpRequest')
self.assertEquals(response.status_code, 200)	
response_json = json.loads(response.content)
logging.debug(response_json)
if not response_json.has_key('first_question_url'):
	raise RuntimeError("missing first_question_url: %s" % response_json)
self.survey_url = response_json['first_question_url']
self.instance_id = response_json['instance_id']

{% endhighlight %}


#### Testing emails

If you have a view or function that sends and email, Django has built in unit testing modules to let you test if the email was sent. It also lets you check the content (subject + body) of the email.


{% highlight python %}
self.assertEqual(response.status_code,200)
self.assertEqual(len(mail.outbox),1)
self.assertEqual(mail.outbox[0].subject, GENERIC_SOCIALQ_WELCOME_EMAIL_SUBJECT)
self.assertTrue( 'newuser@socialq.com' in mail.outbox[0].to)
{% endhighlight %}


#### Mocking the Facebook API

Do you use Facebook's Graph API? If so, we develop this class to mock the facebook API in unit tests:

{% highlight python %}

class MockGraphAPI(object):
	me_profile=FACEBOOK_ME
	me_checkins=FACEBOOK_ME_CHECKINS
	me_friends=FACEBOOK_ME_FRIENDS
	me_accounts=FACEBOOK_ME_ACCOUNTS
	account_posts=FACEBOOK_ACCOUNT_POSTS
	post_insights=FACEBOOK_POST_INSIGHTS
	other_profiles=FACEBOOK_OTHER_PROFILES

	def __init__(self, 
		access_token=None):
		self.me_id = self.me_profile['id']
		self.access_token = access_token
	
	def _is_me(self, id):
		return id=="me" or id==self.me_id

	def __unicode__(self):
		return "MOCKED:: GraphAPI %s" % self.self.access_token

	def get_object(self, id, **args):
		logging.debug("[MOCKGraphAPI get_object] id=%s args=%s" % (id, args))
		if self._is_me(id):
			return self.me_profile
		try:
			return self.other_profiles[id]
		except KeyError:
			logging.exception("Failed to find id.  Add it or maybe you wanted the failure...")
			return {
			   "error": {
			      "message": "(#803) Some of the aliases you requested do not exist: " + id,
			      "type": "OAuthException",
			      "code": 803
			   }
			}

	def get_objects(self, ids, **args):
		logging.debug("[MOCKGraphAPI get_objects] ids=%s args=%s" % (ids, args))
		if any([self._is_me(id) for id in ids]):
			d = {}
			d["me"] = self.me_profile

			d.update(FACEBOOK_FRIENDS_PROFILES)
			return d
		return FACEBOOK_FRIENDS_PROFILES

	def get_connections(self, id, connection_name, **args):
		date_format = args.get('date_format', 'c')
		ret = None
		id_str = self._is_me(id) and "me(%s)"%id or id
		logging.debug("[MOCKGraphAPI get_connections] id=%s getting '%s', args=%s" % (id_str, connection_name, args))
		if connection_name == "friends":
			if self._is_me(id):
				ret = self.me_friends
		elif connection_name == "likes":
			if self._is_me(id):
				ret = FACEBOOK_ME_LIKES
			else:
				ret = FACEBOOK_FRIENDS_LIKES[str(id)]
		elif connection_name == "checkins":
			ret = self.me_checkins
		elif connection_name == "scores":
			ret = {'data': []}
		elif connection_name == "feed":
			ret = FACEBOOK_ME_FEED
		elif connection_name == "accounts":
			ret = self.me_accounts
		elif connection_name == "posts":
			if self._is_me(id):
				raise NotImplementedError
			else:
				ret = self.account_posts
		elif connection_name == "insights":
			if self._is_me(id):
				raise NotImplementedError
			else:
				ret = self.post_insights
			logging.debug("[MOCKGraphAPI get_connections] returning %s" % (ret), colorize=False)
		
		if ret is None:
			raise NotImplementedError
		else:
			return copy.deepcopy(ret)

	def put_object(self, parent_object, connection_name, **data):
		raise NotImplementedError()

	def put_wall_post(self, message, attachment={}, profile_id="me"):
		raise NotImplementedError()

	def put_comment(self, object_id, message):
		raise NotImplementedError()

	def put_like(self, object_id):
		raise NotImplementedError()

	def delete_object(self, id):
		raise NotImplementedError()

	def request(self, path, args=None, post_args=None):
		raise NotImplementedError()
{% endhighlight %}

You can use this class in a unit tests as follows:

{% highlight python %}
self.mock_graphapi_patcher = patch('vendor.facebook.facebook.GraphAPI',
new_callable=getMockGraphAPI())
self.mock_graphapi = self.mock_graphapi_patcher.start()
{% endhighlight %}


If you are interested in Django, machine learning, math, and/or statistics you can following me on twitter: [@josephmisiti](https://www.twitter.com/josephmisiti)

If you need help with Django or Machine Learning, contact my firm at info@mathandpencil.com
