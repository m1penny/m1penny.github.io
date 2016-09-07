---
title: 数据库Realm简单使用
date: 2016-06-01 14:25:53
tags:
---
1.CocoaPods
```ios
pod 'Realm', '~> 1.0.0'
```
2.建模型

使用Xcode插件能够方便我们创建realm的数据库模型，插件名“RealmPlugin”，在Alcatraz可下。

![](http://o7awmhryr.bkt.clouddn.com/780CA48C-752F-49BE-B6D1-F669A164FE92.png)
<!-- more -->
.h：
```ios
#import <Realm/Realm.h>

#import "Dog.h"

@interface Person : RLMObject
@property NSString *name;
@property RLMArray<Dog *><Dog> *dogs;
@property NSInteger personId;
@end

// This protocol enables typed collections. i.e.:
// RLMArray<Person>
RLM_ARRAY_TYPE(Person)
```
.m：
```ios
#import "Person.h"

@implementation Person

+ (NSString *)primaryKey{
    return @"personId";
}

@end
```
3.指定默认数据库

实际开发中，可能需要根据不同用户来创建数据库
```ios
RLMRealmConfiguration *config = [RLMRealmConfiguration defaultConfiguration];
    
// Use the default directory, but replace the filename with the username
config.fileURL = [[[config.fileURL URLByDeletingLastPathComponent]
                       URLByAppendingPathComponent:@"test"]
                      URLByAppendingPathExtension:@"realm"];
    
// Set this as the configuration used for the default Realm
[RLMRealmConfiguration setDefaultConfiguration:config];
```
4.插入数据
```ios
Person *person = [[Person alloc]init];
person.name = @"大王";
person.personId = 0;
[person.dogs addObjects:dogs];
[realm beginWriteTransaction];
[realm addOrUpdateObject:person];
[realm commitWriteTransaction];
```
5.查询数据
![](http://o7awmhryr.bkt.clouddn.com/5CCD477F-58A7-44A1-8BE3-44519657BF84.png)
```ios
RLMResults<Person *> *persons = [Person allObjects];
    for (Person *person in persons) {
        NSLog(@"%@",person.name);
        NSLog(@"%@",person.dogs);
    }
```
6.查看数据库

使用Realm Browser
下载地址：<https://itunes.apple.com/app/realm-browser/id1007457278/>

![](http://o7awmhryr.bkt.clouddn.com/A779C5BD-2E43-4E5F-88B9-FA419DE0E3B6.png)

![](http://o7awmhryr.bkt.clouddn.com/1AB20279-8FF5-49EA-9888-87E560A2AEFF.png)

Demo地址：<https://github.com/m1penny/RealmTest/>
