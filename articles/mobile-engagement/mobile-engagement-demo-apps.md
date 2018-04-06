---
title: Azure Mobile Engagement 데모 앱 | Microsoft Docs
description: Azure Mobile Engagement 데모 앱 다운로드 위치, 사용 방법, 사용 시 이점에 대해 설명합니다
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: b74afcadc52973a4d824afddc0abff6ac9f71d17
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement 데모 앱
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

유용한 리소스를 찾고 Mobile Engagement에 대한 자세한 정보를 알아보는데 도움이 되도록 **iOS**, **Android** 및 **Windows** 플랫폼용 Azure Mobile Engagement 데모 앱을 게시했습니다.

앱을 통해 다음 작업이 가능합니다.

* 비디오, 문서, 지원 포럼, 기능 요청을 할 수 있는 위치 등 Mobile Engagement 리소스에 대한 유용한 링크를 쉽게 찾습니다.
* 모바일 응용 프로그램에 대한 아이디어를 얻기 위해 Mobile Engagement에서 지원하는 샘플 알림을 경험합니다.
* Mobile Engagement를 사용자 앱에 구현하는 방법을 알아보기 위한 참조 구현을 제공합니다. 다음을 배울 수 있습니다.
  
  * 분석 데이터 수집
  * *전체 화면 중간* 또는 *팝업*과 같은 형식의 고급 알림 시나리오 구현
  * 설문 조사 구현
  * 자동 푸시 데이터 및 푸시 시나리오 구현   

## <a name="app-installation"></a>앱 설치
이 앱은 다음과 같은 앱 스토어에서 이용할 수 있습니다.

* **Windows 유니버설 데모 앱**:
  
  * [Windows 앱 스토어](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2)에서 앱을 다운로드합니다.
  * 이 앱은 Windows 10 유니버설 앱으로 개발되었습니다. 소스 코드는 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows)에서 사용할 수 있습니다.
* **iOS 데모 앱**:
  
  * [Apple 스토어](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090)에서 앱을 다운로드합니다.
  * 이 앱은 iOS Swift에서 개발되었습니다. 소스 코드는 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios)에서 사용할 수 있습니다.
* **Android 데모 앱**:
  
  * [Google Play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)에서 앱을 다운로드합니다.
  * 소스 코드는 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)에서 사용할 수 있습니다.

![Windows 유니버설 데모 앱][1]

![iOS 데모 앱][2]
![Android 데모 앱][3]

## <a name="usage"></a>사용 현황
이 앱은 다음과 같은 방법으로 사용할 수 있습니다.

**위에 제공된 응용 프로그램 스토어 링크를 통해 장치에 앱을 다운로드합니다.**

> [!IMPORTANT]
> Azure 계정이 필요하지 않으며 앱을 백 엔드에 연결하지 않아도 됩니다. 앱이 독립적으로 작동합니다.
> 
> 

