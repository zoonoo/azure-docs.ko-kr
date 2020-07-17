---
title: '빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 SSO(Single Sign-On) 설정'
description: 이 빠른 시작에서는 Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 SSO(Single Sign-On)를 설정하는 과정을 안내합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038993"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 SSO(Single Sign-On) 설정

Azure AD 테넌트에 추가한 애플리케이션에 대한 SSO를 설정하여 간단한 사용자 로그인을 시작합니다. SSO를 설정하면 사용자가 자신의 Azure AD 자격 증명을 사용하여 애플리케이션에 로그인할 수 있습니다. SSO는 Azure AD 평가판에 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트에 추가한 애플리케이션에 대한 SSO를 설정하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자
- 이미 미리 구성되어 Azure AD 갤러리에 추가된 Single Sign-On 지원 애플리케이션. 대부분의 앱은 Azure AD를 Single Sign-On에 사용할 수 있습니다. Azure AD 갤러리의 앱은 미리 구성되어 있습니다. 앱이 목록에 없거나 맞춤형 앱인 경우에도 Azure AD에서 사용할 수 있습니다. 목차의 자습서 및 기타 설명서를 확인하세요. 이 빠른 시작에서는 SSO용으로 미리 구성되었으며 앱 개발자가 Azure AD 갤러리에 추가한 앱을 중점적으로 설명합니다.
- (선택 사항: [앱 보기](view-applications-portal.md) 완료)
- (선택 사항: [앱 추가](add-application-portal.md) 완료)
- (선택 사항: [앱 구성](add-application-portal-configure.md) 완료)


>[!IMPORTANT]
>이 빠른 시작의 단계를 테스트할 때 비-프로덕션 환경을 사용하는 것이 좋습니다.


## <a name="enable-single-sign-on-for-an-app"></a>앱에 Single Sign-On 설정

Azure AD 테넌트에 애플리케이션을 추가하는 즉시 애플리케이션에 대한 개요 페이지가 제공됩니다. 이미 추가된 애플리케이션을 구성하는 경우 테넌트에 추가된 애플리케이션을 살펴보는 방법을 안내하는 첫 번째 빠른 시작을 살펴봅니다. 

애플리케이션에 대한 Single Sign-On을 설정하는 방법은 다음과 같습니다.

1. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, Single Sign-On을 설정하려는 애플리케이션을 찾아 선택합니다.
2. [관리] 섹션에서 **Single Sign-On**을 선택하여 편집할 속성 창을 엽니다.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Azure AD 포털에 Single Sign-On 구성 페이지 표시":::
3. SAML을 선택하여 SSO 구성 페이지를 엽니다. 이 예제에서 SSO를 구성할 애플리케이션은 GitHub입니다. GitHub가 설정되면 사용자는 Azure AD 테넌트에서 자신의 자격 증명을 사용하여 GitHub에 로그인할 수 있습니다.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="GitHub에 Single Sign-On 구성 페이지 표시":::
4. SAML 기반 SSO에 Azure AD를 사용하도록 애플리케이션을 구성하는 프로세스는 애플리케이션에 따라 다릅니다. GitHub 참고 자료에 대한 링크가 있습니다. 다른 앱에 대한 가이드는 https://docs.microsoft.com/azure/active-directory/saas-apps/ 에서 찾을 수 있습니다.
5. 가이드에 따라 애플리케이션에 대한 SSO를 설정합니다. SSO가 작동하려면 특정 구독 요구 사항을 충족해야 하는 애플리케이션이 많이 있습니다. 예를 들어 GitHub는 엔터프라이즈 구독이 필요합니다.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="GitHub 가격 책정 페이지의 엔터프라이즈 구독에 Single Sign-On 옵션 표시":::


## <a name="next-steps"></a>다음 단계

- [앱 삭제](delete-application-portal.md)
