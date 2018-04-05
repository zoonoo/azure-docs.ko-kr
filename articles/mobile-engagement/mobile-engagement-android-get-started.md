---
title: Android 앱 Azure Mobile Engagement 시작
description: Android 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4eda8129e03e4880e168f7c154c7236da028e81e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Android 앱용 Azure Mobile Engagement 시작
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 


[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용을 이해하고 Android 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 해당 시나리오에서 기본 데이터를 수집하고 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 Android Studio 통합 개발 환경이 포함된 [Android 개발자 도구](https://developer.android.com/sdk/index.html)및 최신 Android 플랫폼이 필요합니다.

[Mobile Engagement Android SDK](https://aka.ms/vq9mfn)도 필요합니다.

> [!IMPORTANT]
> 이 자습서를 완료하려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)을 참조하세요.
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Android 앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Mobile Engagement 백 엔드에 앱 연결
이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 통합을 시연하기 위해 Android Studio로 기본 앱을 만듭니다.

전체 통합 설명서는 [Mobile Engagement Android SDK 통합](mobile-engagement-android-sdk-overview.md)에서 찾아볼 수 있습니다.

### <a name="create-an-android-project"></a>Android 프로젝트 만들기
1. **Android Studio**를 시작하고 팝업에서 **새 Android Studio 프로젝트 시작**을 선택합니다.

    ![][1]
2. 앱 이름 및 회사 도메인을 제공합니다. 나중에 필요하기 때문에 입력한 내용을 적어둡니다. **다음**을 클릭합니다.

    ![][2]
3. 대상 폼 팩터 및 API 수준을 선택하고 **다음**을 클릭합니다.

   > [!NOTE]
   > Mobile Engagement를 사용하려면 API 수준 10 이상(Android 2.3.3)이 필요합니다.
   >
   >

    ![][3]
4. 이 앱에 대한 유일한 화면인 **빈 활동**을 여기서 선택하고 **다음**을 클릭합니다.

    ![][4]
5. 마지막으로 기본값을 그대로 두고 **마침**을 클릭합니다.

    ![][5]

이제 Android Studio가 Mobile Engagement를 통합할 데모 앱을 만듭니다.

### <a name="include-the-sdk-library-in-your-project"></a>프로젝트에 SDK 라이브러리 포함
1. [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)를 다운로드합니다.
2. 보관 파일을 컴퓨터의 폴더로 추출합니다.
3. 이 SDK의 최신 버전에 대한 .jar 라이브러리를 확인하고 클립보드로 복사합니다.

      ![][6]
4. **Project** 섹션으로 이동하여(1) libs 폴더에 .jar을 붙여넣습니다(2).

      ![][7]
5. 라이브러리를 로드하려면 프로젝트를 동기화합니다.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>연결 문자열을 사용하여 Mobile Engagement 백 엔드에 앱 연결
1. 작업 생성에 다음 코드 줄을 복사합니다(응용 프로그램의 한 위치에서만 수행해야 하며, 일반적으로 기본 작업에서 수행함). 이 샘플 앱의 경우  src -> 기본 -> java 폴더에서 MainActivity를 열고 다음을 추가합니다.

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Alt + Enter를 누르거나 다음 import 문을 추가하여 참조를 확인합니다.

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. Azure Portal의 앱 **연결 정보** 페이지로 돌아가서 **연결 문자열**을 복사합니다.

      ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)

4. 이를 `setConnectionString` 매개 변수에 붙여 넣어 다음 코드에 표시된 전체 문자열을 대체합니다.

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>권한 및 서비스 선언 추가
1. 프로젝트의 Manifest.xml에서 `<application>` 태그 바로 앞에 다음 권한을 추가합니다.

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. 에이전트 서비스를 선언하려면 `<application>` 태그와 `</application>` 태그 사이에 이 코드를 추가합니다.

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. 붙여 넣은 코드에서 레이블의 `"<Your application name>"`을(를) 대체합니다. 레이블은 장치에서 실행 중인 서비스를 볼 수 있는 **설정** 메뉴에 표시됩니다. 예를 들어 해당 레이블에 "서비스"라는 단어를 추가할 수 있습니다.

### <a name="send-a-screen-to-mobile-engagement"></a>Mobile Engagement에 화면 보내기
데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다.

**MainActivity.java**로 이동하고 다음을 추가하여 **MainActivity**의 기본 클래스를 **EngagementActivity**로 바꿉니다.

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> 기본 클래스가 *활동*이 아닌 경우 [고급 Android 보고](mobile-engagement-android-advanced-reporting.md) 에서 다른 클래스에서 상속하는 방법을 참조하세요.
>
>

간단한 샘플 시나리오에 대해 다음 줄을 주석 처리합니다.

    // setSupportActionBar(toolbar);

앱에서 `ActionBar` 을(를) 유지하려는 경우 [고급 Android 보고](mobile-engagement-android-advanced-reporting.md)를 참조하세요.

## <a name="connect-app-with-real-time-monitoring"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>푸시 알림 및 앱 내 메시징 사용
캠페인 도중 Mobile Engagement에서는 사용자와 상호 작용하고 푸시 알림 및 앱 내 메시징을 통해 사용자에게 알릴 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
다음 섹션에서는 이를 수신하도록 앱을 설정합니다.

### <a name="copy-sdk-resources-in-your-project"></a>프로젝트에 SDK 리소스 복사
1. 다시 SDK 다운로드 콘텐츠로 돌아가서 **res** 폴더를 복사합니다.

    ![][10]
2. Android Studio로 돌아가서 프로젝트 파일의 **main** 디렉터리를 선택하고 붙여넣어 프로젝트에 리소스를 추가합니다.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>다음 단계
[Android SDK](mobile-engagement-android-sdk-overview.md) 로 돌아가 SDK 통합에 대한 자세한 정보를 확인합니다.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
