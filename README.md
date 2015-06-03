# First
test
Hey I'm a iOS developer From Xinyang,China
newer guide from -->https://guides.github.com/activities/hello-world/
哈哈哈

```
// MBProgressHUD.m（版本 0.9.1）里面MBBarProgressView类的drawRect:方法里：
if (isnan(angle)) angle = 0;
```
isnan()函数用来判断一个变量（比如上面的angle）是不是数字，如果不是数字，返回YES
(isnan = is not a number)

----------
忽略编译器警告（使用了已废弃方法、创建未使用的变量等），例子：
`-Wdeprecated`表示已废弃

```
//  SVProgressHUD.m里的361行
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wdeprecated"
	//此处是你写的iOS7以后已废弃的方法
    stringSize = [string sizeWithFont:self.stringLabel.font constrainedToSize:CGSizeMake(200.0f, 300.0f)];
#pragma clang diagnostic pop
```

----------

> 说MJRefresh是垃圾的这位[大神](https://www.v2ex.com/t/194294#reply26)自己写了个挺简洁的下拉刷新控件[PRRefreshControl](https://github.com/Elethom/PRRefreshControl)

```
//正常状态下，下拉后箭头朝下
case PRRefreshControlStateNormal:
{
	[UIView animateWithDuration:.2f animations:^{
		self.arrowImageView.hidden = NO;
		self.arrowImageView.transform = CGAffineTransformIdentity;
	}];
	[self.activityIndicator stopAnimating];
	break;
}
```
这里要说的是`self.arrowImageView.transform = CGAffineTransformIdentity`这句话，关于CGAffineTransformIdentity，苹果文档注释如下：

```
struct CGAffineTransform {
  CGFloat a, b, c, d;
  CGFloat tx, ty;
};

/* The identity transform: [ 1 0 0 1 0 0 ]. */ 
const CGAffineTransform CGAffineTransformIdentity

/* Return the transform [ a b c d tx ty ]. */ 
CGAffineTransform CGAffineTransformMake(CGFloat a, CGFloat b,
  CGFloat c, CGFloat d, CGFloat tx, CGFloat ty)

/* Return a transform which translates by `(tx, ty)':
     t' = [ 1 0 0 1 tx ty ] */
 CGAffineTransform CGAffineTransformMakeTranslation(CGFloat tx,
  CGFloat ty)

```
` [ 1 0 0 1 0 0 ]`是个什么鬼呢？



```
再次复制这一段
struct CGAffineTransform {
  CGFloat a, b, c, d;
  CGFloat tx, ty;
};
在二维空间里，如果原始坐标是（X，Y, 1）的话
		   |a  b  0 |
|X，Y, 1|  |c  d  0 | = [aX + cY + tx, bX + dY + ty, 1] ;
           |tx ty  1|    
乘以矩阵之后（X，Y, 1）→（aX + cY + tx, bX + dY + ty, 1）

那么[ 1 0 0 1 0 0 ]（CGAffineTransformIdentity）就相当于做了如下变换
		   |1  0  0 |
|X，Y, 1|  |0  1  0 | = [X, Y, 1] ;
           |0  0  1|  
             
```

所以`self.arrowImageView.transform = CGAffineTransformIdentity;`这句话的意思就相当于重置位置的作用
也有人说
>每次变换前都要置位，不然你变换用的坐标系统不是屏幕坐标系统（即绝对坐标系统），而是上一次变换后的坐标系统

`CGAffineTransformMake`和`CGAffineTransformMakeTranslation`同理


初始化下拉刷新控件的：
```
    PRRefreshControl *refreshControl = [[PRRefreshControl alloc] init];
    [refreshControl addTarget:self
                       action:@selector(refreshControlTriggered:)
             forControlEvents:UIControlEventValueChanged];
    self.refreshControl = refreshControl;
    [self.tableView addSubview:refreshControl];
```
突然发现其中的`UIControlEventValueChanged`事件（平常估计也就UISwitch、UISlider之类的才会用到），那么触发下拉刷新的`refreshControlTriggered:`方法为什么能在下拉50高度时执行呢？
答案在这里：
```
- (void)scrollViewDidEndDragging
{
    UIScrollView *scrollView = self.scrollView;
    UIEdgeInsets contentInset = self.scrollViewContentInset;
    CGFloat offset = scrollView.contentOffset.y + contentInset.top;
    if (!self.refreshing) {
        if (offset < - self.height) {
            [self beginRefreshing];
            [self sendActionsForControlEvents:UIControlEventValueChanged];
        }
    }
}
```
可以看到，`[self sendActionsForControlEvents:UIControlEventValueChanged];`就是触发前面绑定的`refreshControlTriggered`方法,有点回调的感觉有木有？

现在是19：22，我把github仓库名字改成了iOS-Learning-Notes
