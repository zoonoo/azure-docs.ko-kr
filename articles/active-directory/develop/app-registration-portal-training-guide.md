---
title: 새로운 Azure Portal 앱 등록 환경
titleSuffix: Microsoft identity platform
description: Azure 포털의 새 앱 등록 환경 소개
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154596"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>새 Azure 포털 앱 등록 환경

Azure 포털의 새 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 많이 개선되었습니다. 이전 환경이라고 하는 컨버지드 응용 프로그램을 등록하거나 관리하는 응용 프로그램 등록 포털(apps.dev.microsoft.com) 환경을 더 잘 알고 있는 경우 이 교육 가이드에서 새 환경을 사용할 수 있습니다.

## <a name="whats-not-changing"></a>변경되지 않는 것은 무엇입니까?

- 응용 프로그램 및 관련 구성은 새 환경에서 있는 것처럼 찾을 수 있습니다. 응용 프로그램을 다시 등록할 필요가 없으며 응용 프로그램 사용자가 다시 로그인할 필요가 없습니다.

    > [!NOTE]
    > Azure 포털에서 응용 프로그램을 찾기 위해 응용 프로그램을 등록하는 데 사용한 계정으로 로그인해야 합니다. Azure Portal에서 로그인한 사용자가 프로필의 전자 메일 주소를 비교하여 응용 프로그램 등록 포털에 로그인한 사용자와 일치하는지 확인하는 것이 좋습니다.
    >
    > 경우에 따라 특히 Azure AD 전자 메일 주소로 개인 Microsoft 계정(예: Outlook, Live, Xbox 등)을 사용하여 로그인할 때 이전 환경에서 Azure 포털로 이동하면 동일한 계정으로 다른 계정으로 로그인한다는 것을 알게 되었습니다. Azure AD 테넌트의 전자 메일입니다. 여전히 응용 프로그램이 누락된 것으로 생각되면 로그아웃하고 올바른 계정으로 로그인하세요.

- 개인 Microsoft 계정을 사용하여 만든 라이브 SDK 앱은 Azure 포털에서 아직 지원되지 않으며 가까운 장래에 이전 환경에서 계속 유지됩니다.

## <a name="key-changes"></a>주요 변경 사항

-   이전 환경에서 앱은 기본적으로 개인 Microsoft 계정뿐만 아니라 모든 조직 계정(다중 테넌트)을 지원하는 컨버지드 앱으로 등록되었습니다. 이전 환경을 통해 수정할 수 없습니다.
    새로운 환경을 사용하면 이러한 모든 옵션을 지원하는 앱을 등록할 수 있습니다. [앱 유형에 대해 자세히 알아보세요.](active-directory-v2-registration-portal.md)

-   새 환경에서 개인 Microsoft 계정이 Azure AD 테넌트에 있는 경우 테넌트의 모든 응용 프로그램, 테넌트의 소유 응용 프로그램 및 개인 계정의 응용 프로그램 등 세 개의 탭이 표시됩니다. 따라서 개인 Microsoft 계정에 등록된 앱이 누락되었다고 생각되면 **개인 계정 탭에서 응용 프로그램을 확인합니다.**

-   새 환경에서는 프로필로 이동하여 스위치 디렉터리를 선택하여 테넌티 간을 쉽게 전환할 수 있습니다.

## <a name="list-of-applications"></a>애플리케이션 목록

