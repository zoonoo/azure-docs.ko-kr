---
title: '빠른 시작: Azure Active Directory를 ID 공급자로 사용하는 앱에 사용자 할당'
description: 이 빠른 시작에서는 Azure AD를 ID 공급자로 사용하도록 설정한 앱을 사용자가 사용할 수 있도록 허용하는 프로세스를 안내합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 9d396c0787798c783b44748dc2461c073334cc7e
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322503"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>빠른 시작: Azure AD를 ID 공급자로 사용하는 앱에 사용자 할당

이전 빠른 시작에서는 앱의 속성을 구성했습니다. 속성을 설정할 때 할당된 사용자 및 할당되지 않은 사용자 모두에 대해 환경을 구성합니다. 이 빠른 시작에서는 앱에 사용자를 할당하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 테넌트에 추가한 앱에 사용자를 설정하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- 선택 사항: [앱 보기](view-applications-portal.md) 완료.
- 선택 사항: [앱 추가](add-application-portal.md) 완료.
- 선택 사항: [앱 구성](add-application-portal-configure.md) 완료.

>[!IMPORTANT]
>비 프로덕션 환경을 사용하여 이 빠른 시작의 단계를 테스트합니다.

## <a name="assign-users-to-an-app"></a>앱에 사용자 할당
1. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택합니다. 그런 다음, 구성하려는 애플리케이션을 찾아 선택합니다.
2. 왼쪽 탐색 메뉴에서 **사용자 및 그룹**을 선택합니다.
   > [!NOTE]
   > 일부 Microsoft 365 및 Office 365 앱은 PowerShell을 사용해야 합니다. 
3. **사용자 추가** 단추를 선택합니다.
4. **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.
5. 애플리케이션에 할당할 사용자 또는 그룹을 선택합니다. 검색 상자에 사용자 또는 그룹의 이름을 입력할 수도 있습니다. 여러 사용자 및 그룹을 선택할 수 있으며 선택 항목은 **선택한 항목** 아래에 표시됩니다.
    > [!IMPORTANT]
    > 애플리케이션에 그룹을 할당하면 그룹의 사용자만 액세스할 수 있습니다. 할당은 중첩된 그룹에 적용되지 않습니다.

    > [!NOTE]
    > 그룹 기반 할당에는 Azure Active Directory Premium P1 또는 P2 버전이 필요합니다. 그룹 기반 할당은 보안 그룹에만 지원됩니다. 중첩된 그룹 멤버 자격 및 Office 365 그룹은 현재 지원되지 않습니다. 이 문서에서 설명하는 기능에 대한 추가 라이선스 요구 사항은 [Azure Active Directory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory)를 참조하세요. 
6. 완료되면 **선택**을 선택합니다.
   ![앱에 사용자 또는 그룹 할당](./media/assign-user-or-group-access-portal/assign-users.png)
7. **사용자 및 그룹** 창의 목록에서 하나 이상의 사용자 또는 그룹을 선택한 다음, 창 하단에 있는 **선택** 단추를 선택합니다.
8. 애플리케이션에서 지원하는 경우 사용자 또는 그룹에 역할을 할당할 수 있습니다. **할당 추가** 창에서 **역할 선택**을 선택합니다. 그런 다음, **역할 선택** 창에서 선택한 사용자 또는 그룹에 적용할 역할을 선택한 다음, 창 하단에 있는 **확인** 단추를 선택합니다. 
    > [!NOTE]
    > 애플리케이션에서 역할 선택을 지원하지 않는 경우 기본 액세스 역할이 할당됩니다. 이 경우 애플리케이션은 사용자의 액세스 수준을 관리합니다.
9. **할당 추가** 창에서 창 하단에 있는 **할당** 단추를 선택합니다.

> [!NOTE]
> 동일한 절차를 사용하여 사용자 또는 그룹의 할당을 해제할 수 있습니다. 할당을 해제할 사용자 또는 그룹을 선택한 다음, **제거**를 선택합니다. 일부 Microsoft 365 및 Office 365 앱은 PowerShell을 사용해야 합니다. 

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작을 완료한 후에는 앱을 삭제하는 것이 좋습니다. 이렇게 하면 테스트 테넌트를 깨끗하게 유지할 수 있습니다. 앱 삭제에 대한 내용은 이 시리즈의 마지막 빠른 시작에서 다룹니다. [앱 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

앱에 대한 Single Sign-On을 지정하는 방법에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [Single Sign-On 설정](add-application-portal-setup-sso.md)
