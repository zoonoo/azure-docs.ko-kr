---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133030"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Xamarin Studio에서 iOS 프로젝트 구성
1. Xamarin.Studio에서 **Info.plist**를 열고 앞에서 새 앱 ID로 만든 번들 ID를 사용하여 **Bundle 식별자**를 업데이트합니다.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. 아래의 **Background Modes**로 스크롤합니다. **Enable Background Modes** 상자와 **Remote notifications** 상자를 선택합니다.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Solution Panel에서 프로젝트를 두 번 클릭하여 **Project Options**를 엽니다.
4. **Build**에서 **iOS Bundle Signing**을 선택하고 방금 이 프로젝트에 대해 설정한 해당 ID 및 프로비전 프로필을 선택합니다.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   이제 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 공식 Xamarin 장치 프로비저닝 설명서를 보려면 [Xamarin 장치 프로비저닝]을 참조하세요.

#### <a name="configure-the-ios-project-in-visual-studio"></a>Visual Studio에서 iOS 프로젝트 구성
1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.
2. 속성 페이지에서 **iOS 응용 프로그램** 탭을 클릭하고 앞에서 만든 ID를 사용하여 **식별자**를 업데이트합니다.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. **iOS Bundle Signing** 탭에서 방금 이 프로젝트에 대해 설정한 해당 ID 및 프로비전 프로필을 선택합니다.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    이제 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 공식 Xamarin 장치 프로비저닝 설명서를 보려면 [Xamarin 장치 프로비저닝]을 참조하세요.
4. Info.plist를 두 번 클릭하여 연 다음 **Background Modes**에서 **RemoteNotifications**를 사용하도록 설정합니다.

[Xamarin 장치 프로비저닝]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
