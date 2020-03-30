---
title: 새로운 Azure Portal 앱 등록 학습 가이드
description: 새 Azure 포털 앱 등록 환경 소개
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154579"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>새로운 Azure Portal 앱 등록 학습 가이드

Azure 포털에서 새 앱 등록 환경에서 많은 개선 된 내용을 찾을 수 [있습니다.](https://go.microsoft.com/fwlink/?linkid=2083908) Azure 포털의 앱 등록(레거시) 환경을 잘 알고 있는 경우 이 교육 가이드를 사용하여 새 환경을 사용하기 를 사용합니다.

Azure Active Directory에서 여기에 설명된 새 응용 프로그램 등록 환경은 일반적으로 사용할 수 있습니다(GA). Azure Active Directory B2C(Azure AD B2C)에서는 이 환경이 미리 보기 상태입니다.

## <a name="key-changes"></a>주요 변경 사항

- 앱 등록은 *웹 앱/API* 또는 *네이티브* 앱으로 제한되지 않습니다. 각 리디렉션 URI를 등록하여 이러한 모든 앱에 대해 동일한 앱 등록을 사용할 수 있습니다.

- 레거시 환경은 조직(Azure AD) 계정만 사용하여 로그인하는 앱을 지원합니다. 앱이 단일 테넌트로 등록되었습니다. 앱은 앱이 등록된 디렉터리에서 조직 계정만 지원했습니다. 앱을 다중 테넌트로 수정하고 모든 조직 계정을 지원할 수 있습니다. 새로운 환경을 사용하면 이러한 옵션과 세 번째 옵션인 모든 조직 계정과 개인 Microsoft 계정을 모두 지원할 수 있는 앱을 등록할 수 있습니다.

- 레거시 환경은 조직 계정을 사용하여 Azure 포털에 로그인한 경우에만 사용할 수 있었습니다. 새 환경을 사용하면 디렉터리와 연결되지 않은 개인 Microsoft 계정을 사용할 수 있습니다.

## <a name="list-of-applications"></a>애플리케이션 목록

새 앱 목록에는 Azure 포털의 레거시 앱 등록 환경을 통해 등록된 응용 프로그램이 표시됩니다. 이러한 앱은 Azure AD 계정을 사용하여 로그인합니다. 새 앱 목록에는 응용 프로그램 등록 포털을 통해 등록된 앱도 표시됩니다. 이러한 앱은 Azure AD 및 개인 Microsoft 계정을 사용하여 로그인합니다.

>[!NOTE]
>응용 프로그램 등록 포털이 더 이상 사용되지 않았습니다.

단일 앱 등록이 여러 형식일 수 있으므로 새 앱 **목록에는 응용 프로그램 유형** 열이 없습니다. 목록에는 두 개의 추가 열이 있습니다: **생성됨** 및 **인증서 & 비밀.** **인증서 & 암호는** 앱에 등록된 자격 증명의 상태를 표시합니다. 상태에는 **현재**, **곧 만료됨**및 만료됨 이 **포함됩니다.**

## <a name="new-app-registration"></a>새 앱 등록

레거시 환경에서 제공해야 하는 앱을 등록하려면 **이름,** **응용 프로그램 유형**및 **로그온 URL/리디렉션 URI**. 생성된 앱은 Azure AD에서만 단일 테넌트 응용 프로그램이었습니다. 앱이 등록된 디렉터리에서 조직 계정만 지원했습니다.

새 환경에서는 앱의 **이름을** 제공하고 **지원되는 계정 유형을**선택해야 합니다. 선택적으로 **리디렉션 URI를**제공할 수 있습니다. 리디렉션 URI를 제공하는 경우 웹/공용(모바일 및 데스크톱)인지 여부를 지정해야 합니다. 자세한 내용은 [빠른 시작: Microsoft ID 플랫폼에 응용 프로그램 등록을](quickstart-register-app.md)참조하십시오. Azure AD B2C의 경우 [Azure Active Directory B2C에서 응용 프로그램 등록을](../../active-directory-b2c/tutorial-register-applications.md)참조하십시오.

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>응용 프로그램 등록 포털 과 앱 등록 페이지의 차이점

### <a name="the-legacy-properties-page"></a>레거시 속성 페이지

레거시 경험에는 **속성** 페이지가 있었습니다. **속성에는** 다음 필드가 있습니다.

- **이름**
- **개체 ID**
- **응용 프로그램 ID**
- **앱 ID URI**
- **로고**
- **홈페이지 URL**
- **로그아웃 URL**
- **서비스 약관 URL**
- **개인 정보 보호 정보 URL**
- **응용 프로그램 유형**
- **다중 테넌트**

새 경험에는 해당 페이지가 없습니다. 동등한 기능을 찾을 수 있는 곳은 다음과 같습니다.

- **이름,** **로고,** **홈 페이지 URL,** **서비스 약관 URL**및 개인 정보 보호 **명세서 URL은** 이제 앱의 **브랜딩** 페이지에 있습니다.
- **개체 ID** 및 **응용 프로그램(클라이언트) ID가** **개요** 페이지에 있습니다.
- 레거시 환경에서 **다중 테넌트** 토글에 의해 제어되는 기능이 **인증** 페이지에서 **지원되는 계정 유형으로** 대체되었습니다. 자세한 내용은 [빠른 시작: 응용 프로그램에서 지원하는 계정 수정을](quickstart-modify-supported-accounts.md)참조하십시오.
- **이제 로그아웃 URL이** **인증** 페이지에 있습니다.
- **응용 프로그램 유형이** 더 이상 유효한 필드가 아닙니다. 대신 **인증** 페이지에서 찾을 수 있는 URI를 리디렉션하여 지원되는 앱 유형을 결정합니다.
- **앱 ID URI는** 이제 **응용 프로그램 ID URI라고** 하며 **API 노출에서**찾을 수 있습니다. 레거시 환경에서 이 속성은 다음과 같은 형식을 `https://{tenantdomain}/{appID}` `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`사용하여 자동 등록되었습니다. 새 환경에서는 `api://{appID}`로 자동 생성되지만 명시적으로 저장해야 합니다. Azure AD B2C 테넌에서는 형식이 `https://{tenantdomain}/{appID}` 계속 사용됩니다.

### <a name="reply-urlsredirect-urls"></a>회신 URL/리디렉션 URls

레거시 환경에서 앱에는 **회신 URL** 페이지가 있었습니다. 새 환경에서는 앱의 **인증** 페이지에서 답장 URL을 찾을 수 있습니다. 이제 **리디렉션 URI라고**합니다.

리디렉션 URI의 형식이 변경되었습니다. 웹 또는 공개 앱 유형과 연결되어야 합니다. 보안상의 이유로 와일드카드 `http://` 및 스키마는 *http://localhost*을 제외하고 는 지원되지 않습니다.

### <a name="keyscertificates--secrets"></a>키/인증서 & 비밀

레거시 환경에서 앱에는 **키** 페이지가 있었습니다. 새로운 환경에서는 인증서 & 비밀로 이름이 **바뀌었습니다.**

**공개 키를** 이제 **인증서라고 합니다.** **이제 암호를** **클라이언트 암호라고**합니다.

### <a name="required-permissionsapi-permissions"></a>필수 권한/API 권한

레거시 환경에서 앱에는 필수 **권한 페이지가 있었습니다.** 새 환경에서는 **API 사용 권한으로**이름이 변경되었습니다.

레거시 환경에서 API를 선택한 경우 작은 Microsoft API 목록에서 선택할 수 있습니다. 테넌트의 서비스 주체를 통해 검색할 수도 있습니다. 새 환경에서는 여러 탭에서 선택할 수 있습니다: **Microsoft API,** **내 조직에서 사용하는 API**또는 내 **API**. **조직에서 API의** 검색 표시줄은 테넌트의 서비스 주체를 통해 탭 검색을 사용합니다.

> [!NOTE]
> 응용 프로그램이 테넌트와 연결되어 있지 않으면 이 탭이 표시되지 않습니다. 사용 권한을 요청하는 방법에 대한 자세한 내용은 [빠른 시작: 웹 API에 액세스하도록 클라이언트 응용 프로그램 구성을](quickstart-configure-app-access-web-apis.md)참조하십시오.

레거시 경험에는 요청된 **사용 권한** 페이지 상단에 권한 부여 권한 **버튼이 있습니다.** 새 환경에서 는 **권한 부여 동의** 페이지에 앱의 API 권한 섹션에 **부여 관리자 동의** **버튼이 있습니다.** 단추의 작동 방식에도 몇 가지 차이점이 있습니다.

레거시 환경에서 논리는 로그인한 사용자 및 요청되는 권한에 따라 다양합니다. 논리는 다음과 같은 것이었습니다.

- 사용자 동의 가능 권한만 요청되고 로그인한 사용자가 관리자가 아닌 경우 사용자는 요청된 권한에 대해 사용자 동의를 부여할 수 있습니다.
- 관리자 동의가 필요한 권한이 하나 이상 요청되고 로그인한 사용자가 관리자가 아닌 경우 사용자에게 동의를 요청할 때 오류가 발생했습니다.
- 로그인한 사용자가 관리자인 경우 요청된 모든 권한에 대해 관리자 동의가 부여되었습니다.

새 환경에서는 관리자만 동의를 부여할 수 있습니다. 관리자가 관리자 **동의를 부여를**선택하면 요청된 모든 권한에 관리자 동의가 부여됩니다.

## <a name="deleting-an-app-registration"></a>앱 등록 삭제

레거시 환경에서는 단일 테넌트 앱만 삭제할 수 있습니다. 다중 테넌트 앱의 경우 삭제 단추를 사용하지 않도록 설정했습니다. 새 환경에서는 모든 상태에서 앱을 삭제할 수 있지만 작업을 확인해야 합니다. 자세한 내용은 [빠른 시작: Microsoft ID 플랫폼에 등록된 응용 프로그램 제거를](quickstart-remove-app.md)참조하십시오.

## <a name="application-manifest"></a>애플리케이션 매니페스트.

레거시 및 새 환경은 매니페스트 편집기에서 JSON 형식에 대해 서로 다른 버전을 사용합니다. 자세한 내용은 [Azure Active Directory 앱 매니페스트를](reference-app-manifest.md)참조하십시오.

## <a name="new-ui"></a>새 UI

새 환경은 다음 속성에 대한 UI 컨트롤을 추가합니다.

- **인증** 페이지에 **암시적 권한 부여** 흐름()이`oauth2AllowImplicitFlow`있습니다. 레거시 환경과 달리 Access **토큰** 또는 ID 토큰 또는 둘 다를 활성화할 수 **있습니다.**
- **API 노출** 페이지에는 이 API ()`oauth2Permissions`및 권한 있는 클라이언트 응용 프로그램()에`preAuthorizedApplications` **의해 정의된 범위가** 포함되어 **있습니다.** 앱을 웹 API로 구성하고 권한/범위를 노출하는 방법에 대한 자세한 내용은 [빠른 시작: 웹 API를 노출하도록 응용 프로그램 구성을](quickstart-configure-app-expose-web-apis.md)참조하십시오.
- **브랜딩** 페이지에는 **게시자 도메인이**포함되어 있습니다. 게시자 도메인은 [응용 프로그램의 동의 프롬프트에](application-consent-experience.md)있는 사용자에게 표시됩니다. 자세한 내용은 [응용 프로그램의 게시자 도메인 구성 방법을 참조하세요.](howto-configure-publisher-domain.md)

## <a name="limitations"></a>제한 사항

새 경험에는 다음과 같은 제한 사항이 있습니다.

- 클라이언트 암호(앱 암호)의 형식은 레거시 환경의 형식과 다르며 CLI를 중단할 수 있습니다.
- 지원되는 계정에 대한 값을 변경하는 것은 UI에서 지원되지 않습니다. Azure AD 단일 테넌트와 다중 테넌트 간에 전환하지 않는 한 앱 매니페스트를 사용해야 합니다.
