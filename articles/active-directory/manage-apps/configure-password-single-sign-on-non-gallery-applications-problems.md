---
title: 갤러리가 아닌 앱에 대한 암호 SSO 를 구성하는 문제
description: Azure AD 응용 프로그램 갤러리에 없는 사용자 지정 앱에 대해 SSO(암호 단일 사인온)를 구성할 때 발생하는 일반적인 문제입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274143"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>갤러리가 아닌 응용 프로그램에 대한 암호 단일 사인온을 구성하는 문제

이 문서에서는 갤러리가 아닌 앱에 대해 *SSO(암호 단일 사인온)를* 구성할 때 발생할 수 있는 일반적인 문제에 대해 설명합니다.

## <a name="capture-sign-in-fields-for-an-app"></a>앱의 로그인 필드 캡처

로그인 필드 캡처는 HTML 지원 로그인 페이지에만 지원됩니다. Adobe Flash 또는 기타 HTML 지원 이외 기술을 사용하는 것과 같이 비표준 로그인 페이지에는 지원되지 않습니다.

사용자 지정 앱에 대한 로그인 필드를 캡처하는 방법에는 두 가지가 있습니다.

- **자동 로그인 필드 캡처는** 사용자 이름 및 암호 필드에 잘 알려진 DIV ID를 사용하는 경우 대부분의 HTML 지원 로그인 페이지에서 잘 *작동합니다.* 페이지의 HTML은 특정 기준과 일치하는 DIV ID를 찾기 위해 스크랩됩니다. 해당 메타데이터는 나중에 앱으로 재생할 수 있도록 저장됩니다.

- **수동 로그인 필드 캡처는** 앱 공급업체에서 *로그인 입력 필드에 레이블을 지정하지 않는*경우에 사용됩니다. 수동 캡처는 공급업체에서 *자동 검색할 수 없는 여러 필드를 렌더링하는 경우에도 사용됩니다.* Azure Active Directory(Azure AD)는 해당 필드가 페이지에 있는 위치를 알려주면 로그인 페이지에 있는 만큼의 필드에 대한 데이터를 저장할 수 있습니다.

일반적으로 자동 로그인 필드 캡처가 작동하지 않으면 수동 옵션을 사용해 보십시오.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>앱의 로그인 필드 자동 캡처

자동 로그인 필드 캡처를 사용하여 암호 기반 SSO를 구성하려면 다음 단계를 따르십시오.

