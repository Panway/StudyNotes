
UIWebView与JS的交互，说白了就是Objective-C和JavaScript的相互调用。Objective-C调用JavaScript代码的方法，是通过UIWebView的 `- (NSString *)stringByEvaluatingJavaScriptFromString:(NSString *)script;`的方法来实现的。该方法向UIWebView传递一段需要执行的JavaScript代码最后获取执行结果。

JavaScript调用Objective-C的方法，并没有现成的API，但是有些方法可以达到相应的效果。具体是利用UIWebView的特性：在UIWebView的内发起的所有网络请求，都可以通过delegate函数得到通知。

说明：

* 本文是一个小白记录OC与JS交互的学习历程，适合跟我一样的小白，大神若要喷，请轻喷^_^

* 学习UIWebView与JS的交互之前，建议熟悉下HTML和Javascript的基本语法，不用看太多，在[w3school](http://www.w3school.com.cn/index.html)看一到两天HTML，再看一到两天JS就行。

##OC调用JS方法、JS调用OC方法（不使用第三方开源库的情况下）


准备工作：

1.新建一个Single View Application，
再新建一个ViewController（eg:BasicUsageViewController），然后在StoryBoard新建一个ViewController，拖一个UIWebView和UILabel以备用，关联webView及代理

```objective-c
@property (weak, nonatomic) IBOutlet UIWebView *webView;
@property (weak, nonatomic) IBOutlet UILabel *testLabel;
```

2.在工程中新建一个`web1.html`文件（Commend+N、`Other`、`Empty`、`Next`、输入、`create`），代码如下：

``` html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
            <title>我是HTML标题</title>
            </head>
    <body bgcolor="#9aff9a">
        <div id="addNewNodeTest">
            <p id="p1"> 这是段落A。</p>
            <p id="p2"> 这是段落B。</p>
        </div>
        <div class="page">
            <button onclick="changeUILabelText()"> 改变UILabel文字 </button>
            <button onclick="logText()"> NSLog打印文字 </button>
        </div>
    </body>
    
</html>
```

3.再在工程中新建一个`test.js`文件，代码如下：

``` javascript
//添加子节点
function addNewNodeTest () {
    var para = document.createElement("p");
    var node = document.createTextNode("这是新段落。");
    para.appendChild(node);
    var element = document.getElementById("addNewNodeTest");
    element.appendChild(para);
    console.log("添加子节点成功");
}

//改变UILabel的文本
function changeUILabelText() {
    //"changelabeltext"是你自己定的一个协议。
    //注url不要含大写字母，就算是大写字母，在`webView:shouldStartLoadWithRequest:navigationType:`代理方法里也会被替换成小写字母
    var url = "changelabeltext:" + "我是改变后的文字";
    //给document.location重新赋值就相当于webView加载一个新的URL，所以又会调用`webView:shouldStartLoadWithRequest:navigationType:`方法，然后就可以在这个代理方法里截获这个重定向请求
    document.location = url;
}

//也可以自己封装个传参数的方法
function sendCommand(cmd,param){
    var url = "yourprotocol:" + cmd + ":" + param;
    document.location = url;
}
//打印测试
function logText(){
    sendCommand("log","Hi,I'm In logText Function");
}
```

好了，现在可以开撸了

4.加载webView并插入测试js

``` objective-c 
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    NSString* path = [[NSBundle mainBundle] pathForResource:@"web1" ofType:@"html"];
    [self.webView loadRequest:[NSURLRequest requestWithURL:[NSURL fileURLWithPath:path]]];
    
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"test" ofType:@"js"];
    NSString *jsString = [[NSString alloc] initWithContentsOfFile:filePath encoding:NSUTF8StringEncoding error:nil];
    [self.webView stringByEvaluatingJavaScriptFromString:jsString];
}
```

5、加载结束,获取HTML页面title元素，赋值给self.title

``` objective-c
- (void)webViewDidFinishLoad:(UIWebView *)webView {
    // 获取HTML页面title元素，赋值给self.title
    self.title = [webView stringByEvaluatingJavaScriptFromString:@"document.title"];
}
```
6、建几个按钮，体验插入js的几种方式

![模拟器](http://panway.b0.upaiyun.com/resource/20151126_WebViewAndJS1.png)

``` objective-c
//分别对应上图3个按钮
- (IBAction)insertJavaScript1:(UIButton *)sender {
    //方法1：预加载的test.js内部已经写了addNewNodeTest()方法，这里只需注入"addNewNodeTest()"字符串即可
    [self.webView stringByEvaluatingJavaScriptFromString:@"addNewNodeTest()"];
}

- (IBAction)insertJavaScript2:(UIButton *)sender {
    //方法2：把test.js内部的addNewNodeTest()方法复制过来，去掉行与行之间的空格
    //字符串双引号要么前面加转义符"\",要么变成单引号，例如：
    NSString *addNewNode = @"var para = document.createElement(\"p\");var node=document.createTextNode('这是新段落。');para.appendChild(node);var element=document.getElementById('addNewNodeTest');element.appendChild(para);";
    [self.webView stringByEvaluatingJavaScriptFromString:addNewNode];
}

- (IBAction)insertJavaScript3:(UIButton *)sender {
    //方法3：把test.js内部的addNewNodeTest()方法复制过来，并在每一行首尾加上双引号（跟方法2差不多）
    NSString *addNewNode =
    @"var para = document.createElement('p');"
    "var node = document.createTextNode('这是新段落。');"
    "para.appendChild(node);"
    "var element = document.getElementById('addNewNodeTest');"
    "element.appendChild(para);";
    [self.webView stringByEvaluatingJavaScriptFromString:addNewNode];
}
```

说明：addNewNodeTest()方法执行的操作是创建了一个节点`<p> 这是新段落。</p>`,添加到了位置1，然后webView上就会新增一行，不懂的同学请自行脑补（看不懂也没关系，这里只是演示怎么用OC调js代码）

``` html
	<div id="addNewNodeTest">
        <p id="p1"> 这是段落A。</p>
        <p id="p2"> 这是段落B。</p>
        //位置1
    </div>

```

7.好了，现在让js调OC的方法：在ViewController里添加如下代码：

``` objective-c
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    NSLog(@"开始加载请求");
    //当点击按钮时，navigationType = UIWebViewNavigationTypeOther
    NSString *requestString = [[request URL] absoluteString];
    NSArray *components = [requestString componentsSeparatedByString:@":"];
    if ([components[0] isEqualToString:@"changelabeltext"] && components.count > 1) {
        //这种通过URL传参数的方式貌似不是太好，因为参数如果含中文还得URL解码，eg:
        self.testLabel.text = [components[1] stringByRemovingPercentEncoding];
        return NO;
    }
    //也可以这样判断
    else if([request.URL.scheme isEqualToString:@"yourprotocol"]) {
        NSLog(@"%@",[components[2] stringByRemovingPercentEncoding]);
        return NO;
    }
    return YES;
}
```

点击webView里的`改变UILabel文字`按钮会发现`testLabel `的文字变了，这里解释下原因：`web1.html`代码中
`<button onclick="changeUILabelText()"> 改变UILabel文字 </button>`这个按钮绑定了一个方法，名字叫`changeUILabelText()`，点击就会调用changeUILabelText()方法（当然包含这个方法的test.js已经加载了）,然后webView的URL变了就会重新加载，这样在回调方法`webView:shouldStartLoadWithRequest:navigationType:`会再次调用，然后就可以在这个代理方法里截获这个重定向请求的`request.URL.absoluteString`来处理OC代码了

说明：

（1）Objective-C调用JavaScript代码的时候是同步的

`- (NSString *)stringByEvaluatingJavaScriptFromString:(NSString *)script;`

（2）JavaScript调用Objective-C代码的时候是异步的

`- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;`

#调试模拟器或真机里的WebView的技巧

* 模拟器

模拟器加载网页后，打开电脑端Safari（确保偏好设置里的`高级` - `在菜单中显示“开发”菜单`选项已打开），然后选择`开发`、`Simulator`,就会看见模拟器正在运行的`web1.html`，点击`web1.html`就来到了控制台。
点击`方式一`按钮，就会发现控制台标签页最先面有输出，这是因为在`test.js`的`addNewNodeTest ()`的最后一行有这么一句话：`console.log("添加子节点成功");`，在这里，`console.log()`相当于NSLog，括号内可以直接加变量。

当然，你也可以在控制台插入js代码，如下图：在左下角输入一句js代码`alert('666');`就能在模拟器上得到反馈，当然，此时你输入`addNewNodeTest();`效果跟点击`方式一`按钮是一样的

![模拟器调试之控制台](http://panway.b0.upaiyun.com/resource/20151126_WebViewAndJS2.png)

你也可以切换到`调试器`标签，然后打个断点，点击`方式一`按钮，就可以单步调试了。有兴趣的同学可以切换到`元素`标签页看看

![模拟器调试之调试器](http://panway.b0.upaiyun.com/resource/20151126_WebViewAndJS3.png)


* 真机

首先在手机的`设置` - `Safari` - `高级` - 启用`Web检查器`，然后用数据线连接电脑，Xcode运行你的项目，打开一个含webView的页面，就可以在电脑端Safari的`开发`菜单下看到你的设备了，调试方法同上


#高级用法（WebViewJavascriptBridge）

[WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge) 是一个用于UIWebView / WKWebViews和JS交互的封装库，连Facebook Messenger都在使用。

这里我就引用一下[杨骑滔的这篇博客](http://kittenyang.com/webview-javascript-bridge/)的内容，也就是通过实现以下功能来学习WebViewJavascriptBridge的使用（侵删）。

原文已经比较详尽了，但是有一些地方对于我等小白来说可能不够详细，所以折腾了不少时间，所以在这里对原文做了一点修改，更加清晰易懂。

###要实现的功能

* WebView展示一段HTML，禁止HTML文本中自带的 `<img>` 标签自动加载，也就是说下载图片的操作放在native端来处理，并通过JS将图片在Cache中的地址返回给UIWebview。

* 实现点击WebView图片放大、保存图片到相册等操作。

之所以要把图片操作放在native端做的好处在于：1、可以进行本地缓存，下次进入这篇文章可以直接从缓存读取，提高响应速度并且节省用户流量。2、可以实现点击图片放大、保存图片到相册等操作。

技术难点也有两个：

* 如何让HTML文本onLoad的时候，禁用自身的图片加载而是从本地获取图片？
* 如何把native端下载好的图片返回给网页？

先来看看基本用法

在WebViewJavascriptBridge中，交互的方式只有两种：send 和 callHandler，JS和OC都有这两个方法，所以对应的四种关系是（很重要）：

![四种关系图表](http://panway.b0.upaiyun.com/resource/Screen-Shot-2015-08-02-at-01-35-51.png)


以上表中的对应关系的解读是，例如第一条：在JS中如果调用了bridge.send()，那么将触发OC端_bridge初始化方法中的回调。

同理，第二条，在JS中调用了bridge.callHandler('testJavascriptHandler')，它将触发OC端注册的同名方法:

也就是说，一种语言register了Handler（回调或者block），另一种语言callHandler就会执行回调或者block，还能传递数据；不理解不要紧，下面的Demo这四种方式全都有例子。

了解了使用规则，下面来看看在我们这个实际需求中应用的整体思路：
![整体思路](http://panway.b0.upaiyun.com/resource/20151126_WebViewAndJS4.png)
废话不说，直接开撸：

1、导入`WebViewJavascriptBridge`，新建一个ViewController，声明一个WebViewJavascriptBridge实例：

```objective-c
@property WebViewJavascriptBridge* bridge;
```

2、找一个含图片的html，比如[这一篇](http://panway.b0.upaiyun.com/resource/article1.html)（源码已做删减），导入到项目中

3、在项目中新建一个js文件，比如`imageCache.js`，贴上如下代码：

``` javascript
//一加载这个js就会调用下面自己写的onLoaded() 方法
window.onload = function() {
    onLoaded();
}

//使用WebViewJavascriptBridge的话，这一段是必须的（固定写法）
function connectWebViewJavascriptBridge(callback) {
    if (window.WebViewJavascriptBridge) {
        callback(WebViewJavascriptBridge)
    } else {
        document.addEventListener('WebViewJavascriptBridgeReady', function() {
                                  callback(WebViewJavascriptBridge)
                                  }, false)
    }
}

//上面已经说了，一插入js，这个方法就开始执行
function onLoaded() {
    connectWebViewJavascriptBridge(function(bridge) {
                                   //document.querySelectorAll：按文档顺序返回指定元素节点的子树中匹配选择器的元素集合，如果没有匹配返回空集合
                                   //下面这几句是提取所有img标签的esrc属性值（图片的URL），并存到imageUrlsArray这个数组中
                                   var allImage = document.querySelectorAll("img");
                                   allImage = Array.prototype.slice.call(allImage, 0);
                                   var imageUrlsArray = new Array();
                                   allImage.forEach(function(image) {
                                                    var esrc = image.getAttribute("esrc");
                                                    var newLength = imageUrlsArray.push(esrc);
                                                    });
                                   //将imageUrlsArray这个数组发送到OC的block
                                   bridge.send(imageUrlsArray);////四种关系图表之第1种
                                   
                                   bridge.init(function(message, responseCallback) {
                                               alert(message);
                                               if (responseCallback) {
                                               responseCallback("Message1已收到，送你个Message2")
                                               }
                                    })
                                   //这里先注册下，等待OC代码的_bridge调用([_bridge callHandler:....])
                                   bridge.registerHandler('imagesDownloadComplete', function(data, responseCallback) {
                                                          var allImage = document.querySelectorAll("img");
                                                          allImage = Array.prototype.slice.call(allImage, 0);
                                                          allImage.forEach(function(image) {
                                                                           if (image.getAttribute("esrc") == data[0] || image.getAttribute("esrc") == decodeURIComponent(data[0])) {
                                                                           image.src = data[1];
                                                                           }
                                                                           
                                                                           });
                                                          responseCallback("图片"+data[0]+"已加载")
                                                          })
                                   
                                   //使用WebViewJavascriptBridge的话，这一段是必须的，不然上面的imageUrlsArray传不过去
                                   bridge.send('Please respond to this', function responseCallback(responseData) {
                                               console.log("Javascript got its response", responseData)
                                               })
                                   });
    
}
```

4、viewDidLoad里加载 webView

``` objective-c
NSString *path = [[NSBundle mainBundle] pathForResource:@"article1" ofType:@"html"];
    //原网页html代码
    NSString *_content = [NSString stringWithContentsOfFile:path encoding:NSUTF8StringEncoding error:nil];
    //我们要做的第一步是替换获取的HTML文本中默认的src，避免webView自动加载图片
    _content = [_content stringByReplacingOccurrencesOfString:@"src" withString:@"esrc"];
    //正则替换,给每个图片添加一个onImageClick点击方法
    NSRegularExpression *regex = [NSRegularExpression regularExpressionWithPattern:@"(<img[^>]+esrc=\")(\\S+)\"" options:0 error:nil];
    //终于得到我想要的html了!!!
    _content = [regex stringByReplacingMatchesInString:_content options:0 range:NSMakeRange(0, _content.length) withTemplate:@"<img esrc=\"$2\" onClick=\"javascript:onImageClick('$2')\""];
    [self.webView loadHTMLString:_content baseURL:nil];
    
    //插入js
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"imageCache" ofType:@"js"];
    NSString *jsString = [[NSString alloc] initWithContentsOfFile:filePath encoding:NSUTF8StringEncoding error:nil];
    [self.webView stringByEvaluatingJavaScriptFromString:jsString];
    
    //初始化一个WebViewJavascript桥梁，方便imageCache.js把数据传过来
    self.bridge = [WebViewJavascriptBridge bridgeForWebView:self.webView webViewDelegate:self handler:^(id data, WVJBResponseCallback responseCallback) {
        NSLog(@"###来自imageCache.js的图片URL数组: %@", data);
        //利用SDWebImageManager下载图片到本地
        [self downloadAllImagesInNative:data];
        _imageURLs = data;
        responseCallback(@"###Right back atcha");
    }];
```


``` objective-c
#pragma mark -- 下载全部图片
-(void)downloadAllImagesInNative:(NSArray *)imageUrls{
    SDWebImageManager *manager = [SDWebImageManager sharedManager];
    //初始化一个数组用于存image
    _allImagesOfThisArticle = [NSMutableArray arrayWithCapacity:imageUrls.count];
    for (NSUInteger i = 0; i < imageUrls.count; i++) {
        NSString *_url = imageUrls[i];
        [manager downloadImageWithURL:[NSURL URLWithString:_url] options:SDWebImageHighPriority progress:nil completed:^(UIImage *image, NSError *error, SDImageCacheType cacheType, BOOL finished, NSURL *imageURL) {
            
            if (image) {
                [_allImagesOfThisArticle addObject:image];
                dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
                    NSString *imgB64 = [UIImageJPEGRepresentation(image, 1.0) base64EncodedStringWithOptions:NSDataBase64Encoding64CharacterLineLength];
                    //把图片在磁盘中的地址传回给JS
                    NSString *key = [manager cacheKeyForURL:imageURL];
                    
                    NSString *source = [NSString stringWithFormat:@"data:image/png;base64,%@", imgB64];
                    //四种关系图表之第4种
                    [_bridge callHandler:@"imagesDownloadComplete" data:@[key,source] responseCallback:^(id responseData) {
                        NSLog(@"js把img标签的esrc属性替换后-->%@<",responseData);
                    }];
                    
                });
                
            }
            
        }];
        
    }
    
}
```

好了，到这里为止就webView就可以加载本地缓存的图片了，如果要实现图片的点击放大，请接着往下看

5、imageCache.js里添加图片点击事件：

``` javascript
//图片点击会触发
function onImageClick(picUrl){
    connectWebViewJavascriptBridge(function(bridge) {
                                   //作者用的是"p img[esrc]"，意思是获取p标签里的img的src值
                                   //我这里的图片是div，所以要改成"div img[esrc]"
                                   //var allImage = document.getElementsByTagName('img');//这样比较通用
                                   var allImage = document.querySelectorAll("div img[esrc]");
                                   allImage = Array.prototype.slice.call(allImage, 0);
                                   var urls = new Array();
                                   var index = -1;
                                   var x = 0;
                                   var y = 0;
                                   var width = 0;
                                   var height = 0;
                                   //获取点击图片在所有图片中的编号以及在图片相对于webView左上角的位置、宽高，并把这些信息返回给OC
                                   allImage.forEach(function(image) {
                                                    var imgUrl = image.getAttribute("esrc");
                                                    var newLength = urls.push(imgUrl);
                                                    if(imgUrl == picUrl || imgUrl == decodeURIComponent(picUrl)){
                                                    index = newLength-1;
                                                    x = image.getBoundingClientRect().left;
                                                    y = image.getBoundingClientRect().top;
                                                    x = x + document.documentElement.scrollLeft;
                                                    y = y + document.documentElement.scrollTop;
                                                    width = image.width;
                                                    height = image.height;
                                                    console.log("x:"+x +";y:" + y+";width:"+image.width +";height:"+image.height);
                                                    }
                                                    });
                                   
                                   console.log("检测到点击"+"x="+x+"y="+y+"width="+width+"height="+height);
                                   //四种关系图表之第2种
                                   bridge.callHandler('imageDidClicked', {'index':index,'x':x,'y':y,'width':width,'height':height}, function(response) {
                                                      console.log("JS已经发出imgurl和index，同时收到回调，说明OC已经收到数据");
                                                      });
                                   });
    
}

```

6、viewDidLoad里注册js图片点击事件回调,这里我用了一个简单的图片浏览器[HZPhotoBrowser](https://github.com/chennyhuang/HZPhotoBrowser),修改了部分代码使能够适用于webView


``` objective-c
//这里注册一下，imageCache.js里的`bridge.callHandler('imageDidClicked', {'index':index,'x':x,'y':y,'width':width,'height':height}, function(response)`就会传数据过来
    [_bridge registerHandler:@"imageDidClicked" handler:^(id data, WVJBResponseCallback responseCallback) {
        
        NSInteger index = [[data objectForKey:@"index"] integerValue];
        
        CGFloat originX = [[data objectForKey:@"x"] floatValue];
        CGFloat originY = [[data objectForKey:@"y"] floatValue];
        CGFloat width   = [[data objectForKey:@"width"] floatValue];
        CGFloat height  = [[data objectForKey:@"height"] floatValue];
        
        //启动图片浏览器
        HZPhotoBrowser *browserVc = [[HZPhotoBrowser alloc] init];
        // browserVc.sourceImagesContainerView = cell.webView; // 原图的父控件
        browserVc.imageCount = _allImagesOfThisArticle.count; // 图片总数
        browserVc.currentImageIndex = index;
        browserVc.delegate = self;
        browserVc.imageFrameinWebView = CGRectMake(originX, originY+64, width, height);
        [browserVc show];
        
        NSLog(@"OC已经收到JS的imageDidClicked了: %@", data);
        responseCallback(@"OC已经收到JS的imageDidClicked了");
    }];
    //四种关系图表之第3种（测试）
//    [_bridge send:@"###Message1：我将会被发送到imageCache.js里bridge.init()的回调里"];
    
    //四种关系图表之第3种（测试）
//    [_bridge send:@"###Message1：我将会被发送到imageCache.js里bridge.init()的回调里，imageCache.js还会给我回调,不信你可能下面的Log" responseCallback:^(id responseData) {
//        NSLog(@"###%@", responseData);
//    }];
```


``` objective-c
#pragma mark - HZPhotoBrowser的代理方法
//这里没有占位小图，所以就让大图代替
- (UIImage *)photoBrowser:(HZPhotoBrowser *)browser placeholderImageForIndex:(NSInteger)index {
    return _allImagesOfThisArticle[index];
}

- (NSURL *)photoBrowser:(HZPhotoBrowser *)browser highQualityImageURLForIndex:(NSInteger)index {
    return [NSURL URLWithString:_imageURLs[index]];
}
```

最终效果

![最终效果](http://panway.b0.upaiyun.com/resource/20151126_WebViewAndJS5.gif)

好了，到此为止WebViewJavascriptBridge的基本用法已基本说完了，虽然很简单，但是也花了我一天的时间，写的同时又发现了不少新东西，还是很值的。这里是这个小Demo的[源码](http://panway.b0.upaiyun.com/resource/JSAndOC.zip)。渣渣代码，就不上传github了。




* 参考及推荐文章： 

[UIWebView与JS的深度交互](http://kittenyang.com/webview-javascript-bridge/)

[UIWebView与JavaScript的那些事儿](http://blog.csdn.net/devday/article/details/6603923)

[iOS开发之Objective-C与JavaScript的交互](http://www.cnblogs.com/zhuqil/archive/2011/08/03/2126562.html)

[关于UIWebView和PhoneGap的总结](http://blog.devtang.com/blog/2012/03/24/talk-about-uiwebview-and-phonegap/)