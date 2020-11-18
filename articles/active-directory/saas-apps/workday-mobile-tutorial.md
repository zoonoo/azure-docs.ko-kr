---
title: '자습서: Workday 모바일 애플리케이션과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 Workday 모바일 애플리케이션 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: e706649957bf427cd577d7995fb9ce104c687f4b
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378995"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>자습서: Workday 모바일 애플리케이션과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory), 조건부 액세스 및 Intune을 Workday Mobile Application과 통합하는 방법에 대해 알아봅니다. Microsoft와 Workday Mobile Application을 통합하면 다음을 수행할 수 있습니다.

* 로그인하기 전에 디바이스에서 정책을 준수하는지 확인합니다.
* 사용자가 회사 데이터에 안전하게 액세스할 수 있도록 Workday Mobile Application에 컨트롤을 추가합니다. 
* Azure AD에서 Workday에 액세스할 수 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Workday에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기:

* Workday와 Azure AD를 통합합니다.
* [Workday와 Azure Active Directory SSO(Single Sign-On) 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)을 읽어보세요.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 Workday Mobile Application을 사용하여 Azure AD 조건부 액세스 정책 및 Intune을 구성하고 테스트합니다.

SSO(Single Sign-On)를 사용하도록 설정하려면 Azure AD를 사용하여 Workday 페더레이션 애플리케이션을 구성하면 됩니다. 자세한 내용은 [Workday와 Azure Active Directory SSO(Single Sign-On) 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)을 참조하세요.

> [!NOTE] 
> Workday는 Intune의 앱 보호 정책을 지원하지 않습니다. 조건부 액세스를 사용하려면 모바일 디바이스 관리를 사용해야 합니다.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>사용자가 Workday Mobile Application에 액세스할 수 있도록 보장

모바일 앱에 대한 액세스를 허용하도록 Workday를 구성합니다. Workday Mobile에 대해 다음 정책을 구성해야 합니다.

1. 기능 영역에 대한 도메인 보안 정책 보고서에 액세스합니다.
1. 다음 중에서 적절한 보안 정책을 선택합니다.
    * 모바일 사용량 - Android
    * 모바일 사용량 - iPad
    * 모바일 사용량 - iPhone
1. **권한 편집** 을 선택합니다.
1. **보기 또는 수정** 확인란을 선택하여 보안 그룹에 보고서 또는 작업 보안 개체 항목에 대한 액세스 권한을 부여합니다.
1. **가져오기 또는 넣기** 확인란을 선택하여 보안 그룹에 통합 및 보고서 또는 작업 보안 개체 작업에 대한 액세스 권한을 부여합니다.

**보류 중인 보안 정책 변경 활성화** 를 실행하여 보류 중인 보안 정책 변경을 활성화합니다.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Workday Mobile 브라우저에서 Workday 로그인 페이지 열기

Workday Mobile Application에 조건부 액세스를 적용하려면 외부 브라우저에서 앱을 열어야 합니다. **테넌트 설정 편집 - 보안** 에서 **네이티브 앱에 모바일 브라우저 SSO 사용** 을 선택합니다. 이렇게 하려면 Intune 승인 브라우저를 iOS용 디바이스 및 Android용 회사 프로필에 설치해야 합니다.

