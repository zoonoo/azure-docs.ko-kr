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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956001"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>빠른 시작: Azure AD(Azure Active Directory) 테넌트에서 애플리케이션 삭제

이 빠른 시작에서는 Azure Portal을 사용하여 Azure AD 테넌트에 추가된 애플리케이션을 삭제합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트에서 애플리케이션을 삭제하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나입니다. 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- (선택 사항: [앱 보기](view-applications-portal.md) 완료).
- (선택 사항: [앱 추가](add-application-portal.md) 완료).
- (선택 사항: [앱 구성](add-application-portal-configure.md) 완료).
- (선택 사항: [Single Sign-On 설정](add-application-portal-setup-sso.md) 완료).

>[!IMPORTANT]
>이 빠른 시작의 단계를 테스트하기 위해 비-프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Azure AD 테넌트에서 애플리케이션 삭제

Azure AD 테넌트에서 애플리케이션을 삭제하려면 다음을 수행합니다.

1. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, 삭제할 애플리케이션을 찾아 선택합니다. 이 경우 이전 빠른 시작에서 추가한 GitHub_test 애플리케이션이 삭제됩니다.
2. 탐색의 관리 섹션에서 **속성**을 선택합니다.
3. 삭제를 선택한 다음, 예를 선택하여 Azure AD 테넌트에서 앱을 삭제할지 확인합니다.


## <a name="next-steps"></a>다음 단계

- [애플리케이션 관리 모범 사례](application-management-fundamentals.md)
- [애플리케이션 관리 공통 시나리오](common-scenarios.md)
- [애플리케이션 관리 표시 유형 및 제어](cloud-app-security.md)