---
layout: post
title:  "Getting Started with Core Data for iOS"
date:   2014-12-01
---

If you are new to iOS programming, one confusing aspect you are going to run into quickly (well, as soon as you need to persist data on your device) is Apple's Core Data. Core Data is an object graph and persistence framework provided by Apple in the Mac OS X and iOS operating systems. Core Data interfaces directly with SQLite, insulating the developer from the underlying SQL

[SSDataKit](https://github.com/soffes/SSDataKit) is a third party library that takes care of a lot of the boilerplate code needed to manage a Core Data application.


{% highlight objective-c %}

#import "SSRemoteManagedObject.h"

@interface GFUser : SSRemoteManagedObject

@property (nonatomic, strong) NSString *first_name;
@property (nonatomic, strong) NSString *last_name;
@property (nonatomic, strong) NSString *username;
@property (nonatomic, strong) NSString *email;

@end

{% endhighlight %}

{% highlight objective-c %}

#import "GFUser.h"

@implementation GFUser

@dynamic first_name;
@dynamic last_name;
@dynamic username;
@dynamic email;

+ (NSString *)entityName {
    return @"User";
}

@end

{% endhighlight %}

{% highlight objective-c %}

AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];
[manager GET:@"https://www.getfetcher.com/events/ajax/guest_list" parameters:params
success:^(AFHTTPRequestOperation *operation, id responseObject) {
[self.guestList removeAllObjects];
__weak NSManagedObjectContext *context = [GFContact mainQueueContext];
[context performBlockAndWait:^{
    NSDictionary *dictionary = (NSDictionary *)responseObject;
    for (NSDictionary *taskDictionary in [dictionary objectForKey:@"guest_list"]) {
        GFContact *contact = [GFContact objectWithDictionary:taskDictionary];
        [contact save];
        [self.guestList addObject:taskDictionary];
    }
    [TWStatus dismiss];
    [self.tableView reloadData];
    [self _updateCounts];
    }];

    } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
        [TWStatus dismiss];
        NSInteger statusCode = [operation.response statusCode];
        if( statusCode == 401 ) {
            //[self promptLogin];
        }
}];

{% endhighlight %}
