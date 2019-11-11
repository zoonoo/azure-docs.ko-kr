---
title: Azure Portal 앱 등록 교육 가이드 (레거시)-Azure
description: Microsoft id 플랫폼의 새로운 응용 프로그램 등록 환경에 대해 소개 합니다.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbdb6a2e17ab867b4938e94ae5a20a95cc352daa
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905395"
---
# <a name="training-guide-app-registrations-in-the-azure-portal-legacy"></a>학습 가이드: Azure Portal 앱 등록 (레거시)

Azure Portal에서 새로운 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 다양 한 기능 향상을 찾을 수 있습니다. 레거시 환경에 대해 잘 알고 있는 경우이 교육 가이드를 사용 하 여 새 환경 사용을 시작 해 보세요.

Azure Active Directory에서 여기에 설명 된 새 응용 프로그램 등록 환경은 일반적으로 사용 가능 (GA)입니다. Azure Active Directory B2C (Azure AD B2C)에서이 환경은 미리 보기로 제공 됩니다.

## <a name="key-changes"></a>주요 변경 내용

- 앱 등록는 **웹 앱/** a p i 또는 **네이티브** 앱으로 제한 되지 않습니다. 각각의 리디렉션 Uri를 등록 하 여 이러한 모든에 대해 동일한 앱 등록을 사용할 수 있습니다.
- 레거시 환경은 조직 (Azure AD) 계정에만 로그인 하는 앱을 지원 합니다. 앱은 단일 테 넌 트로 등록 되었으며 (앱이 등록 된 디렉터리에서 조직 계정만 지원), 다중 테 넌 트 (모든 조직 계정 지원)로 수정할 수 있습니다. 새 환경을 사용 하 여 이러한 옵션을 모두 지원할 수 있는 앱을 등록 하 고, 세 번째 옵션인 모든 조직 계정 및 개인 Microsoft 계정을 등록할 수 있습니다.
- 레거시 환경은 조직 계정을 사용 하 여 Azure Portal에 로그인 한 경우에만 사용할 수 있었습니다. 새 환경에서는 디렉터리와 연결 되지 않은 개인 Microsoft 계정을 사용할 수 있습니다.

## <a name="list-of-applications"></a>애플리케이션 목록

- 새 앱 목록에는 응용 프로그램 등록 포털 (Azure AD 계정에 로그인 하는 앱) 및 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/) (로그인 앱)에서 등록 된 앱 뿐만 아니라 Azure Portal의 레거시 앱 등록 환경을 통해 등록 된 응용 프로그램이 표시 됩니다. Azure AD 및 개인 Microsoft 계정).
- 새 앱 목록에는 **응용 프로그램 유형** 열이 없으며 (단일 앱 등록은 여러 유형이 될 수 있으므로) 열 **에 생성** 된와 상태를 표시 하는 **인증서 & 비밀** 열을 포함 하는 두 개의 추가 열이 있습니다. 앱에 등록 된 자격 증명의 곧 만료 되거나 만료 됨).

## <a name="new-app-registration"></a>새 앱 등록

레거시 환경에서는 **이름**, **응용 프로그램 유형**및 **로그온 URL/리디렉션 URI**를 제공 해야 하는 앱을 등록 합니다. 만들어진 앱은 Azure AD 전용 단일 테 넌 트 응용 프로그램 이므로 앱이 등록 된 디렉터리에서 조직 계정만 지원 합니다.

새 환경에서는 앱의 **이름을** 제공 하 고 **지원 되는 계정 유형을**선택 해야 합니다. 선택적으로 **리디렉션 URI**를 제공할 수 있습니다. 리디렉션 URI를 제공 하는 경우 웹/공용 (모바일 및 데스크톱) 인지를 지정 해야 합니다. 새 앱 등록 환경을 사용 하 여 앱을 등록 하는 방법에 대 한 자세한 내용은 [Microsoft id 플랫폼에 앱 등록](quickstart-register-app.md)을 참조 하세요. Azure AD B2C [Azure Active Directory B2C에서 응용 프로그램 등록](../../active-directory-b2c/tutorial-register-applications.md)을 참조 하세요.

## <a name="the-legacy-properties-page"></a>레거시 속성 페이지

레거시 환경에는 새 환경에 포함 되지 않은 **속성** 페이지가 있습니다. **속성** 블레이드에는 **이름**, **개체 id**, **응용 프로그램 id**, **앱 id URI**, **로고**, **홈 페이지 URL**, **로그 아웃 url**, **서비스 약관 URL**, **개인 정보 취급 방침 필드가 있습니다. URL**, **응용 프로그램 유형**및 **다중 테 넌 트가 있습니다.**

새 환경에서 동일한 기능을 찾을 수 있는 위치는 다음과 같습니다.

- **이름**, **로고**, **홈 페이지 url**, **서비스 약관 url**및 **개인정보 취급 방침 url** 은 이제 앱의 **브랜딩** 페이지에 있습니다.
- **개체 id** 및 **응용 프로그램 (클라이언트) id** 는 **개요** 페이지에 있습니다.
- 레거시 환경에서 **다중 테 넌 트** 에 의해 제어 되는 기능은 **인증** 페이지에서 **지원 되는 계정 유형** 으로 대체 되었습니다. 다중 테 넌 트가 지원 되는 계정 유형 옵션에 매핑되는 방법에 대 한 자세한 내용은 [이 빠른](quickstart-modify-supported-accounts.md)시작을 참조 하세요.
- **로그 아웃 URL** 은 이제 **인증** 페이지에 있습니다.
- **응용 프로그램 유형이** 더 이상 올바른 필드가 아닙니다. 대신, 리디렉션 Uri ( **인증** 페이지에서 찾을 수 있음)에 따라 지원 되는 앱 유형이 결정 됩니다.
- **앱 ID uri** 는 이제 **응용 프로그램 id URI** 라고 하며 **API 노출** 블레이드에서 찾을 수 있습니다. 레거시 환경에서이 속성은 `https://{tenantdomain}/{appID}` 형식 (예: `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`)을 사용 하 여 자동으로 등록 되었습니다. 새 환경에서는 `api://{appID}`으로 자동 생성 되지만 명시적으로 저장 해야 합니다. Azure AD B2C 테 넌 트에서 `https://{tenantdomain}/{appID}` 형식이 계속 사용 됩니다.

## <a name="reply-urlsredirect-urls"></a>회신 Url/리디렉션 Url

레거시 환경에서 앱에는 **회신 url** 페이지가 있습니다. 새 환경에서 회신 Url은 앱의 **인증** 섹션에서 찾을 수 있습니다. 또한 **리디렉션 uri**라고도 합니다. 또한 리디렉션 Uri의 형식이 변경 되었습니다. 앱 유형 (웹 또는 공용)에 연결 해야 합니다. 또한 보안상의 이유로 와일드 카드 및 http://체계는 지원 되지 않습니다 (http://localhost)를 제외 하 고는 지원 되지 않습니다.

## <a name="keyscertificates--secrets"></a>키/인증서 & 비밀

레거시 환경에서 앱에는 **키** 페이지가 있습니다. 새 환경에서는 **인증서 & 암호**로 이름이 변경 되었습니다. 또한 **공개 키** 를 **인증서** 라고 하며 **암호** 를 **클라이언트**암호 라고도 합니다.

## <a name="required-permissionsapi-permissions"></a>필요한 권한/API 권한

- 레거시 환경에서는 앱에 **필요한 사용 권한** 페이지가 있습니다. 새 환경에서는 **API 권한**으로 이름이 변경 되었습니다.
- 레거시 환경에서 API를 선택 하는 경우 작은 Microsoft Api 목록에서 선택 하거나 테 넌 트의 서비스 주체를 검색할 수 있습니다. 새 환경에서는 **Microsoft api**, **내 조직에서 사용 하는 Api**, **내 api**등 여러 탭 중에서 선택할 수 있습니다. Api의 검색 표시줄 **내 조직** 에서는 테 넌 트의 서비스 주체를 통해 탭 검색을 사용 합니다.

   > [!NOTE]
   > 응용 프로그램이 테 넌 트와 연결 되지 않은 경우이 탭이 표시 되지 않습니다. 새 환경을 사용 하 여 사용 권한을 요청 하는 방법에 대 한 자세한 내용은 [이 빠른](quickstart-configure-app-access-web-apis.md)시작을 참조 하세요.

- 레거시 환경에서는 **요청 된 사용 권한** 페이지의 맨 위에 **권한 부여** 단추가 있습니다. 새 환경에는 앱의 **API 사용 권한** 섹션에 **관리자 동의 부여** 단추가 있는 **grant 동의** 섹션이 있습니다. 또한 단추가 작동 하는 방식에는 몇 가지 차이점이 있습니다.
   - 레거시 환경에서 논리는 로그인 한 사용자 및 요청 된 권한에 따라 달라 집니다. 논리는 다음과 같습니다.
      - 사용자 동의 가능 권한만 요청 하 고 로그인 한 사용자가 관리자가 아닌 경우 사용자는 요청 된 권한에 대 한 사용자 동의를 부여할 수 있습니다.
      - 관리자 동의가 필요한 하나 이상의 사용 권한을 요청 하 고 로그인 한 사용자가 관리자가 아닌 경우 사용자에 게 동의를 부여 하려고 하면 오류가 발생 합니다.
      - 로그인 한 사용자가 관리자 인 경우 요청 된 모든 권한에 대해 관리자 동의가 부여 되었습니다.
   - 새 환경에서는 관리자만 동의를 허용할 수 있습니다. 관리자가 **관리자 동의 허용** 단추를 선택 하면 모든 요청 된 권한에 대해 관리자 동의가 부여 됩니다.

## <a name="deleting-an-app-registration"></a>앱 등록 삭제

레거시 환경에서 앱은 삭제 해야 하는 단일 테 넌 트 여야 합니다. 다중 테 넌 트 앱에 대해 삭제 단추를 사용할 수 없습니다. 새 환경에서는 모든 상태에서 앱을 삭제할 수 있지만 작업을 확인 해야 합니다. 앱 등록을 삭제 하는 방법에 대 한 자세한 내용은 [이 빠른](quickstart-remove-app.md)시작을 참조 하세요.

## <a name="application-manifest"></a>애플리케이션 매니페스트

레거시 및 새로운 환경은 매니페스트 편집기에서 JSON 형식에 대해 서로 다른 버전을 사용 합니다. 자세한 내용은 [애플리케이션 매니페스트](reference-app-manifest.md)를 참조하세요.

## <a name="new-ui"></a>새 UI

이전에 매니페스트 편집기나 API를 사용 하 여 설정 하거나 존재 하지 않는 속성에 대 한 새로운 UI가 있습니다.

- Oauth2AllowImplicitFlow ( **암시적 부여 흐름** )는 **인증** 페이지에서 찾을 수 있습니다. 레거시 환경과 달리 **액세스 토큰** 또는 **id 토큰**을 사용 하거나 둘 모두를 사용할 수 있습니다.
- API (oauth2Permissions) 및 **권한 있는 클라이언트 응용 프로그램** (preAuthorizedApplications) **에서 정의 된 범위** 는 **api 노출** 페이지를 통해 구성할 수 있습니다. 앱을 web API로 구성 하 고 사용 권한/범위를 노출 하는 방법에 대 한 자세한 내용은 [이 빠른](quickstart-configure-app-expose-web-apis.md)시작을 참조 하세요.
- [응용 프로그램의 동의 프롬프트](application-consent-experience.md)에서 사용자에 게 표시 되는 **게시자 도메인** 은 **브랜딩 블레이드** 페이지에서 찾을 수 있습니다. 게시자 도메인을 구성 하는 방법에 대 한 자세한 내용은 [이 방법을](howto-configure-publisher-domain.md)참조 하세요.

## <a name="limitations"></a>제한 사항

새 환경에는 다음과 같은 제한 사항이 있습니다.

- 클라이언트 암호 (앱 암호)의 형식은 레거시 환경과 다르며 CLI를 중단할 수 있습니다.
- 지원 되는 계정에 대 한 값 변경은 UI에서 지원 되지 않습니다. Azure AD 단일 테 넌 트와 다중 테 넌 트 간을 전환 하는 경우가 아니면 앱 매니페스트를 사용 해야 합니다.