1. Azure [포털을](https://portal.azure.com/)엽니다. 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽탐색 창에서 Azure AD 확장을 열려면 **모든 서비스를** 선택합니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을** 사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. SSO에 대해 구성할 앱을 선택합니다.

7. 앱이 로드된 후 왼쪽탐색 창에서 **Single sign-on을** 선택합니다.

8. **암호 기반 사인온** 모드를 선택합니다.

9. 로그인할 사용자 이름과 암호를 입력하는 페이지의 URL인 **사인온 URL을**입력합니다. *에서 제공하는 URL의 페이지에 로그인 필드가 표시되는지 확인합니다.*

10. **저장**을 선택합니다.

    사용자 이름 및 암호 입력 상자에 대해 페이지가 자동으로 스크레이핑됩니다. 이제 Azure AD를 사용하여 액세스 패널 브라우저 확장을 사용하여 해당 앱에 암호를 안전하게 전송할 수 있습니다.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>앱의 로그인 필드를 수동으로 캡처

로그인 필드를 수동으로 캡처하려면 액세스 패널 브라우저 확장이 설치되어 있어야 합니다. 또한 브라우저에서 실행할 수 *없습니다개인,* *시크릿,* 또는 *개인* 모드.

확장을 설치하려면 이 문서의 [액세스 패널 브라우저 확장 설치](#install-the-access-panel-browser-extension) 섹션을 참조하십시오.

수동 로그인 필드 캡처를 사용하여 앱에 대한 암호 기반 SSO를 구성하려면 다음 단계를 따르세요.

1. Azure [포털을](https://portal.azure.com/)엽니다. 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽탐색 창에서 Azure AD 확장을 열려면 **모든 서비스를** 선택합니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 앱 목록을 봅니다.

   > [!NOTE] 
   > 원하는 앱이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을** 사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. SSO에 대해 구성할 앱을 선택합니다.

7. 앱이 로드된 후 왼쪽탐색 창에서 **Single sign-on을** 선택합니다.

8. **암호 기반 사인온** 모드를 선택합니다.

9. 로그인할 사용자 이름과 암호를 입력하는 페이지인 **사인온 URL을**입력합니다. *에서 제공하는 URL의 페이지에 로그인 필드가 표시되는지 확인합니다.*

10. **앱 * &lt;이름&gt; * 암호 단일 사인온 설정 구성을**선택합니다.

11. **로그인 필드를 수동으로 검색합니다.**

14. **확인을 선택합니다.**

15. **저장**을 선택합니다.

16. 지침에 따라 액세스 패널을 사용합니다.

## <a name="troubleshoot-problems"></a>문제 해결

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"해당 URL에서 로그인 필드를 찾을 수 없습니다." 오류가 발생합니다.

로그인 필드의 자동 검색에 실패하면 이 오류 메시지가 나타납니다. 이 문제를 해결하려면 수동 로그인 필드 검색을 시도합니다. 이 문서의 [응용 프로그램 섹션은 수동으로 캡처 로그인 필드를](#manually-capture-sign-in-fields-for-an-app) 참조하십시오.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"단일 사인온 구성을 저장할 수 없음" 오류가 발생합니다.

드물게 SSO 구성을 업데이트하지 못합니다. 이 문제를 해결하려면 구성을 다시 저장해 보십시오.

계속 오류가 발생하면 지원 사례를 엽니다. [포털 알림 세부 정보 View에](#view-portal-notification-details) 설명된 정보를 포함하고 지원 엔지니어에게 알림 세부 정보를 보내 이 문서의 도움말 섹션을 [가져옵니다.](#send-notification-details-to-a-support-engineer-to-get-help)

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>앱의 로그인 필드를 수동으로 검색할 수 없습니다.

수동 검색이 작동하지 않을 때 다음 동작을 관찰할 수 있습니다.

- 수동 캡처 프로세스가 작동하는 것처럼 보였지만 캡처된 필드가 올바르지 않습니다.

- 캡처 프로세스가 실행될 때 올바른 필드가 강조 표시되지 않습니다.

- 캡처 프로세스는 예상대로 앱의 로그인 페이지로 이동하지만 아무 일도 일어나지 않습니다.

- 수동 캡처가 작동하는 것처럼 보이지만 사용자가 액세스 패널에서 앱으로 이동할 때 SSO는 발생하지 않습니다.

이러한 문제가 발생하면 다음 작업을 수행합니다.

- 최신 버전의 액세스 패널 브라우저 확장이 *설치되어 있고 활성화되어*있는지 확인합니다. 이 문서의 [액세스 패널 브라우저 확장 설치](#install-the-access-panel-browser-extension) 섹션을 참조하십시오.

- 캡처 프로세스 중에 브라우저가 *시크릿,* *inPrivate*또는 *비공개* 모드에 있지 않은지 확인합니다. 액세스 패널 확장은 이러한 모드에서 지원되지 않습니다.

- 사용자가 *시크릿*, *inPrivate*또는 *비공개 모드에서*액세스 패널에서 앱에 로그인하려고 하지 않는지 확인합니다.

- 수동 캡처 프로세스를 다시 시도합니다. 빨간색 마커가 올바른 필드 위에 있는지 확인합니다.

- 수동 캡처 프로세스가 응답을 중지하거나 로그인 페이지가 응답하지 않는 경우 수동 캡처 프로세스를 다시 시도하십시오. 그러나 이번에는 프로세스를 완료한 후 F12 키를 눌러 브라우저의 개발자 콘솔을 엽니다. **콘솔** 탭을 선택합니다. ***&lt;&gt;*** 이렇게 하면 캡처 프로세스가 종료되고 캡처된 필드가 저장되는 페이지 리디렉션이 강제로 이루어집니다.

### <a name="contact-support"></a>지원에 문의

그래도 문제가 있는 경우 Microsoft 지원 사례를 엽니다. 시도한 내용을 설명합니다. 포털 알림 세부 정보 [View에](#view-portal-notification-details) 설명된 세부 정보를 포함하고 지원 [엔지니어에게 알림 세부 정보를 보내](#send-notification-details-to-a-support-engineer-to-get-help) 이 문서의 도움말 섹션을 가져옵니다(해당하는 경우).

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

다음 단계를 수행하세요.

1. 지원되는 브라우저에서 [액세스 패널을](https://myapps.microsoft.com) 엽니다. *사용자로*Azure AD에 로그인합니다.

2. 액세스 패널에서 **암호-SSO 응용 프로그램을** 선택합니다.

3. 소프트웨어를 설치하라는 메시지가 표시되면 **지금 설치를**선택합니다.

4. 브라우저의 다운로드 페이지로 이동합니다. 확장 **추가를** 선택합니다.

5. 메시지가 표시되면 **사용** 또는 **허용을**선택합니다.

6. 설치 후 브라우저를 다시 시작합니다.

7. 액세스 패널에 로그인합니다. 암호 SSO 지원 앱을 열 수 있는지 확인합니다.

또한 이러한 링크를 통해 크롬과 파이어 폭스에 대 한 브라우저 확장 프로그램을 직접 다운로드할 수 있습니다.

-   [크롬 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [파이어 폭스 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>포털 알림 세부 정보 보기

포털 알림의 세부 정보를 보려면 다음 단계를 따르십시오.

1. Azure 포털의 오른쪽 위 모서리에서 **알림** 아이콘(종)을 선택합니다.

2. *오류* 상태를 표시하는 알림을 선택합니다. (그들은 빨간색 "!".

   > [!NOTE]
   > *성공* 또는 *진행 률* 상태인 알림은 선택할 수 없습니다.

3. **알림 세부 정보** 창이 열립니다. 문제에 대해 알아보려면 정보를 읽으십시오.

5. 그래도 도움이 필요한 경우 지원 엔지니어 또는 제품 그룹과 정보를 공유하십시오. 복사 **오류** 상자 오른쪽에 있는 **복사** 아이콘을 선택하여 공유할 알림 세부 정보를 복사합니다.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>지원 엔지니어에게 알림 세부 정보를 보내 도움을 받으십시오.

이 섹션에 나열된 *모든* 세부 정보를 지원과 공유하여 신속하게 도움을 줄 수 있도록 하는 것이 중요합니다. 그것을 기록하려면 스크린 샷을 찍거나 **복사 오류를**선택할 수 있습니다.

다음 정보는 각 알림 항목의 의미를 설명하고 예제를 제공합니다.

### <a name="essential-notification-items"></a>필수 알림 항목

- **제목**: 알림의 설명 제목입니다.

   예: *응용 프로그램 프록시 설정*

- **설명**: 작업의 결과로 발생한 내용입니다.

   예: *입력된 내부 URL이 이미 다른 응용 프로그램에서 사용되고 있습니다.*

- 알림 ID : **알림의**고유 ID입니다.

    예: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **클라이언트 요청 ID**: 브라우저가 만든 특정 요청 ID입니다.

    예: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **타임스탬프 UTC**: UTC에서 알림이 발생한 시점의 타임스탬프입니다.

    예: *2017-03-23T19:50:43.7583681Z*

- **내부 거래 ID**: 시스템의 오류를 조회하는 데 사용되는 내부 ID입니다.

    예: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: 작업을 실행한 사용자입니다.

    예: *f128.info\@*

- **테넌트 ID**: 작업을 실행한 사용자가 멤버인 테넌트의 고유 ID입니다.

    예: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **사용자 개체 ID**: 작업을 실행한 사용자의 고유 ID입니다.

    예: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>자세한 알림 항목

- **표시 이름**: 오류에 대한 자세한 표시 이름(비어 있을 수 있음)입니다.

    예: *응용 프로그램 프록시 설정*

- **상태**: 알림의 특정 상태입니다.

    예: *실패*

- **개체 ID**: 작업이 실행된 개체 ID(비어 있을 수 있음).

   예: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **세부 사항**: 작업의 결과로 발생한 작업에 대한 자세한 설명입니다.

    예: *내부<https://bing.com/>URL ''은 이미 사용 중이므로 유효하지 않습니다.*

- **복사 오류**: **복사 오류** 텍스트 상자의 오른쪽에있는 **복사 아이콘을** 선택하여 지원에 도움이되는 알림 세부 정보를 복사 할 수 있습니다.

    예제:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