* 장치에 앱을 설치한 후 왼쪽 메뉴의 링크를 통해 Mobile Engagement에 대한 모든 유용한 리소스를 찾을 수 있습니다.
* 최신 제품 업데이트에 대한 알림을 항상 받아볼 수 있도록 [서비스의 RSS 피드](https://aka.ms/azmerssfeed) 를 이 응용 프로그램에 추가했습니다.
* 또한 샘플 알림 시나리오를 통해 Mobile Engagement에서 각 플랫폼에 대해 지원하는 알림 유형을 경험할 수 있습니다. 이러한 알림을 로컬에서 경험할 수 있습니다. 즉 화면의 단추를 클릭하여 Mobile Engagement 플랫폼에서 알림을 전송하는 경우와 동일한 알림 환경을 보여 줄 수 있습니다.

![Windows용 앱 메뉴][4]

![iOS용 앱 메뉴][5]
![Android용 앱 메뉴][6]

**위에 제공된 GitHub 링크에서 소스 코드를 다운로드합니다.**

* 소스 코드를 다운로드한 후 각 개발 환경에서 엽니다(iOS용 XCode, Android용 Android Studio, Windows용 Visual Studio).
* 그런 다음 이 앱을 해당 Mobile Engagement 백 엔드 인스턴스에 연결할 수 있도록 [기본 SDK 통합 단계](mobile-engagement-windows-store-dotnet-get-started.md) 를 따릅니다.
  * 앱에서 연결 문자열을 구성해야 합니다.
  * 또한 앱에 대한 푸시 알림 플랫폼을 구성해야 합니다.
* 앱 자체가 Mobile Engagement에서 계측되는 것을 확인할 수 있습니다. 따라서 백 엔드에 연결한 다음 앱을 열면 **모니터** 탭에 사용자 세션, 활동, 이벤트 등이 표시됩니다.
* 또한 로컬 알림을 사용하는 대신 Mobile Engagement 인스턴스에서 이 앱에 알림을 보낼 수 있습니다.
  
  * 여기에서 앱의 **장치 ID 가져오기** 메뉴를 사용하여 사용자 장치를 테스트 장치로 추가할 수 있습니다. 그러면 플랫폼 백 엔드 인스턴스를 사용하여 테스트 장치로 등록할 수 있는 장치 ID가 제공됩니다.
    
    ![Windows의 장치 ID][7]
    
    ![iOS의 장치 ID][8]
    ![Android의 장치 ID][9]

## <a name="key-features-of-the-demo-app"></a>데모 앱의 주요 기능
* 위에서 설명한 것처럼 이 앱에는 Mobile Engagement에 대한 모든 주요 리소스가 있습니다. 왼쪽 메뉴의 링크를 통해 이동할 수 있습니다.
* 각 플랫폼에 대한 앱 알림을 경험할 수 있습니다. 이러한 알림은 알림을 클릭하면 응용 프로그램의 네이티브 화면이 간단히 열리는 **알림 전용**(**딥 링크** 사용) 또는 알림이 클릭될 때 표시되는 Mobile Engagement 백 엔드에서 추가 HTML 콘텐츠를 전달할 수 있는 **웹 알림**으로 전달할 수 있습니다.
  
    ![앱 알림][29]
* iOS에서 앱 또는 시스템 푸시 알림을 확인하려면 앱을 닫아야 합니다. *피드백* 및 *공유*에 대해 이 앱 알림에 추가한 것과 같이 **실행 단추** 추가 화면이 구현되므로 사용자가 알림 자체에서 바로 작업을 할 수 있습니다.
  
    ![iOS의 앱 알림][11] ![iOS의 앱 알림 표시][14]
* Android에서 지원되는 옵션은 알림에 여러 줄 텍스트(**큰 텍스트**) 또는 알림 이미지(**큰 그림**)를 추가하거나 **실행 단추**(iOS에서 지원)를 추가하는 것입니다.
  
    ![Android의 앱 알림][12] ![Android의 앱 알림 표시][15]
* Windows 10에서 알림이 PC에 표시되는 방법을 볼 수 있습니다. 이 알림은 Windows 10 **알림 센터**에도 표시됩니다. 현재 Windows SDK에서는 **실행 단추** 를 추가할 수 없습니다.
  
    ![Windows의 앱 알림][10] ![Windows의 앱 표시][13]
* 각 플랫폼의 기본 "앱 내" 알림을 경험할 수 있습니다. **알림** 창이 먼저 표시되는 2단계 환경입니다. 이 창을 클릭하면 다음 스크린샷과 같은 전체 화면 **알림**이 열립니다. 이 알림의 내용은 Mobile Engagement 백 엔드 인스턴스의 내용입니다. SDK에는 두 가지 알림에 대한 템플릿이 있습니다. 이 템플릿은 이 데모 앱과 같이 로고와 색을 추가하여 쉽게 사용자 지정할 수 있습니다.  
  
    ![Windows의 앱 내 알림][16]
  
    ![iOS의 앱 내 알림][17]  ![Android의 앱 내 알림][18]
  
    **iOS**, **Android**
* Mobile Engagement의 **범주** 기능을 사용하여 이 기본 환경을 사용자 지정할 수도 있습니다. 이 데모 앱에서는 알림 환경을 변경하는 두 가지 일반적인 방법에 대해 알아보았습니다. 범주 기능은 아직 Windows SDK에서 지원되지 않습니다.
  
    **전체 화면 중간:**
  
    ![앱 내 알림 - 중간 범주][30]
  
    ![iOS의 중간 범주][21]     ![Android의 중간 범주][22]
  
    **팝업 알림:**
  
    ![앱 내 알림 - 팝업 범주][31]
  
    ![iOS의 팝업 알림][19]    ![Android의 팝업 알림][20]

**iOS**, **Android**

* Mobile Engagement는 **설문 조사**라는 전문 앱 내 알림도 지원합니다. 이 알림을 사용하여 분할된 앱 사용자에게 간단한 설문 조사를 보낼 수 있습니다. 다음 스크린샷과 같이 질문과 각 질문의 선택 항목을 추가할 수 있습니다. 그러면 앱 사용자에게 앱 내 알림으로 표시됩니다.   
  
    ![설문 조사 알림][32]
  
    ![Windows의 설문 조사][26]
  
    ![iOS의 설문 조사][27]   ![Android의 설문 조사][28]

**iOS**, **Android**

* Mobile Engagement는 자동 **데이터 푸시** 알림도 전송할 수 있습니다. 이러한 알림을 사용하면 서비스에서 데이터를 전송할 수 있으며(예: 다음 예제의 JSON 데이터) 앱에서 해당 데이터를 처리하고 몇 가지 작업을 할 수 있습니다. 예를 들어 데이터 푸시 알림을 사용하여 항목의 가격을 선택적으로 변경하는 방법이 있습니다.
  
    ![데이터 푸시 알림][33]
  
    ![Windows의 데이터 푸시 알림][23]
  
    ![iOS의 데이터 푸시 알림][24]  ![Android의 데이터 푸시 알림][25]

**iOS**, **Android**

> [!NOTE]
> 샘플 알림 화면에서 **Mobile Engagement 플랫폼에서 이러한 알림을 전송하는 방법에 대한 지침은 여기를 클릭하십시오.** 를 클릭하여 이러한 알림에 대한 자세한 단계별 지침을 볼 수 있습니다.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
