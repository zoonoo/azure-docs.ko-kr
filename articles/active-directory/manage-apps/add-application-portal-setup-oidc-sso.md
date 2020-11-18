---
title: '빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 OIDC 기반 SSO(Single Sign-On) 설정'
description: 이 빠른 시작에서는 Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 OIDC 기반 SSO(Single Sign-On)를 설정하는 과정을 안내합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 9ea4ec748ca37f93e9711970b10746a009543d00
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656601"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 대한 OIDC 기반 SSO(Single Sign-On) 설정

Azure AD(Azure Active Directory) 테넌트에 추가한 애플리케이션에 대한 SSO(Single Sign-On)를 설정하여 간단한 사용자 로그인을 시작합니다. SSO를 설정한 후 사용자는 해당 Azure AD 자격 증명을 사용하여 애플리케이션에 로그인할 수 있습니다. SSO는 Azure AD 평가판에 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트에 추가한 애플리케이션에 대한 SSO를 설정하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- 이미 미리 구성되어 Azure AD 갤러리에 추가된 SSO 지원 애플리케이션입니다. 대부분의 앱은 SSO에 Azure AD를 사용할 수 있습니다. Azure AD 갤러리의 앱은 미리 구성되어 있습니다. 앱이 목록에 없거나 사용자 지정 개발 앱인 경우에도 Azure AD에서 사용할 수 있습니다. 목차의 자습서 및 기타 설명서를 확인하세요. 이 빠른 시작에서는 SSO용으로 미리 구성되었으며 앱 개발자가 Azure AD 갤러리에 추가한 앱을 중점적으로 설명합니다.
- 선택 사항: [앱 보기](view-applications-portal.md) 완료.
- 선택 사항: [앱 추가](add-application-portal.md) 완료.
- 선택 사항: [앱 구성](add-application-portal-configure.md) 완료.
- 선택 사항: [앱에 사용자 할당](add-application-portal-assign-users.md) 완료.

>[!IMPORTANT]
>비 프로덕션 환경을 사용하여 이 빠른 시작의 단계를 테스트합니다.

## <a name="enable-single-sign-on-for-an-app"></a>앱에 Single Sign-On 설정

SSO에 대한 OIDC 표준을 사용하는 앱을 추가하는 경우 설정 단추가 있습니다. 단추를 선택하면 애플리케이션 사이트로 이동하여 앱에 대한 가입 프로세스를 완료합니다. 앱을 추가하는 프로세스는 이 시리즈의 앞부분에 있는 앱 추가 빠른 시작에 설명되어 있습니다. 이미 추가된 애플리케이션을 구성하는 경우 첫 번째 빠른 시작을 살펴보세요. 테넌트에 이미 있는 애플리케이션을 보는 과정을 안내합니다. 

애플리케이션에 대한 Single Sign-On을 설정하는 방법은 다음과 같습니다.

1. 이 시리즈의 앞부분에 나오는 빠른 시작에서는 ID 관리에 Azure AD 테넌트를 사용하는 앱을 추가하는 방법을 알아보았습니다. 앱 개발자가 OIDC 표준을 사용하여 SSO를 구현하는 경우 앱을 추가할 때 등록 단추가 표시됩니다. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Single Sign-On 옵션 및 가입 단추를 보여 주는 스크린샷" lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. **가입** 을 선택하면 앱 개발자 로그온 페이지가 표시됩니다. Azure Active Directory 로그인 자격 증명을 사용하여 로그인합니다. 

   > [!IMPORTANT]
    > 애플리케이션에 대한 구독이 이미 있는 경우 사용자 세부 정보 및 테넌트/디렉터리 정보에 대한 유효성 검사가 수행됩니다. 애플리케이션에서 사용자를 확인할 수 없는 경우 사용자가 애플리케이션 서비스 또는 오류 페이지에 가입하도록 리디렉션됩니다.

3. 인증에 성공하면 관리자 동의를 요청하는 대화 상자가 나타납니다. **조직 대신 동의** 를 선택하고 **수락** 을 선택합니다. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="앱에 대한 동의 화면을 보여 주는 스크린샷" lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. 애플리케이션이 테넌트에 추가되고 애플리케이션 홈 페이지가 표시됩니다.


> [!TIP]
> Graph API를 사용하여 앱 관리를 자동화할 수 있습니다. [Microsoft Graph API를 사용하여 앱 관리 자동화](/graph/application-saml-sso-configure-api)를 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 시리즈를 완료했다면 앱을 삭제하여 테스트 테넌트를 정리하는 것이 좋습니다. 앱 삭제에 대한 내용은 이 시리즈의 마지막 빠른 시작에서 다룹니다. [앱 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

앱을 삭제하는 방법에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [앱 삭제](delete-application-portal.md)