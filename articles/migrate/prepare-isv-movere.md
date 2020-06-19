---
title: ISV 도구/Movere와 함께 사용할 Azure Migrate 준비
description: 이 문서에서는 ISV 도구 또는 Movere와 함께 사용할 Azure Migrate를 준비하는 방법과 이 도구를 사용하여 시작하는 방법을 차례로 설명합니다.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682655"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>ISV 도구 또는 Movere로 작업 준비

Azure Migrate 프로젝트에 또는 [ISV 도구](migrate-services-overview.md#isv-integration) 또는 Movere를 추가한 경우 도구를 연결하고 데이터를 Azure Migrate로 보내기 전에 몇 가지 단계를 수행해야 합니다. 

## <a name="check-azure-ad-permissions"></a>Azure AD 권한 확인

Azure 사용자 계정에는 다음 권한이 필요합니다.

- Azure 테넌트를 사용하여 Azure AD(Azure Active Directory) 앱을 등록할 수 있는 권한
- 구독 수준에서 Azure AD 앱에 역할을 할당할 수 있는 권한


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Azure AD 앱을 등록할 수 있는 권한 설정

1. Azure AD에서 계정의 역할을 확인합니다.
2. 사용자 역할이 있는 경우 왼쪽에서 **사용자 설정**을 선택하고 사용자가 애플리케이션을 등록할 수 있는지 확인합니다. **예**로 설정하면 Azure AD 테넌트의 모든 사용자가 앱을 등록할 수 있습니다. **아니요**로 설정되어 있으면 관리 사용자만 앱을 등록할 수 있습니다.   
3. 권한이 없는 경우 관리 사용자가 사용자 계정에 [애플리케이션 관리자](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) 역할을 제공하여 앱을 등록하도록 할 수 있습니다.
4. Azure Migrate에 도구가 연결되면 관리자가 계정에서 역할을 제거할 수 있습니다.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Azure AD 앱에 역할을 할당할 수 있는 권한 설정
 
Azure 구독에서 Azure AD 앱에 역할을 할당하려면 계정에 **Microsoft.Authorization/*/Write** 액세스 권한이 필요합니다. 

1. Azure Portal에서 **구독**을 엽니다.
2. 관련 구독을 선택합니다. 표시되지 않는 경우 **전역 구독 필터**를 선택합니다. 
3. **내 사용 권한**을 선택합니다. 그런 다음, **이 구독에 대한 전체 액세스 세부 정보를 보려면 여기를 클릭합니다.** 를 선택합니다.
4. **역할 할당** > **보기**에서 권한을 확인합니다. 계정에 권한이 없는 경우 구독 관리자에게 [사용자 액세스 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) 역할 또는 [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 역할을 부여해 달라고 요청합니다.
 

## <a name="start-using-the-tool"></a>도구를 사용하여 시작하기

1. 도구에 대한 라이선스 또는 평가판이 아직 없는 경우 Azure Migrate의 도구 항목에 있는 **등록**에서 **자세한 정보**를 선택합니다.
2. 도구에서 지침에 따라 Azure Migrate 프로젝트에 연결하고 Azure Migrate에 데이터를 보냅니다.

## <a name="next-steps"></a>다음 단계

ISV 또는 Movere의 지침에 따라 데이터를 Azure Migrate로 보냅니다.

   
