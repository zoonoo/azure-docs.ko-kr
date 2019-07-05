---
title: 비 갤러리 응용 프로그램 암호 SSO를 구성 하는 문제 | Microsoft Docs
description: Azure AD 응용 프로그램 갤러리에 없는 사용자 지정 앱에 대해 암호 single sign-on (SSO) 구성 하는 경우 발생 하는 일반적인 문제입니다.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440345"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>암호 single sign-on 비 갤러리 응용 프로그램을 구성 하는 문제

이 문서에서는 구성한 경우 발생할 수 있는 일반적인 문제를 설명 *암호 single sign-on* (SSO)는 비 갤러리 응용 프로그램에 대 한 합니다.

## <a name="capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드 캡처

로그인 필드 캡처는 HTML 기반 로그인 페이지에 대해서만 지원 됩니다. 사용 하는 Adobe Flash 또는 기타 비 HTML 기반 기술을 비표준 로그인 페이지의 경우 등에 지원 되지 않습니다 했습니다.

두 가지 방법으로 사용자 지정 앱에 대 한 로그인 필드 캡처를 수 있습니다.

- **자동 로그인 필드 캡처** 대부분의 HTML 기반 로그인 페이지에서 잘 작동 *잘 알려진 DIV Id를 사용 하는 경우* 사용자 이름 및 암호 필드에 대 한 합니다. 페이지의 HTML 특정 조건과 일치 하는 DIV Id를 찾은 가져오고 됩니다. 메타 데이터는 나중에 앱에 재생 될 수 있도록 저장 됩니다.

- **수동 로그인 필드 캡처** 경우에 앱 공급 업체 *로그인 입력된 필드 레이블을 지정 하지 않습니다*합니다. 경우에 수동 캡처는 공급 업체 *자동으로 검색 될 수 없는 여러 필드를 렌더링*합니다. Azure Active Directory (Azure AD)에 로그인 페이지에서 이러한 필드는 페이지에 있는 지시 하면 경우 만큼 필드에 대 한 데이터를 저장할 수 있습니다.

자동 로그인 필드 캡처 경우 일반적으로 작동 하지 않습니다, 그리고 수동 옵션을 시도 합니다.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>자동으로 앱에 대 한 로그인 필드 캡처

자동 로그인 필드 캡처를 사용 하 여 암호 기반 SSO를 구성 하려면 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자 로그인

2. 왼쪽 탐색 창에서 선택 **모든 서비스** 를 Azure AD 확장을 엽니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 앱 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 원하는 앱이 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램** 목록. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Sso를 구성 하려는 앱을 선택 합니다.

7. 앱 로드 되 면 선택 **Single sign on** 왼쪽의 탐색 창에서.

8. 선택 **암호 기반 로그온** 모드입니다.

9. 입력 된 **로그온 URL**, 사용자가 자신의 사용자 이름 및 로그인 하는 데 암호를 입력 하는 여기서 페이지의 URL 인 합니다. *로그인 필드를 제공 하는 URL에 대 한 페이지에 표시 되는지 확인*합니다.

10. **저장**을 선택합니다.

    페이지는 사용자 이름 및 암호 입력된 상자에 대 한 자동으로 가져오고 됩니다. 액세스 패널 브라우저 확장을 사용 하 여 해당 앱에 암호를 안전 하 게 전송할 이제 Azure AD를 사용할 수 있습니다.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>수동으로 앱에 대 한 로그인 필드 캡처

로그인 필드를 수동으로 캡처하려면 액세스 패널 브라우저 확장이 설치 되어 있어야 합니다. 또한 브라우저를 실행할 수 없습니다 *inPrivate*하십시오 *incognito*, 또는 *개인* 모드.

