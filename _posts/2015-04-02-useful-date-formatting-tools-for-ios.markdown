---
layout: post
title:  "Useful Date Formatting Tools For iOS"
date:   2015-04-02
---

Here is a list of useful date formatting tools I use in every iphone project on iOS. I plan to add to this
as new functionality is needed.

{% highlight objc %}
#import <Foundation/Foundation.h>

@interface GFDateUtils : NSObject


+(NSString*)getAbbreviatedDayOfWeek:(NSDate *)theDate;
+(NSString*)getDayInMonth:(NSDate *)theDate;
+(NSDate*)str2date:(NSString *)strDate;

@end

{% endhighlight %}

{% highlight objc %}

+(NSDate*)str2date:(NSString *)strDate{
	/* convert NSString* -> NSDate* */
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"MM/dd/yyyy"];
    return [dateFormatter dateFromString:strDate];
}

+(NSString*)getAbbreviatedDayOfWeek:(NSDate *)theDate{
	/* Returns abbr. day of week from NSDate* */
    NSDateFormatter* dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"E"];
    return [dateFormatter stringFromDate:theDate];
}

+(NSString*)getDayInMonth:(NSDate *)theDate {
	/* Returns Day Of Month From NSDate* */
    NSDateFormatter* dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"d"];
    return [dateFormatter stringFromDate:theDate];
}

{% endhighlight %}