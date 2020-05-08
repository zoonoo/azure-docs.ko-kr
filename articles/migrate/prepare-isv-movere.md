---
title: ISV tools/Movere를 사용 하 Azure Migrate 준비
description: ISV tools/Movere를 사용 하 Azure Migrate 준비
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906983"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>ISV tools/Movere 작업 준비

Azure Migrate 프로젝트에 [ISV 도구](migrate-services-overview.md#isv-integration)또는 Movere를 추가한 경우 도구를 연결 하기 전에 준비 하 고 Azure Migrate에 데이터를 전송 하기 전에 몇 가지 단계를 수행 해야 합니다. 

## <a name="check-azure-ad-permissions"></a>Azure AD 권한 확인

Azure 사용자 계정에는 두 가지 권한이 필요 합니다.

- Azure 테 넌 트에 Azure AD 앱을 등록할 수 있는 권한.
- 구독 수준에서 Azure AD 앱에 역할을 할당할 수 있는 권한입니다.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Azure AD 앱을 등록 하기 위한 권한 설정

1. Azure Active Directory에서 계정의 역할을 확인 합니다. 사용자 역할을 사용 하는 경우 왼쪽의 **사용자 설정** 을 클릭 하 고 사용자가 응용 프로그램을 등록할 수 있는지 확인 합니다.
2. **예**로 설정 된 경우 Azure AD 테 넌 트의 모든 사용자가 앱을 등록할 수 있습니다. 사용할 수 없는 경우 관리 사용자만 앱을 등록할 수 있습니다.
3. 권한이 없는 경우 관리 사용자는 응용 프로그램을 등록할 수 있도록 사용자 계정에 [응용 프로그램 관리자](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) 역할을 제공할 수 있습니다.
4. 도구가 Azure Migrate 연결 된 후 관리자가 계정에서 역할을 제거할 수 있습니다.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Azure AD 앱에 역할을 할당할 수 있는 권한 설정
 
Azure 구독에서 계정에는 Azure AD 앱에 역할을 할당 하기 위한 **Microsoft 권한 부여/*/쓰기 액세스 권한이**필요 합니다. 

1. 구독 권한을 확인 하려면 Azure Portal에서 **구독**을 엽니다.
2. 관련 구독을 선택합니다. 표시 되지 않는 경우 **전역 구독 필터**를 선택 합니다. 
3. **내 사용 권한**을 선택합니다. 그런 다음, **이 구독에 대한 전체 액세스 세부 정보를 보려면 여기를 클릭합니다.** 를 선택합니다.
4. **역할 할당** > **보기**에서 사용 권한을 확인 합니다.
5. 계정에 권한이 없는 경우 구독 관리자에 게 [사용자 액세스 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) 역할 또는 [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 역할을 추가 하도록 요청 합니다.
 

## <a name="start-using-the-tool"></a>도구 사용 시작

1. 도구에 대 한 라이선스 또는 무료 평가판이 아직 없는 경우 Azure Migrate의 도구 항목에서 **등록**을 클릭 하 여 **자세한 정보**를 클릭 합니다.
2. 도구에서 지침에 따라 도구에서 Azure Migrate 프로젝트로 연결 하 고 데이터를 Azure Migrate으로 보냅니다.

## <a name="next-steps"></a>다음 단계

ISV 또는 Movere 지침에 따라 데이터를 Azure Migrate 보냅니다.

   
