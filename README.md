#iOS开发：给UITabBar和UIView添加小红点
我们经常使用的QQ或者是微信上面，uiTabBar或者是UIView、UILabel、UIImageView上面常有小红点，以示有新的什么什么东西需要你去查看或者设置之类的；关于小红点，网上有一篇非常好的三方库[WZLBadge](https://github.com/weng1250/WZLBadge)；但是由于自己的工程用不了那么多的炫酷动画，所以就自行封装了一个简单实用的；如下
![小红点](http://upload-images.jianshu.io/upload_images/1840399-5345ba5e4484d156.gif?imageMogr2/auto-orient/strip)

####给UIView添加小红点
创建一个UIView的category，通过runtime创建一个UIView属性；

.h中代码如下：

```
#import <UIKit/UIKit.h>

@interface UIView (DKSBadge)

/**
 *  通过创建label，创建小红点；
 *  我是想以后如果小红点里面需要显示个数的时候，好扩展；
 */
@property (nonatomic, strong) UIView *badge;

/**
 *  显示小红点
 */
- (void)showBadge;

/**
 *  隐藏小红点
 */
- (void)hidenBadge;

@end

```

.m中

```
#import "UIView+DKSBadge.h"
#import <objc/runtime.h>

static NSString * const badgeViewKey;
static NSInteger const pointWidth = 6; //小红点的宽高
static NSInteger const rightRange = 5; //距离控件右边的距离
@implementation UIView (DKSBadge)

- (void)showBadge
{
    if (self.badge == nil) {
        self.badge = [[UIView alloc] init];
        self.badge.frame = CGRectMake(self.frame.size.width + rightRange, -pointWidth / 2, pointWidth, pointWidth);
        self.badge.backgroundColor = [UIColor redColor];
        //圆角为宽度的一半
        self.badge.layer.cornerRadius = pointWidth / 2;
        //确保可以有圆角
        self.badge.layer.masksToBounds = YES;
        [self addSubview:self.badge];
        [self bringSubviewToFront:self.badge];
    }
}

- (void)hidenBadge
{
    //从父视图上面移除
    [self.badge removeFromSuperview];
}

#pragma mark - GetterAndSetter

- (UIView *)badge
{
    //通过runtime创建一个UIView的属性
    return objc_getAssociatedObject(self, &badgeViewKey);
}

- (void)setBadge:(UIView *)view
{
    objc_setAssociatedObject(self, &badgeViewKey, view, OBJC_ASSOCIATION_RETAIN);
}

@end
```
####如果需要在UITabBar上面添加小红点，也是同样的道理，需要创建UITabBarItem的category，此处就不在贴代码了，UITabBarItem的代码和UIView的代码都在下面的demo中；

注释：本来我是创建的UITabBar的Category，但是更新过Xcode之后，这个Demo的小红点就出现了混乱的状况，但是我公司的项目是没有问题的（具体原因没有搞明白）；所以这才创建了UITabBarItem的延展，但是UITabBar的延展还留在了Demo中，仅供参考！

如果有幸被你用到的话，需要将DKSBadge拖入到你的工程中；当然，你也可以自己封装一个！！！
[Demo的GitHub地址](https://github.com/FirstDKS521/RedBadge.git)