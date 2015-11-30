---
layout: post
title:  "Saving A User Address Book in iOS Programmatically"
date:   2015-11-20
categories: ios
---

I recently built an iOS application that needed to access and save our user's iOS contacts. It took a little bit of searching around the internet to find a way that worked for me, so I decided to document it here. The first step is to include the `AddressBook` header file:

{% highlight objc %}
#import <AddressBook/AddressBook.h>
{% endhighlight %}

This header does not require any linked libraries etc. to compile. The next step is to create an `ABAddressBookRef` and query the user for permissions (if they have not already given it). The following block of code does this.

{% highlight objc %}

    NSMutableDictionary* contactDict;
    ABAddressBookRef addressBookRef = ABAddressBookCreateWithOptions(NULL, NULL);
    
    if (ABAddressBookGetAuthorizationStatus() == 
			kABAuthorizationStatusNotDetermined) {
        ABAddressBookRequestAccessWithCompletion(addressBookRef, 
			^(bool granted, CFErrorRef error) {
            ABAddressBookRef addressBook = ABAddressBookCreate();
        });
    }

{% endhighlight %}

If the user has never given the app permisson to it's contact list, `ABAddressBookGetAuthorizationStatus()` should return `kABAuthorizationStatusNotDetermined`. If the user already has access to the contact list, `ABAddressBookGetAuthorizationStatus()` will return `kABAuthorizationStatusAuthorized`. After the user has authorized the contact list access, you need to copy them all to an internal data structure (in my case, an array of dictionaries), and then send it to an endpoint, save it to YapDatabase, etc. The following block of code saves all of th contacts first name, last name, and phone number into `contactsList`.

{% highlight objc %}

    else if (ABAddressBookGetAuthorizationStatus() == kABAuthorizationStatusAuthorized) {
        
        CFErrorRef *error = NULL;
        ABAddressBookRef addressBook = ABAddressBookCreateWithOptions(NULL, error);
        CFArrayRef allPeople = ABAddressBookCopyArrayOfAllPeople(addressBook);
        CFIndex numberOfPeople = ABAddressBookGetPersonCount(addressBook);
        
        for(int i = 0; i < numberOfPeople; i++) {
            
            ABRecordRef person = CFArrayGetValueAtIndex( allPeople, i );
            
            NSString *firstName = (__bridge NSString *)
                (ABRecordCopyValue(person, kABPersonFirstNameProperty));
            NSString *lastName = (__bridge NSString *)
                (ABRecordCopyValue(person, kABPersonLastNameProperty));
            
            ABMultiValueRef phoneNumbers = 
				ABRecordCopyValue(person, kABPersonPhoneProperty);
            for (CFIndex i = 0; i < ABMultiValueGetCount(phoneNumbers); i++) {
                NSString *phoneNumber = (__bridge_transfer NSString *)
                    ABMultiValueCopyValueAtIndex(phoneNumbers, i);
                contactDict = [NSMutableDictionary
                          dictionaryWithObjectsAndKeys:
                          firstName, @"first_name",
                          lastName, @"last_name",
                          phoneNumber, @"phone_number",
                          nil];
                [contactsList addObject:contactDict];
            }
        }       
    }
    else {
        // Send an alert telling user to change privacy setting in settings app
    }

{% endhighlight %}

Finally, if you want to send this data to an endpoint, I recommend you convert the data to JSON before you send it over the wire:

{% highlight objc %}

NSError *jsonError;
NSMutableDictionary* params = [[NSMutableDictionary alloc] init];
NSData *jsonData = [NSJSONSerialization dataWithJSONObject:
		contactsList options:0 error:&jsonError];
[params setObject:[[NSString alloc] 
	initWithData:jsonData encoding:NSUTF8StringEncoding] 
	forKey:@"contacts"];

{% endhighlight %}
 
If you have any questions, feel free to reach out to me on [twitter](https://twitter.com/josephmisiti) and if you need iOS help, please reach me at `joseph.misiti@mathandpencil.com`
 
