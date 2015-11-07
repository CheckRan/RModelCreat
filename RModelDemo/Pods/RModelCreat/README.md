# RModelCreat
 这是一款快速创建 Json Model 的框架 , 但是需要借助于 ESJsonFormat 配合使用
 
#假如你没有ESJsonFormat请先安装插件(保证你不会后悔的)
PS: [ESJsonFormat 地址](https://github.com/EnjoySR/ESJsonFormat-Xcode)

插件不能用 

//获取DVTPlugInCompatibilityUUID字段
```
defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID 
```

//将XXXX替换成刚刚获取的DVTPlugInCompatibilityUUID 
```
find ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins -name Info.plist -maxdepth 3 | xargs -I{} defaults write {} DVTPlugInCompatibilityUUIDs -array-add XXXX
```


![插件的注意点](https://github.com/CheckRan/RModelCreat/raw/master/ScreenShot/1.png)

#开始代码
###1导入数据什么的
![](https://github.com/CheckRan/RModelCreat/raw/master/ScreenShot/2.png)

添加的名字就是类的名字,结构是随数据从上至下
##(切记 : 是先完整解析一个子数据完整的结构,再解析下一个,多用几次就会了)

###2 导入头文件调用方法 , 完成
![好复杂啊,总算完成了](https://github.com/CheckRan/RModelCreat/raw/master/ScreenShot/3.png)


有人会说我需要返回一个Model 的数组,那你需要实现此方法

```
+(NSArray *)modelWithArray:(NSArray *)array
{
    return [self objectsWithArray:array];
}
```
逗比的后台给我的数据带有 id 的字典 , 还有description 怎么办 ?? 
别急,俺已经帮你实现 id -> ID   description -> desc 
,ESJsonFormat 勾选 Uppercase key for 'id'
description 没有办法,只能你在 Model 中手工改一下罗 !
![](https://github.com/CheckRan/RModelCreat/raw/master/ScreenShot/4.png)


假如说:你还有别的需要改 , 比如说 后台给的是 `NSObject` ,(估计没有后台会给这个吧 ),你需要这样

```
/**
 *  NSObject -> nsobject
 */
-(void)r_dealSetValueKeyNotFound:(NSString *)propertyName andDictionary:(NSDictionary *)dictionary
{
    /**
     *  继续使用 id -> ID  description -> desc 不需要就不写
     */
    [super r_dealSetValueKeyNotFound:propertyName andDictionary:dictionary];
    
    if ([propertyName isEqualToString:@"nsobject"]) {
        [self r_FindDictionary:dictionary andKey:@"NSObject" toPropertyName:propertyName];
    }
}
```
后台给的时间字符串是这样的:
`2015/11/06 22:15:37` , 界面中你要展示 `  2015/11/06`

```
/* 
把 数据 2015/11/06 22:15:37 ==>  2015/11/06
 */
-(void)addMorePropertyOrSomething
{
	/*
	* 在 .h 中加入 showEditDate 属性
	* 截取前面 9 个字母即可
	*/
	self.showEditDate = [self.editDate substringToIndex:9];
```

为了以防万一 , ESJsonFormat 不知道有没有可能会出现 有数组没有对应生成  `+ (NSDictionary *)objectClassInArray` 方法 , 你可以在对应的对象中加入此方法,格式就是这样

```
+ (NSDictionary *)objectClassInArray{
    return @{@"data" : [RDataDataModel class],@"banner" : [RBannerDataModel class]};
 }
```

---
##切记: 
###如果程序在解析数据的时候崩掉 , 请检查数据输入是否为空 ! ! ! !

#最近可能更新比较频繁,如果要不想麻烦,请用 cocoapods 

`
pod 'RModelCreat' ,'0.1.1'
`


---

###测试数据
```
{
如需转载 , 请 注明来源 :
`
 https://github.com/CheckRan/RModelCreat
 `