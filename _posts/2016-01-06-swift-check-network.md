---
layout: post
title:  "Swift 网络连接状态检测"
date:   2016-01-06 23:50:00 +0800
categories: Swift
---

做 IOS 或 OSX 开发时，经常需要判断当前的网络连接状态，今天整理了一段代码，分享出来。

创建 Reachability.swift 文件，代码如下：
{% highlight swift %}
public class Reachability {
    class func isConnectedToNetwork() -> Bool {
        var zeroAddress = sockaddr_in()
        zeroAddress.sin_len = UInt8(sizeofValue(zeroAddress))
        zeroAddress.sin_family = sa_family_t(AF_INET)
        let defaultRouteReachability = withUnsafePointer(&zeroAddress) {
            SCNetworkReachabilityCreateWithAddress(nil, UnsafePointer($0))
        }
        var flags = SCNetworkReachabilityFlags()
        if !SCNetworkReachabilityGetFlags(defaultRouteReachability!, &flags) {
            return false
        }
        let isReachable = (flags.rawValue & UInt32(kSCNetworkFlagsReachable)) != 0
        let needsConnection = (flags.rawValue & UInt32(kSCNetworkFlagsConnectionRequired)) != 0
        return (isReachable && !needsConnection)
    }
}
{% endhighlight %}

在任何需要使用的地方，按如下方式调用：
{% highlight swift %}
if Reachability.isConnectedToNetwork(){
    print("network connection ok ...")
}
else{
    print("network connection failed ...")
}
{% endhighlight %}

> PS：适用于 Swift 版本 2.0