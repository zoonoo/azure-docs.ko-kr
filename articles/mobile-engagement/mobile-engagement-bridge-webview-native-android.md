---
title: Android WebView와 네이티브 Mobile Engagement Android SDK 브리지
description: Javascript를 실행하는 WebView와 네이티브 Mobile Engagement Android SDK 간의 브리지를 만드는 방법을 설명합니다.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 717c0360042b8f2afd9c41f1ee97e64e95a76a2b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Android WebView와 네이티브 Mobile Engagement Android SDK 브리지
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [Android 브리지](mobile-engagement-bridge-webview-native-android.md)
> * [iOS 브리지](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

일부 모바일 앱은 앱 자체가 네이티브 Android 개발을 사용하여 개발되지만 화면의 일부 또는 전부가 Android WebView 내에서 렌더링되는 하이브리드 앱으로 설계됩니다. 이러한 앱 내에서 Mobile Engagement Android SDK를 계속 사용할 수 있으며, 해당 작업 방법이 이 자습서에 설명되어 있습니다. 아래 샘플 코드는 Android 설명서( [여기](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript))를 기반으로 합니다. Android 설명서에서는 하이브리드 앱의 Webview에서 Android SDK를 통해 파이프하는 동안 이벤트, 작업, 오류 및 앱 정보에 대한 추적 요청을 시작할 수 있도록 이 문서화된 접근 방식을 사용하여 Mobile Engagement Android SDK의 자주 사용되는 메서드에 대해 동일한 기능을 구현하는 방법을 설명합니다. 

1. 먼저 [시작 자습서](mobile-engagement-android-get-started.md) 에 따라 Mobile Engagement Android SDK를 하이브리드 앱에 통합해야 합니다. 그러면 `OnCreate` 메서드가 다음과 같이 표시됩니다.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. 이제 하이브리드 앱에 WebView가 표시된 화면이 있는지 확인합니다. 코드는 화면의 `onCreate` 메서드에서 로컬 HTML 파일 **Sample.html**을 Webview에 로드하는 다음 코드와 유사합니다. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. 이제 [Android 설명서](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)에 설명된 `@JavascriptInterface` 접근 방식을 사용하여 자주 사용되는 몇 가지 Mobile Engagement Android SDK 메서드에 래퍼를 만드는 **WebAppInterface**라는 브리지 파일을 만듭니다.
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. 위 브리지 파일을 만든 후에는 이 파일이 Webview와 연결되었는지 확인해야 합니다. 그러려면 `SetWebview` 메서드를 다음과 같이 편집해야 합니다.
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. 위 조각에서는 브리지 클래스를 Webview에 연결하기 위해 `addJavascriptInterface` 를 호출했으며, 브리지 파일에서 메서드를 호출하기 위해 **EngagementJs** 라는 핸들을 만들었습니다. 
6. 이제 **assets**이라는 폴더의 프로젝트에서 **Sample.html**이라는 파일을 만듭니다. Webview로 로드되는 이 브리지 파일에서 메서드를 호출합니다.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. 위 HTML 파일에 대해 유의할 사항은 다음과 같습니다.
   
   * 이 파일은 이벤트, 작업, 오류, 앱 정보의 이름으로 사용할 데이터를 제공할 수 있는 입력 상자 집합을 포함합니다. 입력 상자 옆의 단추를 클릭하면 이 호출을 Mobile Engagement Android SDK로 전달하기 위해 브리지 파일에서 메서드를 호출하는 Javascript가 호출됩니다. 
   * 작업 방법을 보여 주기 위해 이벤트, 작업 및 오류의 몇 가지 정적 추가 정보에 대한 태그를 지정합니다. 이 추가 정보는 JSON 문자열로 전송됩니다. `WebAppInterface` 파일에서 구문 분석되어 Android `Bundle`에 배치된 후 전송되는 이벤트, 작업 및 오류와 함께 전달됩니다. 
   * Mobile Engagement 작업은 입력 상자에 지정한 이름으로 시작되며, 10초간 실행된 후 종료됩니다. 
   * Mobile Engagement 앱 정보 또는 태그는 입력 상자에 태그 값으로 입력한 값 및 정적 키로 'customer_name'과 함께 전달됩니다. 
8. 앱을 실행하면 다음이 표시됩니다. 이제 다음과 같은 테스트 이벤트의 이름을 제공하고 아래의 **Send** 를 클릭합니다. 
   
    ![][1]
9. 이제 앱의 **모니터링** 탭으로 이동하면 **이벤트 -> 세부 정보** 아래에 전송한 정적 앱 정보와 함께 이 이벤트가 표시됩니다. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
