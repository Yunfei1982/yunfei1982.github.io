---
layout: post
title:  "Swift 设置应用自动系统"
date:   2016-02-04 10:20:00 +0800
categories: Swift
---

做 OSX 桌面程序时，有时需要允许用户设置是否随系统启动而自动启动应用。

创建 StartupSetting.swift 文件，代码如下：
{% highlight swift %}
//
//  StartupSetting.swift
//
//  Created by SEVEN on 16/1/6.
//  Copyright © 2016年 Seven. All rights reserved.

import Cocoa
class StartupSetting {
    
    func applicationIsInStartUpItems() -> Bool {
        return (itemReferencesInLoginItems().existingReference != nil)
    }
    
    func itemReferencesInLoginItems() -> (existingReference: LSSharedFileListItemRef?, lastReference: LSSharedFileListItemRef?) {
        let itemUrl : UnsafeMutablePointer<Unmanaged<CFURL>?> = UnsafeMutablePointer<Unmanaged<CFURL>?>.alloc(1)
        if let appUrl : NSURL = NSURL.fileURLWithPath(NSBundle.mainBundle().bundlePath) {
            let loginItemsRef = LSSharedFileListCreate(
                nil,
                kLSSharedFileListSessionLoginItems.takeRetainedValue(),
                nil
                ).takeRetainedValue() as LSSharedFileListRef?
            if loginItemsRef != nil {
                let loginItems: NSArray = LSSharedFileListCopySnapshot(loginItemsRef, nil).takeRetainedValue() as NSArray
                print("There are \(loginItems.count) login items")
                let lastItemRef: LSSharedFileListItemRef = loginItems.lastObject as! LSSharedFileListItemRef
                for var i = 0; i < loginItems.count; ++i {
                    let currentItemRef: LSSharedFileListItemRef = loginItems.objectAtIndex(i) as! LSSharedFileListItemRef
                    if LSSharedFileListItemResolve(currentItemRef, 0, itemUrl, nil) == noErr {
                        if let urlRef: NSURL =  itemUrl.memory?.takeRetainedValue() {
                            print("URL Ref: \(urlRef.lastPathComponent)")
                            if urlRef.isEqual(appUrl) {
                                return (currentItemRef, lastItemRef)
                            }
                        }
                    } else {
                        print("Unknown login application")
                    }
                }
                //The application was not found in the startup list
                return (nil, lastItemRef)
            }
        }
        return (nil, nil)
    }
    
    func setLaunchAtStartup(let selected: Bool) -> Bool {
        let itemReferences = itemReferencesInLoginItems()
        let shouldBeToggled = (itemReferences.existingReference == nil)
        let loginItemsRef = LSSharedFileListCreate(
            nil,
            kLSSharedFileListSessionLoginItems.takeRetainedValue(),
            nil
            ).takeRetainedValue() as LSSharedFileListRef?
        if loginItemsRef != nil {
            if selected {
                if shouldBeToggled {
                    if let appUrl : CFURLRef = NSURL.fileURLWithPath(NSBundle.mainBundle().bundlePath) {
                        LSSharedFileListInsertItemURL(
                            loginItemsRef,
                            itemReferences.lastReference,
                            nil,
                            nil,
                            appUrl,
                            nil,
                            nil
                        )
                        print("Application was added to login items")
                    }
                }
            } else {
                if !shouldBeToggled {
                    if let itemRef = itemReferences.existingReference {
                        LSSharedFileListItemRemove(loginItemsRef,itemRef);
                        print("Application was removed from login items")
                    }
                }
            }
            return true
        }
        return false
    }
}
{% endhighlight %}

在任何需要使用的地方，按如下方式调用：
{% highlight swift %}
let startupSetting = StartupSetting()

if startupSetting.applicationIsInStartUpItems(){
    print("Application already exists in the startup list...")
}

if startupSetting.setLaunchAtStartup(true) {
    print("Set auto start success...")
}

if startupSetting.setLaunchAtStartup(false) {
    print("Cancel auto start success...")
}
{% endhighlight %}

> PS：适用于 Swift 版本 2.0