![Workday Mobile 브라우저 로그인의 스크린샷](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>조건부 액세스 정책 설정

이 정책은 iOS 또는 Android 디바이스에서 수행하는 로그인에만 적용됩니다. 적용 범위를 모든 플랫폼으로 확장하려면 **모든 디바이스** 를 선택합니다. 이 정책을 적용하려면 디바이스가 정책을 준수해야 하며, Intune을 통해 준수 여부가 확인됩니다. Android에는 회사 프로필이 있으므로 사용자가 회사 프로필을 통해 로그인하고 Intune 회사 포털을 통해 앱을 설치하지 않은 이상 사용자가 Workday에 로그인할 수 없습니다. iOS에는 동일한 상황을 적용하기 위한 한 가지 추가 단계가 있습니다.

Workday는 다음과 같은 액세스 제어를 지원합니다.
* 다단계 인증 필요
* 디바이스를 준수 상태로 표시해야 함

Workday 앱은 다음을 지원하지 않습니다.
* 승인된 클라이언트 앱 필요
* 앱 보호 정책 필요(미리 보기)

Workday를 관리 디바이스로 설정하려면 다음 단계를 수행합니다.

![[관리 디바이스만] 및 [클라우드 앱 또는 작업]의 스크린샷](./media/workday-tutorial/managed-devices-only.png)

1. **홈** > **Microsoft Intune** > **조건부 액세스 정책** 을 선택합니다. 그런 다음, **관리 디바이스만** 을 선택합니다. 

1. **관리 디바이스만** 의 **이름** 에서 **관리 디바이스만** 을 선택한 다음, **클라우드 앱 또는 작업** 을 선택합니다.

1. **클라우드 앱 또는 작업**:

    a. **이 정책을 적용할 항목을 선택합니다** 를 **클라우드 앱** 으로 전환합니다.

    b. **포함** 에서 **앱 선택** 을 선택합니다.

    다. **선택** 목록에서 **Workday** 를 선택합니다.

    d. **완료** 를 선택합니다.

1. **정책 사용** 을 **켜기** 로 전환합니다.

1. **저장** 을 선택합니다.

액세스 **허용** 에 대해 다음 단계를 수행합니다.

![[관리 디바이스만] 및 [권한 부여]의 스크린샷](./media/workday-tutorial/managed-devices-only-2.png)

1. **홈** > **Microsoft Intune** > **조건부 액세스 정책** 을 선택합니다. 그런 다음, **관리 디바이스만** 을 선택합니다. 

1. **관리 디바이스만** 의 **이름** 에서 **관리 디바이스만** 을 선택합니다. **액세스 제어** 에서 **권한 부여** 를 선택합니다.

1. **권한 부여** 에서 다음을 수행합니다.

    a. **액세스 허용** 으로 적용할 제어를 선택합니다.

    b. **준수 상태로 표시된 디바이스 필요** 를 선택합니다.

    다. **선택된 제어 중 하나 필요** 를 선택합니다.

    d. **선택** 을 선택합니다.

1. **정책 사용** 을 **켜기** 로 전환합니다.

1. **저장** 을 선택합니다.

## <a name="set-up-device-compliance-policy"></a>디바이스 규정 준수 정책 설정

iOS 디바이스가 모바일 디바이스 관리를 통해 관리되는 Workday를 통해서만 로그인할 수 있도록 하려면 제한된 앱 목록에 **com.workday.workdayapp** 을 추가하여 App Store 앱을 차단해야 합니다. 이렇게 하면 회사 포털을 통해 Workday가 설치된 디바이스만 Workday에 액세스할 수 있습니다. 브라우저의 경우 디바이스가 Intune을 통해 관리되고 관리형 브라우저를 사용하는 경우에만 Workday에 액세스할 수 있습니다.

![iOS 디바이스 규정 준수 정책의 스크린샷](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Intune 앱 구성 정책 설정

| 시나리오 | 키 값 쌍 |
|----------------------------------------------------------------------------------------   |-----------|
| 다음에 대한 테넌트 및 웹 주소 필드를 자동으로 채웁니다.<br>● 회사 프로필을 Android에 사용하도록 설정하는 경우 Workday<br>● iPad 및 iPhone의 경우 Workday     | 다음 값을 사용하여 테넌트를 구성합니다. <br>● 구성 키 = `UserGroupCode`<br>● 값 형식 = 문자열 <br>● 구성 값 = 테넌트 이름. 예: `gms`<br>다음 값을 사용하여 웹 주소를 구성합니다.<br>● 구성 키 = `AppServiceHost`<br>● 값 형식 = 문자열<br>● 구성 값 = 테넌트에 대한 기준 URL. 예: `https://www.myworkday.com`                                |   |
| iPad 및 iPhone의 Workday에 대해 다음 작업을 사용하지 않도록 설정합니다.<br>● 잘라내기, 복사 및 붙여넣기<br>● 인쇄                       | 기능을 사용하지 않도록 설정하려면 이러한 키에 대한 값(부울)을 `False`로 설정합니다.<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| 스크린샷을 Android의 Workday에 사용하지 않도록 설정합니다. |기능을 사용하지 않도록 설정하려면 `AllowScreenshots` 키에 대한 값(부울)을 `False`로 설정합니다.|
| 사용자에게 제안된 업데이트를 사용하지 않도록 설정합니다.|기능을 사용하지 않도록 설정하려면 `AllowSuggestedUpdates` 키에 대한 값(부울)을 `False`로 설정합니다.|
|모바일 사용자를 선택한 앱 스토어로 보내도록 앱 스토어 URL을 사용자 지정합니다.|다음 값을 사용하여 앱 스토어 URL을 변경합니다.<br>● 구성 키 = `AppUpdateURL`<br>● 값 형식 = 문자열<br> ● 구성 값 = 앱 스토어 URL|
|       |


## <a name="ios-configuration-policies"></a>iOS 구성 정책

1. [Azure Portal](https://portal.azure.com/)로 이동하여 로그인 합니다.
1. **Intune** 을 검색하거나 목록에서 위젯을 선택합니다.
1. **클라이언트 앱** > **앱** > **앱 구성 정책** 으로 이동합니다. 그런 다음, **+ 추가** > **관리 디바이스** 를 선택합니다.
1. 이름을 입력합니다.
1. **플랫폼** 에서 **iOS/iPadOS** 를 선택합니다.
1. **연결된 앱** 에서 추가한 iOS 앱용 Workday를 선택합니다.
1. **구성 설정** 을 선택합니다. **구성 설정 형식** 에서 **XML 데이터 입력** 을 선택합니다.
1. 다음은 XML 파일 예제입니다. 적용할 구성을 추가합니다. `STRING_VALUE`를 사용하려는 문자열로 바꿉니다. `<true /> or <false />`를 `<true />` 또는 `<false />`로 바꿉니다. 구성을 추가하지 않으면 이 예제는 `True`로 설정된 것처럼 작동합니다.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. **추가** 를 선택합니다.
1. 페이지를 새로 고치고, 새로 만든 정책을 선택합니다.
1. **할당** 을 선택하고, 앱을 적용하려는 사람을 선택합니다.
1. **저장** 을 선택합니다.

## <a name="android-configuration-policies"></a>Android 구성 정책

1. [Azure Portal](https://portal.azure.com/)로 이동하여 로그인 합니다.
2. **Intune** 을 검색하거나 목록에서 위젯을 선택합니다.
3. **클라이언트 앱** > **앱** > **앱 구성 정책** 으로 이동합니다. 그런 다음, **+ 추가** > **관리 디바이스** 를 선택합니다.
5. 이름을 입력합니다. 
6. **플랫폼** 에서 **Android** 를 선택합니다.
7. **연결된 앱** 에서 추가한 Android 앱용 Workday를 선택합니다.
8. **구성 설정** 을 선택합니다. **구성 설정 형식** 에서 **JSON 데이터 입력** 을 선택합니다.

