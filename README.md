# Analysys ReactNative SDK

易观方舟react-native-analysys模块，封装了易观 Android & iOS SDK 常用 API ，使用此模块，可以在 React Native 开发的 App 中完成埋点的统计上报。

## npm模块安装

### 模块安装

```base
npm install react-native-analysys
```

### 模块link

```base
react-native link react-native-analysys
```

注意：React Native 0.60 及以上版本会 autolinking，不需要执行下边的 react-native link 命令。

## Android端

### SDK远程引入

```
dependencies
 { 
			//默认使用最新版本SDK，如需要使用特别版本请指定版本号 
			implementation('cn.com.analysys:analysys-arkanalysys:latest.release')') 
}
```

### SDK初始化

```
public class AnalysysApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        
        //  设置 打开 debug 模式，上线时请屏蔽
        AnalysysAgent.setDebugMode(this, 2);
        //  对SDK开始初始化
        AnalysysConfig config = new AnalysysConfig();
        // 设置key，77a52s552c892bn442v721为样例数据，请根据实际情况替换相应内容
        config.setAppKey("77a52s552c892bn442v721");
        // 设置渠道
        config.setChannel("豌豆荚");
        // 设置控件点击自动上报总开关
        config.setAutoTrackClick(true);
        // 调用SDK初始接口
        AnalysysAgent.init(this, config);
        //  设置上传地址，http://example.com:port为您上报地址
        AnalysysAgent.setUploadURL(mContext,"http://example.com:port");

    }
}
```

### RNAnalysysAgentPackage添加

```
    public class AnalysysApplication extends Application implements ReactApplication {
...
    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
            new RNAnalysysAgentPackage()
      );
    }
...
}
```

## iOS端

### 集成方舟SDK

React Native 0.60 及以上版本可以通过 CocoaPods 的方式引用 RNAnalysysAgentModule 插件及 易观方舟SDK；React Native 0.60 以下版本需要使用手动方式添加。

方式一：CocoaPods 集成
1. 将`npm install`下载后的文件：项目目录/node_modules/react-native-analysys文件拷贝至ios工程下（一般为.xcodeproj同级目录），在该目录下创建Podfile文件，并配置RN插件，如下示例：
    
    ```
    platform :ios, '8.0'
    use_frameworks!
    
    target 'YourApp' do
        pod 'RNAnalysysAgentModule', :path => 'react-native-analysys/'
    end
    ```
    
2. 关闭Xcode，在工程目录下执行`pod install`或`pod install --verbose --no-repo-update`，完成后打开xxx.xcworkspace工程

方式二：手动引入
1. 下载[方舟SDK](https://github.com/analysys/ans-ios-sdk/releases)并导入iOS工程中
2. 将`npm install`下载后的`RNAnalysysAgentModule`插件（路径：项目目录/node_modules/react-native-analysys_test/sdk/ios）导入工程
3. 勾选 Copy items if needed、Create groups- Add 完成添加类库
4. 添加依赖库:选择工程 - Targets - “项目名称” - Build Phase - Link Binary With Libraries ：`libz.tbd、libicucore.tbd、libsqlite3.tbd`

### 2.2 初始化SDK
在Xcode工程文件`~AppDelegate.m` 中导入头文件`"#import <AnalysysAgent/AnalysysAgent.h>"`，配置 SDK 相关内容。

```objectivec
#import <AnalysysAgent/AnalysysAgent.h>

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    //  设置key，77a52s552c892bn442v721为样例数据，请根据实际情况替换相应内容
    //  AnalysysAgent 配置信息
    AnalysysConfig.appKey = @"77a52s552c892bn442v721";
    // 设置渠道
    AnalysysConfig.channel = @"App Store";
    // 设置追踪新用户的首次属性
    AnalysysConfig.autoProfile = YES;
    // 设置上传数据使用AES加密，需添加加密模块
    //  AnalysysConfig.encryptType = AnalysysEncryptAES;
    //  使用配置初始化SDK
    [AnalysysAgent startWithConfig:AnalysysConfig];
    
    #if DEBUG
    [AnalysysAgent setDebugMode:AnalysysDebugButTrack];
#else
    [AnalysysAgent setDebugMode:AnalysysDebugOff];
#endif
    //  设置上报地址
    [AnalysysAgent setUploadURL:@"https://url:port""];
    
    ...
  return YES;
}

@end

```

## React Native 中 JS 使用

### 在 js 中获取 `RNAnalysysAgentModule` 模块

```js
//  易观统计模块
import { NativeModules } from "react-native";

const RNAnalysysAgentModule = NativeModules.RNAnalysysAgentModule;
```

### 接口调用

在相关需要进行统计的部分进行埋点。以点击购买事件为例：

```js
//  事件名称为：buy(购买)  事件附加属性为：ptype(产品分类): iPhone; model(型号): iPhone X
var properties = {
    'ptype': 'iPhone',
    'model': 'Apple iPhoneX'
}
RNAnalysysAgentModule.track('buy',properties)
```

## License

[gpl-3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

