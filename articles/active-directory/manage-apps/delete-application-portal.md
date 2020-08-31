---
title: '빠른 시작: Azure AD(Azure Active Directory) 테넌트에서 애플리케이션 삭제'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory) 테넌트에서 애플리케이션을 삭제합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 422c174116bb6973993f5c6093a83f8bdb42a62a
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808238"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>빠른 시작: Azure AD(Azure Active Directory) 테넌트에서 애플리케이션 삭제

이 빠른 시작에서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory) 테넌트에 추가된 애플리케이션을 삭제합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트에서 애플리케이션을 삭제하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- 선택 사항: [앱 보기](view-applications-portal.md) 완료.
- 선택 사항: [앱 추가](add-application-portal.md) 완료.
- 선택 사항: [앱 구성](add-application-portal-configure.md) 완료.
- 선택 사항: [Single Sign-On 설정](add-application-portal-setup-sso.md) 완료.

>[!IMPORTANT]
>비 프로덕션 환경을 사용하여 이 빠른 시작의 단계를 테스트합니다.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Azure AD 테넌트에서 애플리케이션 삭제

Azure AD 테넌트에서 애플리케이션을 삭제하려면 다음을 수행합니다.

1. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택합니다. 그런 다음, 삭제하려는 애플리케이션을 찾아서 선택합니다. 이 경우 이전 빠른 시작에서 추가한 **GitHub_test** 애플리케이션이 삭제됩니다.
1. 왼쪽 창의 **관리** 섹션에서 **속성**을 선택합니다.
1. **삭제**를 선택한 다음, **예**를 선택하여 Azure AD 테넌트에서 앱을 삭제할지 확인합니다.

> [!TIP]
> Graph API를 사용하여 앱 관리를 자동화할 수 있습니다. [Microsoft Graph API를 사용하여 앱 관리 자동화](https://docs.microsoft.com/graph/application-saml-sso-configure-api)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 시리즈를 완료한 후에는 앱을 삭제하여 테스트 테넌트를 정리하는 것이 좋습니다. 이 빠른 시작에서는 앱을 삭제하는 방법을 다루었습니다.

## <a name="next-steps"></a>다음 단계

빠른 시작 시리즈를 완료했습니다. 다음 단계로 앱 관리 모범 사례를 읽어 보세요.
> [!div class="nextstepaction"]
> [애플리케이션 관리 모범 사례](application-management-fundamentals.md)
