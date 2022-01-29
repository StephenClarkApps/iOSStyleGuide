# Objective C

### Do you know how to use the dot syntax?
`self.myArray.count` not `[self.myArray count]` etc

### Do you know how to identify if you are running on a phone or pad?
`UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone` and `UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad`

### Do you know the difference between interface orientation and device orientation?
The interface orientation determines the orientation of the UI on the screen and can be either `UIInterfaceOrientationPortrait` or `UIInterfaceOrientationLanscape(Left|Right)`. The device orientation on the other hand has no bearing on the rendered UI and can indicate landscape, portrait, upside down, face up, face down or unknown. For this reason it is almost always `UIInterfaceOrientation` that we need to check when making UI decisions. This can be identified with `UIInterfaceOrientationIsPortrait([UIApplication sharedApplication].statusBarOrientation)` or `UIInterfaceOrientationIsLandscape([UIApplication sharedApplication].statusBarOrientation)`

### Do you know how to add a listener to notification centre?
There are two methods, one is to add a listener that calls a method, the other calls a block:<br/>
```objective-c
[[NSNotificationCenter defaultCenter] addObserverForName:CityAnalyticsDeviceIDDidChangeNotification
                                                  object:nil
                                                   queue:nil
                                              usingBlock:^(NSNotification * _Nonnull note) {
                                                  [self cityAnalyticsDeviceIDChanged];
                                              }];
[[NSNotificationCenter defaultCenter] addObserver:self
                                         selector:@selector(keyboardWillShow:)
                                             name:UIKeyboardWillShowNotification
                                           object:nil];
```

### Do you know how to remove a listener from notification centre?
To remove observers there are a couple of options:<br/>
```objective-c
//Remove all observers for a given class
[[NSNotificationCenter defaultCenter] removeObserver:self];
//Remove a single observer
[[NSNotificationCenter defaultCenter] removeObserver:self
                                                name:UIApplicationDidBecomeActiveNotification
                                              object:nil];
//Remove block based observer
self.initialSettingsLoadedObserver = [[NSNotificationCenter defaultCenter] addObserverForName:kSettingsLoaded
                                                                                           object:nil
                                                                                            queue:nil
                                                                                       usingBlock:^(NSNotification * _Nonnull note) {
                                                                                           [[NSNotificationCenter defaultCenter] removeObserver:self.initialSettingsLoadedObserver];
                                                                                           weakSelf.initialSettingsLoadedObserver = nil;
                                                                                           //Your code goes here
                                                                                       }];
```

### How to define a singleton / shared instance
```objective-c
+ (NNStyleManager *)sharedInstance {
    static dispatch_once_t pred;
    static CTStyleManager *sharedInstance = nil;
    
    dispatch_once(&pred, ^{
        sharedInstance = [CTStyleManager new];
    });
    
    return sharedInstance;
}
```

### When to use self vs weakSelf?
For any code that will run in a completion block **it is advisable to create a weak reference to self and use that within the block**. This way you are not adding to the retain count of the object and will handle a situation where the object may have been dealloc'd by the time the block executes. There are some exceptions such as the app delegate and singletons which will be in memory for the duration of the app lifecycle.

### Do you know the difference between an instance and a class method?
Instance methods (work on individual instances of classes) and are defined with a minus i.e `- (void)myInstanceMethod {` and class methods are defined with a plus `+ (void)myClassMethod {`. 

The class method can simply be called `[MyClass myClassMethod]` with no need to instantiate an instance of the class, where as the instance method would be something like `MyClass *class = [MyClass new]; [class myInstanceMethod];`. 

Another important difference is that a class method does not have any instance properties and therefore cannot use `self`.