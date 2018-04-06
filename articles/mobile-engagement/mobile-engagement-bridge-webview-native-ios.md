---
title: iOS WebView와 네이티브 Mobile Engagement iOS SDK 브리지
description: Javascript를 실행하는 WebView와 네이티브 Mobile Engagement iOS SDK 간의 브리지를 만드는 방법을 설명합니다.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 07b2b8be80c090ce533f31cc28910f3ce7b91f73
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>iOS WebView와 네이티브 Mobile Engagement iOS SDK 브리지
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [Android 브리지](mobile-engagement-bridge-webview-native-android.md)
> * [iOS 브리지](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

일부 모바일 앱은 앱 자체가 네이티브 iOS Objective-C 개발을 사용하여 개발되지만 화면의 일부 또는 전부가 iOS WebView 내에서 렌더링되는 하이브리드 앱으로 설계됩니다. 이러한 앱 내에서 Mobile Engagement iOS SDK를 계속 사용할 수 있으며, 해당 작업 방법이 이 자습서에 설명되어 있습니다. 

다음 두 가지 접근 방식이 있지만 둘 다 문서화되지 않았습니다.

* 첫 번째 방식은 웹 보기에 `UIWebViewDelegate`를 등록하는 것과 관련된 이 [링크](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip)에 설명되어 있으며, Javascript에서 수행된 위치 변경을 catch하고 즉시 취소합니다. 
* 두 번째 방식은 첫 번째보다 명확한 접근 방식인 이 [WWDC 2013 세션](https://developer.apple.com/videos/play/wwdc2013/615)을 기반으로 합니다. 이 가이드에서는 이 방식을 따릅니다. 이 접근 방식은 iOS7 이상에서만 작동합니다. 

iOS 브리지 샘플에 대해 아래 단계를 따릅니다.

1. 먼저 [시작 자습서](mobile-engagement-ios-get-started.md) 에 따라 Mobile Engagement iOS SDK를 하이브리드 앱에 통합해야 합니다. 필요에 따라 WebView에서 트리거할 때 SDK 메서드를 볼 수 있도록 다음과 같이 테스트 로깅을 사용할 수도 있습니다. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. 이제 하이브리드 앱에 WebView가 표시된 화면이 있는지 확인합니다. 앱의 `Main.storyboard` 에 추가할 수 있습니다. 
3. 보기 컨트롤러 화면에서 WebView를 클릭하여 `ViewController.h` 편집 화면으로 끌어와 이 WebView를 **ViewController**와 연결합니다. `@interface` 줄 바로 아래에 두면 됩니다. 
4. 그러면 이름을 묻는 대화 상자가 나타납니다. **webView**로 이름을 입력합니다. `ViewController.h` 파일은 다음과 같아야 합니다.
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. 나중에 `ViewController.m` 파일을 업데이트할 것이지만 먼저 자주 사용되는 몇 가지 Mobile Engagement iOS SDK 메서드에서 래퍼를 만드는 브리지 파일을 만듭니다. 앞서 언급한 [세션](https://developer.apple.com/videos/play/wwdc2013/615)에 설명된 `JSExport` 메커니즘을 사용하는 **EngagementJsExports.h**라는 새 헤더 파일을 만들어 네이티브 iOS 메서드를 노출합니다. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. 그런 다음 브리지 파일의 두 번째 부분을 만듭니다. Mobile Engagement iOS SDK 메서드를 호출하여 실제 래퍼를 만드는 구현이 포함된 **EngagementJsExports.m** 이라는 파일을 만듭니다. WebView Javascript에서 전달되는 `extras`를 구문 분석하여 Engagement SDK 메서드 호출로 전달할 `NSMutableDictionary` 개체에 배치합니다.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. 이제 **ViewController.m** 으로 돌아가 다음 코드를 사용하여 업데이트합니다. 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. **ViewController.m** 파일에 대해 유의할 사항은 다음과 같습니다.
   
   * `loadWebView` 메서드에서 다음에 코드를 검토할 **LocalPage.html** 이라는 로컬 HTML 파일을 로드합니다. 
   * `webViewDidFinishLoad` 메서드에서 `JsContext`를 가져와 래퍼 클래스와 연결합니다. 그러면 WebView에서 **EngagementJs** 핸들을 사용하여 래퍼 SDK 메서드를 호출할 수 있습니다. 
9. 다음 코드를 사용하여 **LocalPage.html** 파일을 만듭니다.
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. 위 HTML 파일에 대해 유의할 사항은 다음과 같습니다.
    
    * 이 파일은 이벤트, 작업, 오류, 앱 정보의 이름으로 사용할 데이터를 제공할 수 있는 입력 상자 집합을 포함합니다. 입력 상자 옆의 단추를 클릭하면 이 호출을 Mobile Engagement iOS SDK로 전달하기 위해 브리지 파일에서 메서드를 호출하는 Javascript가 호출됩니다. 
    * 작업 방법을 보여 주기 위해 이벤트, 작업 및 오류의 몇 가지 정적 추가 정보에 대한 태그를 지정합니다. 이 추가 정보는 JSON 문자열로 전송됩니다. `EngagementJsExports.m` 파일에서 구문 분석되어 전송되는 이벤트, 작업 및 오류와 함께 전달됩니다. 
    * Mobile Engagement 작업은 입력 상자에 지정한 이름으로 시작되며, 10초간 실행된 후 종료됩니다. 
    * Mobile Engagement 앱 정보 또는 태그는 입력 상자에 태그 값으로 입력한 값 및 정적 키로 'customer_name'과 함께 전달됩니다. 
11. 앱을 실행하면 다음이 표시됩니다. 이제 다음과 같은 테스트 이벤트의 이름을 제공하고 옆에 있는 **Send** 를 클릭합니다. 
    
     ![][1]
12. 이제 앱의 **모니터링** 탭으로 이동하면 **이벤트 -> 세부 정보** 아래에 전송한 정적 앱 정보와 함께 이 이벤트가 표시됩니다. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
