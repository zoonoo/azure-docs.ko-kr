---
title: "Cordova/Phonegap용 Azure Mobile Engagement 시작"
description: "Cordova/Phonegap 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Cordova/Phonegap용 Azure Mobile Engagement 시작
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 Cordova에서 개발된 모바일 응용 프로그램의 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다.

이 자습서에서는 Mac을 사용하여 빈 Cordova 앱을 만들고 Mobile Engagement SDK를 통합합니다. 기본 분석 데이터를 수집하고 iOS용 APNS(Apple 푸시 알림 시스템) 및 Android용 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받습니다. 테스트를 위해 iOS 또는 Android 장치에 이 앱을 배포합니다. 

> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started)을 참조하세요.
> 
> 

이 자습서를 사용하려면 다음이 필요합니다.

* Mac 앱 스토어에서 설치할 수 있는 Xcode(iOS 배포용)
* [Android SDK 및 에뮬레이터](http://developer.android.com/sdk/installing/index.html)(Android 배포용)
* APNS을 위해 Apple 개발자 센터에서 가져올 수 있는 푸시 알림 인증서(.p12)
* GCM을 위해 Google 개발자 콘솔에서 가져올 수 있는 GCM 프로젝트 번호
* [Mobile Engagement Cordova 플러그 인](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)에서 Cordova 플러그 인에 대한 소스 코드와 추가 정보를 찾을 수 있습니다.
> 
> 

## <a id="setup-azme"></a>Cordova 앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결
이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 

여기서는 통합을 시연하기 위해 Cordova를 사용하여 기본 앱을 만듭니다.

### <a name="create-a-new-cordova-project"></a>새 Cordova 프로젝트 만들기
1. Mac 컴퓨터에서 *Terminal* 창을 시작하고 기본 템플릿에서 새 Cordova 프로젝트를 만들 다음을 입력합니다. iOS 앱을 배포하는 데 사용할 최종 게시 프로필이 앱 ID로 'com.mycompany.myapp'을 사용하는지 확인합니다. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. 다음을 실행하여 **iOS** 에 대한 프로젝트를 구성하고 iOS 시뮬레이터에서 실행합니다.
   
        $ cordova platform add ios 
        $ cordova run ios
3. 다음을 실행하여 **Android**에 대한 프로젝트를 구성하고 Android 에뮬레이터에서 실행합니다. Android SDK 에뮬레이터 설정에서 대상이 Google API(Google Inc.)이고 CPU/ABI가 Google API ARM인지 확인합니다.  
   
        $ cordova platform add android
        $ cordova run android
4. Cordova 콘솔 플러그 인을 추가합니다. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Mobile Engagement 백 엔드에 앱 연결
1. 변수 값을 제공하여 플러그 인을 구성하는 동안 Azure Mobile Engagement Cordova 플러그 인을 설치합니다.
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android 도달률 아이콘* : 확장자 없고 그릴 수 있는 접두사도 없는 리소스 이름(예: mynotificationicon)이어야 하며 아이콘 파일을 사용자의 Android 프로젝트(platforms/android/res/drawable)에 복사해야 합니다.

*iOS 도달률 아이콘*: 확장자가 있는 리소스 이름(예: mynotificationicon.png)이어야 하며 XCode를 사용하여 아이콘 파일을 사용자의 iOS 프로젝트에 추가해야 합니다(파일 추가 메뉴 사용).

## <a id="monitor"></a>실시간 모니터링 사용
1. Cordova 프로젝트에서 **www/js/index.js** 를 편집하여 Mobile Engagement에 대한 호출을 추가하고 *deviceReady* 이벤트가 수신된 후 새 활동을 선언합니다.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. 응용 프로그램을 실행합니다.
   
   * **iOS의 경우**
     
       `Terminal` 창에서는 다음을 실행하여 앱을 새 시뮬레이터 인스턴스에서 시작합니다.
     
           cordova run ios
   * **Android의 경우**
     
       `Terminal` 창에서는 다음을 실행하여 앱을 새 에뮬레이터 인스턴스에서 시작합니다.
     
           cordova run android
3. 콘솔 로그에서 다음을 확인할 수 있습니다.
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용
Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용할 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
다음 섹션에서는 해당 알림과 메시지를 받도록 앱을 설정합니다.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Mobile Engagement에 대한 푸시 자격 증명 구성
사용자를 대신하여 Mobile Engagement에서 푸시 알림을 보내도록 허용하려면 Apple iOS 인증서 또는 GCM 서버 API 키에 대한 액세스 권한을 Mobile Engagement에 부여해야 합니다. 

1. Mobile Engagement 포털로 이동합니다. 이 프로젝트용으로 사용 중인 앱이 열려 있는지 확인하고 맨 아래에서 **참여** 단추를 클릭합니다.
   
    ![][1]
2. 그러면 Engagement 포털의 설정 페이지가 표시됩니다. 여기에서 다음과 같이 **네이티브 푸시** 섹션을 클릭합니다.
   
    ![][2]
3. iOS 인증서/GCM 서버 API 키 구성
   
    **[iOS]**
   
    a. 다음과 같이 .p12를 선택하여 업로드하고 암호를 입력합니다.
   
    ![][3]
   
    **[Android]**
   
    a. GCM 설정 섹션 및 표시된 팝업에서 **API 키** 앞의 편집 아이콘을 클릭하고 GCM 서버 키를 붙여넣고 **확인**을 클릭합니다. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Cordova 앱에서 푸시 알림을 사용하도록 설정
**www/js/index.js** 를 편집하여 푸시 알림을 요청하고 처리기를 선언하기 위한 Mobile Engagement에 대한 호출을 추가합니다.

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>앱 실행
**[iOS]**

1. iOS에서는 실제 장치에 대한 푸시 알림만 허용하므로 XCode를 사용하여 푸시 알림을 테스트할 장치에서 앱을 빌드하고 배포합니다. Cordova 프로젝트가 생성된 위치로 이동하고 **...\platforms\ios** 위치로 이동합니다. XCode에서 네이티브 .xcodeproj 파일을 엽니다. 
2. 방금 Mobile Engagement 포털에 업로드한 인증서가 포함된 프로비저닝 프로필이 있는 계정과 Cordova 앱을 만들 때 제공한 것과 일치하는 앱 ID를 사용하여 Cordova 앱을 빌드하고 iOS 장치에 배포합니다. XCode의 **Resources\*-info.plist** 파일에서 *번들 식별자*가 일치하는지 확인할 수 있습니다. 
3. 앱에서 알림을 보낼 권한을 요청함을 나타내는 표준 iOS 팝업이 장치에 표시됩니다. 권한을 부여합니다. 

**[Android]**

GCM 알림은 Android 에뮬레이터에서 지원되므로 에뮬레이터를 사용하여 Android 앱을 실행할 수 있습니다. 

    cordova run android

## <a id="send"></a>앱에 알림 보내기
이제 장치에서 실행 중인 앱에 푸시를 보낼 간단한 푸시 알림 캠페인을 만듭니다.

1. Mobile Engagement 포털에서 **도달률** 탭으로 이동합니다.
2. **새 알림** 을 클릭하여 푸시 캠페인을 만듭니다.
   
    ![][6]
3. 입력을 제공하여 캠페인을 만듭니다. **[Android]**
   
   * 캠페인에 **이름** 을 제공합니다. 
   * **전달 형식**을 *시스템 알림* *단순*으로 선택합니다.
   * **전달 시간** 을 *"언제든지"*
   * 푸시에서 첫째 줄에 있는 알림에 **제목** 을 제공합니다.
   * 메시지 본문으로 사용하는 알림에 **메시지** 를 제공합니다. 
     
     ![][11]
4. 입력을 제공하여 캠페인을 만듭니다. **[iOS]**
   
   * 캠페인에 **이름** 을 제공합니다. 
   * **전달 시간** 을*"앱 외부에서만"*
   * 푸시에서 첫째 줄에 있는 알림에 **제목** 을 제공합니다.
   * 메시지 본문으로 사용하는 알림에 **메시지** 를 제공합니다. 
     
     ![][12]
5. 아래로 스크롤하여 콘텐츠 섹션에서 **알림만**
   
    ![][8]
6. [선택 사항] 작업 URL을 제공할 수도 있습니다. 플러그 인의 **AZME\_REDIRECT\_URL** 변수를 구성하는 동안 제공된 URL 체계가 사용되는지 확인합니다(예: *myapp://test*).  
7. 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 이제 다시 아래로 스크롤하고 **만들기** 단추를 클릭하여 캠페인을 저장합니다.
8. 마지막으로 캠페인을 **활성화** 합니다.
   
    ![][10]
9. 이제 이 캠페인 부분으로 장치나 에뮬레이터에 푸시 알림이 표시되어야 합니다. 

## <a id="next-steps"></a>다음 단계
[Cordova Mobile Engagement SDK에서 사용 가능한 모든 방법의 개요](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


