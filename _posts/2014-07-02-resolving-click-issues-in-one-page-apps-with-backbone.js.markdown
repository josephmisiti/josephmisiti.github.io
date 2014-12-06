---
layout: post
title:  "Resolving Dead Click Issues In One Page Backbone.js Applications"
date:   2014-07-02
categories: ab
---

*** POST NOT FINISHED ****

#### Introduction

Creating single-page applications with [backbone.js](http://backbonejs.org/) and [tastypie](https://django-tastypie.readthedocs.org/en/latest/) is really fun. Although I think there are a few downsides to single pages apps (harder to unit tests, harder to debug, more expensive to maintain), the experience you have in a single page app is certainly more enjoyable. It makes scaling a web service a bit easier also because more of the work is done in the client's browser, and not in web-server threads/processes on the cloud.

I recently ran into a weird issue that I have seen more then one time involving clicks in a series of views in a backbone
router. I am going to demostrate how many application worked, and what the solution was.

The application I created has three panels: The left/middle vertical panels act as navigation, while the larger, right panel acts as the content. In this example, when you click on "receipients", the content pane will change to the dropdown list of "contact lists".

All three panels are separate backbone views within a single backbone router.


![My helpful screenshot]({{ baseurl.url }}/images/posts/dukemail_small.png)

The problem I have run into many times, is what is the best way to swap on different backbone views in the "content panel". In the image above, as you click through **"Campaign Name"** -> **"Receipients"** -> **"Craft Emails"**, the content view needs to toggle for each click.

The following example was a very early version of the router:

{% highlight javascript linenos %}
ContactRouter = Backbone.Router.extend({

	initialize : function(options) {
		this.options = options
		this.createSubViews();
		this.renderSubViews();
		this.listenTo(this.options.campaign_model,"change:state", this._on_route_confirm_and_send, this);
	},

	routes : {
		""  	: "_on_base_route",
		"name"  : "_on_route_campaign_name",
		"contact_list"  : "_on_route_contact_list",
		"craft"  : "_on_route_craft_emails",
		"delivery_details"  : "_on_route_delivery_details",
		"preview"  : "_on_route_preview",
		"confirm_and_send"  : "_on_route_confirm_and_send",
	},

	_on_base_route : function(){
		this.navigate('name', { trigger: true });
	},

 	_on_route_campaign_name : function(){
		this.options.campaign_model.set("tab","name");
		$("#js-edit-section").html(this.edit_campaign_name.el);
	},

 	_on_route_contact_list : function(){
		this.options.campaign_model.set("tab","contact_list");
		$("#js-edit-section").html(this.edit_contact_list.el);
	},

 	_on_route_craft_emails : function(){
		this.options.campaign_model.set("tab","craft");
		$("#js-edit-section").html(this.craft_email.el);
	    DUKEMAIL.Globals['editor'] = new Quill('#editor-container', {
	      modules: {
	        'toolbar'         : { container: '#formatting-container' },
	        'image-tooltip'   : true,
	        'link-tooltip'    : true
	      }
	    });
		DUKEMAIL.Globals['editor'].setHTML(this.options.campaign_model.get('message'));

	},

 	_on_route_delivery_details : function(){
		this.options.campaign_model.set("tab","delivery_details");
		$("#js-edit-section").html(this.delivery_details.el);

	},

 	_on_route_preview : function(){
		this.options.campaign_model.set("tab","preview");
		$("#js-edit-section").html(this.campaign_preview.el);
		var contact_list_pk = this.options.campaign_model.get("contact_list").id;
		NProgress.set(.5);
		DUKEMAIL.log(['_on_route_preview', contact_list_pk])
		DUKEMAIL.Collections.CampaignEmailIntervals.fetch({data : { limit : 10000, contact_list__id : contact_list_pk }}).complete(function(){
			NProgress.done();
			$(".js-data-table").dataTable({
			aaSorting: [[1, "asc"]],
			iDisplayLength: 25,
			"sDom": '<"clear">lfrtip<"clear">',
			"bDestroy": true
			});
		})
	},

 	_on_route_confirm_and_send : function(){
		this.options.campaign_model.set("tab","confirm_and_send");
		$("#js-edit-section").html(this.initiate_campaign.el);
		var JSON = {}
		JSON['data'] = {}
		JSON['data']['limit'] =  10000;
		JSON['data']['contact_list__id'] =  this.options.campaign_model.get("contact_list").id;
		JSON['data']['has_sent'] =  true;
		NProgress.set(.5);
		DUKEMAIL.Collections.CampaignEmailIntervals.fetch(JSON).complete(function(){
			NProgress.done();
		});
	},

	createSubViews : function(){
		var that = this;
		var model = this.options.campaign_model;

		this.your_campaign_block =	new DUKEMAIL.Views.YourCampaignsBlock({
			'user_id'	: that.options.user_id,
			'campaign_id' : that.options.campaign_id,
			'campaign_model' : that.options.campaign_model,
		});


		this.email_details = new DUKEMAIL.Views.EmailDetails({
			'campaign_id' : that.options.campaign_id,
			'model' : that.options.campaign_model,
		});

		this.base_view  = new DUKEMAIL.Views.CampaignBase({ model : model })
		this.edit_campaign_name = new DUKEMAIL.Views.EditCampaignName({ model : model });
		this.edit_contact_list = new DUKEMAIL.Views.EditContactList({ model : model });
		this.craft_email = new DUKEMAIL.Views.CraftEmail({ model : model });
		this.delivery_details = new DUKEMAIL.Views.EditDeliveryDetails({ model : model });
		this.campaign_preview = new DUKEMAIL.Views.PreviewCampaign({ model : model });
		this.initiate_campaign = new DUKEMAIL.Views.InitiateCampaign({ model : model });
	},

	renderSubViews : function(){
		this.base_view.render()
		this.your_campaign_block.render();
		this.email_details.render();
		this.edit_campaign_name.render();
		this.edit_contact_list.render();
		this.craft_email.render();
		this.delivery_details.render();
		this.campaign_preview.render();
		this.initiate_campaign.render();
	},
});
{% endhighlight %}

As you can see, I am swapping the content pane by utilizing (this isnt ideal) the `html()` attribute of `$("#js-edit-section")`