확장을 설치 하려면 참조는 [액세스 패널 브라우저 확장 설치](#install-the-access-panel-browser-extension) 이 문서의 섹션입니다.

수동 로그인 필드 캡처를 사용 하 여 앱에 대 한 암호 기반 SSO를 구성 하려면 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자 로그인

2. 왼쪽 탐색 창에서 선택 **모든 서비스** 를 Azure AD 확장을 엽니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 앱 목록을 볼 수 있습니다.

   > [!NOTE] 
   > 사용 하 여 원하는 앱이 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램** 목록. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Sso를 구성 하려는 앱을 선택 합니다.

7. 앱 로드 되 면 선택 **Single sign on** 왼쪽의 탐색 창에서.

8. 선택 **암호 기반 로그온** 모드입니다.

9. 입력 된 **로그온 URL**는 사용자가 자신의 사용자 이름 및 암호 로그인을 입력할 페이지입니다. *로그인 필드를 제공 하는 URL에 대 한 페이지에 표시 되는지 확인*합니다.

10. 선택 **구성 *&lt;appname&gt;* 암호 Single sign-on 설정**합니다.

11. 선택 **로그인 필드 수동 검색**합니다.

14. **확인**을 선택합니다.

15. **저장**을 선택합니다.

16. 액세스 패널을 사용 하는 지침을 따릅니다.

## <a name="troubleshoot-problems"></a>문제 해결

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>오류가 발생 합니다 "해당 URL에서 로그인 필드를 찾을 수 없습니다."

로그인 필드 자동 검색이 실패할 때이 오류 메시지를 가져옵니다. 이 문제를 해결 하려면 수동 로그인 필드 검색을 시도 합니다. 참조 된 [수동으로 응용 프로그램에 대 한 로그인 필드 캡처](#manually-capture-sign-in-fields-for-an-app) 이 문서의 섹션입니다.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Single sign-on 구성을 저장할 수 없습니다"는 가져오는 오류

드물게가 실패 SSO 구성을 업데이트 합니다. 이 문제를 해결 하려면 구성을 다시 저장해 보세요.

오류가 발생 하 유지 지원 사례를 엽니다. 에 설명 된 정보를 포함 합니다 [포털 알림 세부 정보를 보려면](#view-portal-notification-details) 및 [도움말을 보려면 지원 엔지니어에 게 알림 세부 정보를 보낼](#send-notification-details-to-a-support-engineer-to-get-help) 이 문서의 섹션입니다.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>내 앱에 대 한 로그인 필드 수동으로 검색할 수 없음

수동 검색이 작동 하지 않는 경우에 다음 동작을 알 수 있습니다.

- 수동 캡처 프로세스가 작동 하는 것 이지만 올바르지 않은 캡처된 필드입니다.

- 캡처 프로세스를 실행할 때 올바른 필드를 강조 표시 되지 않음.

- 캡처 프로세스가 하면 앱의 로그인 페이지가 예상 대로 하지만 아무 일도 발생 합니다.

- 수동 캡처 작동 처럼 보이지만는 SSO 사용자가 액세스 패널에서 앱으로 이동 하는 경우에 발생 하지 않습니다.

이러한 문제가 발생 하면 다음 작업을 수행 합니다.

- 액세스 패널 브라우저 확장의 최신 버전이 있는지 *설치 및 활성화*합니다. 참조 된 [액세스 패널 브라우저 확장을 설치](#install-the-access-panel-browser-extension) 이 문서의 섹션입니다.

- 브라우저에 없는 했는지 *incognito*, *inPrivate*, 또는 *개인* 캡처 프로세스 중 모드입니다. 액세스 패널 확장은이 모드에서 지원 되지 않습니다.

- 사용자가 액세스 패널에서 앱에 로그인 하려고 하지 했는지 *incognito*를 *inPrivate*, 또는 *개인 모드*합니다.

- 수동 캡처 프로세스를 다시 시도 합니다. 빨간색 마커가 올바른 필드 위에 있는지 확인 합니다.

- 수동 캡처 프로세스가 응답 하지 않는 것 같습니다 또는 로그인 페이지를 응답 하지 않는 경우 수동 캡처 프로세스를 다시 시도 합니다. 하지만이 이번에는 프로세스를 완료 한 후 F12 키를 눌러 브라우저의 개발자 콘솔을 엽니다. 선택 된 **콘솔** 탭 합니다. 형식 **window.location = " *&lt;앱을 구성할 때 지정한 로그인 URL&gt;* "** , 한 다음 Enter를 누릅니다. 이렇게 하면 캡처 프로세스를 종료 하 고 캡처한 필드가 저장 하는 페이지 리디렉션이 됩니다.

### <a name="contact-support"></a>지원 문의

문제가 계속 되 면 Microsoft 지원 사례를 엽니다. 시도한 내용의 설명 합니다. 에 설명 된 세부 정보를 포함 합니다 [포털 알림 세부 정보를 보려면](#view-portal-notification-details) 및 [도움말을 보려면 지원 엔지니어에 게 알림 세부 정보를 보낼](#send-notification-details-to-a-support-engineer-to-get-help) 해당 하는 경우이 문서의 섹션.

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

다음 단계를 수행하세요.

1. 오픈 [액세스 패널](https://myapps.microsoft.com) 지원 되는 브라우저에서. Azure AD에 로그인을 *사용자*합니다.

2. 선택 **암호-SSO 응용 프로그램** 액세스 패널에서 합니다.

3. 소프트웨어를 설치 하 라는 메시지가 나타나면 선택 **지금 설치**합니다.

4. 브라우저에 대 한 다운로드 페이지로 이동 합니다. 하기로 **추가** 확장 합니다.

5. 메시지가 표시 되는 경우 선택 **을 사용 하도록 설정** 또는 **허용**합니다.

6. 설치 후 브라우저를 다시 시작 합니다.

7. 액세스 패널에 로그인 합니다. 참조 하는 경우에 암호-SSO가 설정 된 앱을 열 수 있습니다.

이러한 링크를 통해 Chrome 및 Firefox 브라우저 확장을 직접 다운로드할 수 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>포털 알림 세부 정보 보기

포털 알림의 세부 정보를 보려면 다음을 수행 합니다.

1. 선택 된 **알림을** Azure portal의 오른쪽 위 모퉁이에서 아이콘 (벨).

2. 알림을 보여 주는 선택는 *오류* 상태입니다. (빨간색 있는 "!".)

   > [!NOTE]
   > 에 있는 알림을 선택할 수 없습니다는 *성공한* 또는 *진행* 상태입니다.

3. **알림 세부 정보** 창이 열립니다. 문제에 대 한 자세한 정보를 읽습니다.

5. 여전히 도움이 필요한 경우 지원 엔지니어 또는 제품 그룹을 사용 하 여 정보를 공유 합니다. 선택는 **복사** 아이콘의 오른쪽에는 **오류 복사** 공유 하도록 알림 세부 정보를 복사 하는 상자입니다.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>도움말을 보려면 지원 엔지니어에 게 알림 세부 정보 보내기

공유 하는 것 *모든* 신속 하 게 지원할 수 있도록 지 원하는이 섹션에 나와 있는 세부 정보입니다. 을 기록 하려면 스크린샷을 찍 었으 하거나 선택할 수 있습니다 **오류 복사**합니다.

다음 설명 각 알림 항목이 의미 및 예제를 제공 합니다.

### <a name="essential-notification-items"></a>중요 알림 항목

- **제목**: 알림의 설명이 포함 된 제목입니다.

   예제: *응용 프로그램 프록시 설정*

- **설명**: 작업의 결과로 발생 한 문제입니다.

   예제: *입력 한 내부 URL은 다른 응용 프로그램에서 이미 사용 중입니다.*

- **알림 ID**: 알림의 고유 ID입니다.

    예: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **클라이언트 요청 ID**: 브라우저에서 만든 특정 요청 ID입니다.

    예제: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **타임 스탬프 UTC**: 타임 스탬프 UTC에서 알림이 발생 합니다.

    예제: *2017-03-23T19:50:43.7583681Z*

- **내부 트랜잭션 ID**: 시스템에서 오류를 찾는 데 사용 되는 내부 ID입니다.

    예제: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: 작업을 실행 한 사용자입니다.

    예: *tperkins\@f128.info*

- **테 넌 트 ID**: 작업을 실행 한 사용자의 구성원 인 테 넌 트의 고유 ID입니다.

    예제: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **사용자 개체 ID**: 작업을 실행 한 사용자의 고유 ID입니다.

    예제: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>자세한 알림 항목

- **표시 이름**: (비어 있을 수 있습니다) 오류에 대 한 더 자세한 표시 이름입니다.

    예제: *응용 프로그램 프록시 설정*

- **상태**: 알림의 특정 상태입니다.

    예제: *실패*

- **개체 ID**: (비어 있을 수 있습니다)는 작업이 실행 된 개체 ID입니다.

   예제: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **세부 정보**: 작업의 결과로 발생 한 문제에 대 한 자세한 설명은 합니다.

    예제: *내부 url '<https://bing.com/>' 이미 사용 중 이므로 잘못 되었습니다.*

- **오류 복사**: 선택할 수 있습니다 합니다 **복사 아이콘** 의 오른쪽에는 **오류 복사** 지원에 도움이 되도록 알림 세부 정보를 복사 하려면 텍스트 상자에 붙여넣습니다.

    예:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
