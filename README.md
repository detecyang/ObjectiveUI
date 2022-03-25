![logo](https://gitee.com/ObjectiveUI/ObjectiveUI/raw/master/Resource/logo@3x.png)

# ObjectiveUI

### 介绍
一个玩具，使用Objective-C实现类似SwiftUI的界面开发框架。

### 使用说明
语法和命名基本遵循SwiftUI，可以完全使用框架的接口创建简单应用。

##### 1、创建应用入口点
去掉原有oc工程中的`main`函数，并在自己的App代理对象中实现`OCAppProtocol`协议即可（有且只有一个）。例如：

```
@interface DemoApp : NSObject <OCAppProtocol>
@end

@implementation DemoApp

- (OCScene *)body {
    return
    OCWindowGroup(^{
        DemoView();
    }).onChange(^(OCScenePhase scenePhase, OCScenePhase newScenePhase) {
        switch(newScenePhase) {
            case OCScenePhaseBackground:
                break;
            case OCScenePhaseActive:
                break;
            case OCScenePhaseInactive:
                break;
        }
    });
}

@end
```
也可以创建单独的界面，通过`OCHostingController`方法与原有的oc工程桥接：

```
OCHostingController(view, ^(UIViewController *vc) {
    // ...
});
```

##### 2、视图元素的使用
与SwiftUI基本保持一致。继承`OCView`类并实现`body`方法，例如：

```
- (OCView *)body {
    return
    TabView(^{
        NavigationView(^{
            ScrollView(^{
                Image(@"logo")
                    .resizable(UIEdgeInsetsZero, UIImageResizingModeTile)
                    .aspectRatio(UIViewContentModeScaleAspectFit)
                    .frame(200, 50, OCAlignmentCenter);
                HStack(^{
                    Text(@"文本");
                    Button(@"按钮", nil);
                    TextField(@"输入框", $(self->_text), nil, nil);
                    TextEditor($(self->_text));
                });
                WebView([NSURL URLWithString:@"https://www.baidu.com/"])
                    .frame(300, 210, OCAlignmentCenter);
                for (int i = 0; i < 50; i++) {
                    HStack(^{
                        Text(@(i).stringValue);
                        Spacer();
                    });
                }
            }).tabItem(^{
                Image(@"circle.fill");
                Text(@"布局演示");
            }).navigationBarTitle1(@"布局演示");
        });
    });
}
```
![演示1](https://gitee.com/ObjectiveUI/ObjectiveUI/raw/master/Resource/1.gif)

##### 3、数据绑定
目前仅实现了`State`和`Binding`。与SwiftUI的写法稍有不同，State修饰是在类成员初始化时通过`State()`方法实现，变量的更新需要通过`StateUpdate()`方法。而Binding的修饰是使用`$()`，例如：

```
- (OCView *)body {
    return
    TabView($(_selection), ^{
        NavigationView(^{
            VStack(^{
                TextField(@"在此输入文本", $(self->_text), nil, ^{
                    [UIApplication.sharedApplication.keyWindow endEditing:YES];
                }).background(OCColor.lightGrayColor);
                HStack(^{
                    Text([NSString stringWithFormat:@"文本长度:%ld", self->_text.length]);
                    Button(@"清除", ^{
                        StateUpdate(self->_text, nil);
                    });
                });
                NavigationLink(SubViewMake($(self->_isActive2)), $(self->_isActive2), ^{
                    Text(@"二级页面");
                });
            }).tabItem(^{
                Image(@"pentagon.fill");
                Text(@"数据绑定");
            });
        }).navigationBarTitle1(@"数据绑定");
    });
}
```
![演示2](https://gitee.com/ObjectiveUI/ObjectiveUI/raw/master/Resource/2.gif)

### 说明
这个框架只是一个玩具，性能自然不及SwiftUI，不建议用在实际生产环境中。另外考虑到开源项目不免会被人利用并商业化，因此暂不考虑开源。

