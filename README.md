# DTNetworkIndicatorManager
DTNetworkIndicatorManager with UIApplication Category

##Advantages:

1. When using the serial line there is no need to synchronize access to the counter (all tasks are performed sequentially).

2. dispatch_after to use serial queue

3. All the necessary serial even create unnecessary. It already exists - it is the main part, is responsible for the interface operation (main queue). 

##How To Use

######1. With DTNetworkIndicatorManager

```Objective-c
#import "DTNetworkIndicatorManager.h"
```

```Objective-c
  [[DTNetworkIndicatorManager defaultManager] showIndicator];
  [[DTNetworkIndicatorManager defaultManager] hideIndicator];
```

#####Example

```Objective-C
  NSDictionary *parameters =  @{@"CardNum": @"000000000000"};

  [[DTNetworkIndicatorManager defaultManager] showIndicator];
  [[DTRestManager defaultManager] getByName:@"TestGetRequest" params:parameters success:^(id result, NSError  *error) {
    if (error) {
      NSLog(@"%@", error.userInfo);
        [[DTNetworkIndicatorManager defaultManager] hideIndicator];
    } else {
      NSLog(@"%@", result);
        [[DTNetworkIndicatorManager defaultManager] hideIndicator];
    }
}];
```
######2. Like Category for UIApplication

```Objective-c
#import "UIApplication+DTNetworkIndicatorManager.h"
```

```Objective-c
  [[[UIApplication sharedApplication] showIndicator];
```

###Code
```Objective-c
static DTNetworkIndicatorManager *networkManager = nil;

- (void)showIndicator {
    dispatch_async(dispatch_get_main_queue(), ^{
        if (requestsCount == 0) {
            [UIApplication sharedApplication].networkActivityIndicatorVisible = YES;
        }
        
        requestsCount++;
    });
}

- (void)hideIndicator {
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.2 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        if (requestsCount == 0) {
            return;
        }
        
        requestsCount--;
        
        if (requestsCount == 0) {
            [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
        }
    });
}

```