-   새 앱 목록에는 Azure Portal(Azure AD 계정에만 로그인하는 앱)의 레거시 앱 등록 환경을 통해 등록된 응용 프로그램과 [응용 프로그램 등록 포털(Azure](https://apps.dev.microsoft.com/) AD 및 개인 Microsoft 계정 모두에 로그인하는 앱)을 통해 등록된 응용 프로그램이 표시됩니다.

-   새 앱 목록에는 두 개의 추가 열이 있습니다: **앱에** 등록된 자격 증명의 상태(현재, 만료 또는 만료됨)를 표시하는 열 및 **인증서 & 비밀** 열에 생성됨.

## <a name="new-app-registration"></a>새 앱 등록

이전 환경에서는 컨버지드 앱을 등록하려면 이름을 제공해야만 합니다. 생성된 앱은 개인 Microsoft 계정뿐만 아니라 모든 조직 디렉터리(다중 테넌트)를 지원하는 컨버지드 앱으로 등록되었습니다.  이전 환경을 통해 수정할 수 없습니다. [지원되는 계정 유형에 대해 자세히 알아보기](v2-supported-account-types.md)

새 환경에서는 앱의 이름을 제공하고 지원되는 계정 유형을 선택해야 합니다. 선택적으로 리디렉션 URI를 제공할 수 있습니다.
리디렉션 URI를 제공하는 경우 웹/공용(네이티브/모바일 및 데스크톱)인지 지정해야 합니다. 새 앱 등록 환경을 사용하여 앱을 등록하는 방법에 대한 자세한 내용은 [이 빠른 시작을](quickstart-register-app.md)참조하십시오.

## <a name="app-management-page"></a>앱 관리 페이지

이전 경험에는 속성, 응용 프로그램 암호, 플랫폼, 소유자, Microsoft 그래프 권한, 프로필 및 고급 옵션과 같은 섹션이 있는 컨버지드 앱에 대한 단일 앱 관리 페이지가 있었습니다.

Azure 포털의 새 환경은 이러한 기능을 별도의 페이지로 나타냅니다. 동등한 기능을 찾을 수 있는 곳은 다음과 같습니다.

-   속성 - 이름 및 응용 프로그램 ID가 개요 페이지에 있습니다.

-   응용 프로그램 암호가 인증서 & 비밀 페이지에 있습니다.

-   플랫폼 구성이 인증 페이지에 있습니다.

-   Microsoft 그래프 권한은 다른 권한과 함께 API 권한 페이지에 있습니다.

-   프로필이 브랜딩 페이지에 있습니다.

-   고급 옵션 - 라이브 SDK 지원이 인증 페이지에 있습니다.

## <a name="application-secretscertificates--secrets"></a>응용 프로그램 암호/인증서 & 비밀

새 환경에서 **응용 프로그램 암호의** 이름이 **인증서 & 암호로**변경되었습니다. 또한 **공개 키를** **인증서라고** 하며 **암호를** **클라이언트 암호라고**합니다. 보안상의 이유로 이 기능을 새 환경에서 함께 가져오지 않기로 결정했기 때문에 더 이상 새 키 쌍을 생성할 수 없습니다.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>플랫폼/인증: 회신 URL/리디렉션 URII
이전 환경에서 앱에는 리디렉션 URL, 로그아웃 URL 및 암시적 흐름을 구성하기 위한 웹, 네이티브 및 웹 API용 플랫폼 섹션이 있었습니다.

새 환경에서는\'앱의 인증 섹션에서 답장 URL을 찾을 수 있습니다. 또한 리디렉션 URI라고 하며 리디렉션 URI의 형식이 변경되었습니다. 앱 유형(웹 또는 공용 클라이언트 - 모바일 및 데스크톱)과 연결되어야 합니다. [자세히 알아보기](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

웹 API는 API 노출 페이지에서 구성됩니다.

> [!NOTE]
> 대상으로 지정하려는 플랫폼 또는 디바이스에 따라 애플리케이션에 대한 설정을 구성할 수 있는 새 인증 설정 환경을 사용해 보세요. [자세히 알아보기](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>마이크로소프트 그래프 권한/API 권한

-   이전 환경에서 API를 선택할 때 Microsoft 그래프 API에서만 선택할 수 있습니다. 새 환경에서는 Microsoft 그래프, 조직에서 API 및 API를 비롯한 많은 Microsoft API 중에서 선택할 수 있으며, 이 탭은 Microsoft API, 조직에서 사용하는 API 또는 내 API의 세 가지 탭으로 표시됩니다. 조직에서 API의 검색 표시줄은 테넌트의 서비스 주체를 통해 탭 검색을 사용합니다.

    > [!NOTE]
    > 응용 프로그램이 테넌트와 연결되어 있지 않으면 이 탭이 표시되지 않습니다. 새 환경을 사용하여 권한을 요청하는 방법에 대한 자세한 내용은 [이 빠른 시작을](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)참조하십시오.

-   이전 경험에는 권한 **부여 권한** 단추가 없습니다. 새 환경에서는 앱의 API 권한 섹션에 **권한 부여 관리자 동의** 버튼이 있는 권한 부여 동의 섹션이 있습니다. 관리자만 동의를 부여할 수 있으며 이 단추는 관리자만 사용할 수 있습니다. 관리자가 **권한 부여 관리자 동의** 버튼을 선택하면 요청된 모든 권한에 관리자 동의가 부여됩니다.

## <a name="profile"></a>프로필
이전 환경에서 프로필에는 로고, 홈 페이지 URL, 서비스 약관 URL 및 개인 정보 취급 방침 URL 구성이 있었습니다. 새 환경에서는 브랜딩 페이지에서 찾을 수 있습니다.

## <a name="application-manifest"></a>애플리케이션 매니페스트.
새 환경에서 매니페스트 페이지를 사용하면 앱의 속성을 편집하고 업데이트할 수 있습니다. 자세한 내용은 [애플리케이션 매니페스트](reference-app-manifest.md)를 참조하세요.

## <a name="new-ui"></a>새 UI
이전에는 매니페스트 편집기 또는 API를 사용하여만 설정할 수 있거나 존재하지 않았던 속성에 대한 새 UI가 있습니다.

-   암시적 권한 부여 흐름(oauth2AllowImplicitFlow)은 인증 페이지에서 찾을 수 있습니다. 이전 환경과 달리 액세스 토큰 또는 ID 토큰 또는 둘 다를 활성화할 수 있습니다.

-   이 API(oauth2Permissions)와 공인 된 클라이언트 응용 프로그램 (사전 권한 응용 프로그램)에 의해 정의 된 범위는 API 노출 페이지를 통해 구성할 수 있습니다. 앱을 웹 API로 구성하고 권한/범위를 노출하는 방법에 대한 자세한 내용은 [이 빠른 시작을](quickstart-configure-app-expose-web-apis.md)참조하십시오.

-   게시자 [도메인(응용 프로그램의\'동의 프롬프트에](application-consent-experience.md)사용자에게 표시)은 브랜딩 블레이드 페이지에서 찾을 수 있습니다. 게시자 도메인을 구성하는 방법에 대한 자세한 내용은 [이 방법](howto-configure-publisher-domain.md)의 를 참조하십시오.

## <a name="limitations"></a>제한 사항

새 경험에는 다음과 같은 제한 사항이 있습니다.

-   새 환경은 Azure AD B2C 테넌트에 대한 앱 등록을 아직 지원하지 않습니다.

-   새 환경은 개인 Microsoft 계정으로 만든 라이브 SDK 앱을 아직 지원하지 않습니다.

-   지원되는 계정에 대한 값을 변경하는 것은 UI에서 지원되지 않습니다. Azure AD 단일 테넌트와 다중 테넌트 간에 전환하는 경우가 아니면\'앱 매니페스트를 사용해야 합니다.

   > [!NOTE]
   > Azure AD 테넌트의 개인 Microsoft 계정 사용자이고 테넌트 관리자가 Azure 포털에 대한 액세스를 제한한 경우 액세스가 거부될 수 있습니다. 그러나 검색 표시줄에 앱 등록을 입력하거나 고정하여 바로 가기를 통과하면 새 경험에 액세스할 수 있습니다.
