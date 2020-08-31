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
ms.openlocfilehash: 461a424f23161e1567c6b32a38db0225efc56b1e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808391"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 SSO(Single Sign-On) 설정

Azure AD(Azure Active Directory) 테넌트에 추가한 애플리케이션에 대한 SSO(Single Sign-On)를 설정하여 간단한 사용자 로그인을 시작합니다. SSO를 설정한 후 사용자는 해당 Azure AD 자격 증명을 사용하여 애플리케이션에 로그인할 수 있습니다. SSO는 Azure AD 평가판에 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트에 추가한 애플리케이션에 대한 SSO를 설정하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- 이미 미리 구성되어 Azure AD 갤러리에 추가된 SSO 지원 애플리케이션입니다. 대부분의 앱은 SSO에 Azure AD를 사용할 수 있습니다. Azure AD 갤러리의 앱은 미리 구성되어 있습니다. 앱이 목록에 없거나 사용자 지정 개발 앱인 경우에도 Azure AD에서 사용할 수 있습니다. 목차의 자습서 및 기타 설명서를 확인하세요. 이 빠른 시작에서는 SSO용으로 미리 구성되었으며 앱 개발자가 Azure AD 갤러리에 추가한 앱을 중점적으로 설명합니다.
- 선택 사항: [앱 보기](view-applications-portal.md) 완료.
- 선택 사항: [앱 추가](add-application-portal.md) 완료.
- 선택 사항: [앱 구성](add-application-portal-configure.md) 완료.


>[!IMPORTANT]
>비 프로덕션 환경을 사용하여 이 빠른 시작의 단계를 테스트합니다.


## <a name="enable-single-sign-on-for-an-app"></a>앱에 Single Sign-On 설정

Azure AD 테넌트에 애플리케이션 추가를 완료하면 개요 페이지가 나타납니다. 이미 추가된 애플리케이션을 구성하는 경우 첫 번째 빠른 시작을 살펴보세요. 테넌트에 추가된 애플리케이션을 보는 과정을 안내합니다. 

애플리케이션에 대한 Single Sign-On을 설정하는 방법은 다음과 같습니다.

1. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택합니다. 그런 다음, Single Sign-On을 설정하려는 애플리케이션을 찾아서 선택합니다.
1. **관리** 섹션에서 **Single Sign-On**을 선택하여 편집할 **Single Sign-On** 창을 엽니다.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="스크린샷이 Azure AD Portal의 Single Sign-On 구성 페이지를 표시합니다.":::

1. **SAML**을 선택하여 SSO 구성 페이지를 엽니다. 이 예제에서 SSO를 구성할 애플리케이션은 GitHub입니다. GitHub가 설정되면 사용자는 Azure AD 테넌트에서 자신의 자격 증명을 사용하여 GitHub에 로그인할 수 있습니다.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="스크린샷이 GitHub의 Single Sign-On 구성 페이지를 표시합니다.":::

1. SAML 기반 SSO에 Azure AD를 사용하도록 애플리케이션을 구성하는 프로세스는 애플리케이션에 따라 다릅니다. GitHub의 지침에 대한 링크가 있습니다. 다른 앱에 대한 가이드를 찾으려면 [SaaS 애플리케이션과 Azure Active Directory를 통합하기 위한 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/)를 참조하세요.
1. 가이드에 따라 애플리케이션에 대한 SSO를 설정합니다. SSO가 작동하려면 특정 구독 요구 사항을 충족해야 하는 애플리케이션이 많이 있습니다. 예를 들어 GitHub는 엔터프라이즈 구독이 필요합니다.
    > [!TIP]
    > SAML 구성 옵션에 대한 자세한 내용은 [SAML 기반 Single Sign-On 구성](configure-saml-single-sign-on.md)을 참조하세요.

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="스크린샷이 GitHub 가격 책정 페이지의 엔터프라이즈 구독에 있는 Single Sign-On 옵션을 표시합니다.":::


> [!TIP]
> Graph API를 사용하여 앱 관리를 자동화할 수 있습니다. [Microsoft Graph API를 사용하여 앱 관리 자동화](https://docs.microsoft.com/graph/application-saml-sso-configure-api)를 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 시리즈를 완료한 후에는 앱을 삭제하여 테스트 테넌트를 정리하는 것이 좋습니다. 앱 삭제에 대한 내용은 이 시리즈의 마지막 빠른 시작에서 다룹니다. [앱 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

앱을 삭제하는 방법에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [앱 삭제](delete-application-portal.md)
