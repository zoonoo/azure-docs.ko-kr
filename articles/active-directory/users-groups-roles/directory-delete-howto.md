---
title: Azure Active Directory 테넌트 디렉터리 삭제 | Microsoft Docs
description: Azure AD 테넌트 디렉터리를 삭제하도록 준비하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/07/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 39682ac04b914809aec36f46889feb5c4b59af51
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243885"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Azure Active Directory 테넌트 삭제

테넌트가 삭제되는 경우 테넌트에 포함된 모든 리소스도 삭제됩니다. 삭제하기 전에 연결된 리소스를 최소화하여 테넌트를 준비해야 합니다. Azure AD(Azure Active Directory) 전역 관리자만 포털에서 Azure AD 테넌트를 삭제할 수 있습니다.

## <a name="prepare-the-tenant-for-deletion"></a>삭제할 테넌트 준비

Azure AD의 테넌트는 여러 가지 검사를 통과한 다음에야 삭제할 수 있습니다. 이러한 검사는 테넌트 삭제로 인해 Office 365에 로그인하거나 Azure의 리소스에 액세스하는 기능 등 사용자 액세스에 부정적인 영향을 주는 위험을 줄일 수 있습니다. 예를 들어, 구독과 연결된 테넌트가 의도하지 않게 삭제된 경우, 사용자는 해당 구독에 대한 Azure 리소스에 액세스할 수 없습니다. 다음에서는 검사된 조건에 대해 설명합니다.

* 테넌트에는 테넌트를 삭제할 전역 관리자 한 명을 제외하고 사용자가 없을 수 있습니다. 테넌트를 삭제하려면 먼저 다른 모든 사용자를 삭제해야 합니다. 사용자가 온-프레미스에서 동기화된 경우 동기화를 해제해야 하며 Azure Portal 또는 Azure PowerShell cmdlet을 사용하여 클라우드 테넌트에서 사용자를 삭제해야 합니다. 
* 테넌트에는 애플리케이션이 없을 수 있습니다. 테넌트를 삭제하려면 먼저 모든 애플리케이션을 제거해야 합니다.
* 테넌트에 연결된 다단계 인증 공급자가 없을 수 있습니다.
* Microsoft Azure, Office 365 또는 Azure AD Premium 등 Microsoft Online Services에 대한 구독이 테넌트에 연결되어 있지 않습니다. 예를 들어, Azure에서 직접 기본 테넌트를 만든 경우 Azure 구독에서 인증에 이 테넌트를 계속 사용하면 이 테넌트를 삭제할 수 없습니다. 마찬가지로 다른 사용자가 구독을 연결한 경우, 테넌트를 삭제할 수 없습니다. 

## <a name="delete-an-azure-ad-tenant"></a>Azure AD 테넌트 삭제

1. 테넌트에 대한 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택합니다.

3. 삭제할 테넌트로 전환합니다.
  
  ![디렉터리 삭제 단추](./media/directory-delete-howto/delete-directory-command.png)

4. **디렉터리 삭제**를 선택합니다.
  
  ![디렉터리 삭제 단추](./media/directory-delete-howto/delete-directory-list.png)

5. 테넌트가 하나 이상의 검사를 통과하지 못하는 경우, 통과 방법에 대한 자세한 정보를 제공하는 링크가 제공됩니다. 모든 검사를 통과한 후 **삭제**를 선택하여 프로세스를 완료합니다.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>만료된 구독이 있지만 테넌트를 삭제할 수 없는 경우

Azure Active Directory 테넌트를 구성한 경우, 조직에 대해 Azure Active Directory Premium P2, Office 365 Business Premium 또는 Enterprise Mobility + Security E5와 같은 라이선스 기반 구독을 활성화했을 수도 있습니다. 이러한 구독은 실수로 인한 데이터 손실을 방지하기 위해 완전히 삭제될 때까지 디렉터리 삭제를 차단합니다. 테넌트 삭제를 허용하려면 구독이 **프로비전 해제됨** 상태여야 합니다. **만료됨** 또는 **취소됨** 상태의 구독은 **사용 안 함** 상태로 이동되며, 최종 단계는 **프로비전 해제됨** 상태입니다. 

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)를 참조하세요. 

구독 상태 | Data | 데이터 액세스
----- | ----- | -----
활성(평가판의 경우 30일)  | 모두 데이터에 액세스할 수 있음    | <li>사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<li>관리자는 Office 365 관리 센터 및 리소스에 대한 일반 액세스 권한이 있음 
만료됨(30일)   | 모두 데이터에 액세스할 수 있음    | <li>사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<li>관리자는 Office 365 관리 센터 및 리소스에 대한 일반 액세스 권한이 있음
사용 안 함(30일) | 관리자만 데이터에 액세스할 수 있음  | <li>사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<li>관리자는 Office 365 관리 센터에 액세스할 수 있지만 라이선스를 할당하거나 사용자를 업데이트할 수 없음
프로비전 해제됨(사용 안 함으로 설정된 후 30일) | 데이터가 삭제됨(사용 중인 다른 서비스가 없는 경우 자동으로 삭제됨) | <li>사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<li>관리자는 Office 365 관리 센터에 액세스하여 다른 구독을 구매하고 관리할 수 있음

구독을 **프로비전 해제됨** 상태로 전환하면 비즈니스용 Microsoft Store 관리 센터를 사용하여 3일 내에 삭제할 수 있습니다. 이 기능은 Office 365 관리 센터에 곧 제공될 예정입니다.

1. 테넌트의 전역 관리자 계정을 사용하여 [비즈니스용 Microsoft Store 관리 센터](https://businessstore.microsoft.com/manage/)에 로그인합니다. 초기 기본 도메인인 contoso.onmicrosoft.com이 있는 “Contoso” 테넌트를 삭제하려는 경우 admin@contoso.onmicrosoft.com과 같은 UPN으로 로그인합니다.

2. **관리** 탭으로 이동하여 **제품 및 서비스**를 선택한 다음, 취소할 구독을 선택하고 **삭제**를 선택합니다.
  
  ![구독 삭제를 위한 삭제 링크](./media/directory-delete-howto/delete-command.png)
  
3. **구독 삭제**를 선택하여 약관에 동의한 다음 구독을 삭제합니다. 모든 데이터는 3일 이내에 영구적으로 삭제됩니다. 구독 삭제를 취소하려는 경우 3일 내에 구독을 다시 활성화할 수 있습니다.
  
  ![사용 약관](./media/directory-delete-howto/delete-terms.png)

4. 이제 구독 상태가 변경되었으므로 구독이 삭제되도록 표시됩니다. 구독은 72시간 후에 **프로비전 해제됨** 상태가 됩니다.

5. 테넌트에서 구독을 삭제한 후 72시간이 경과하면 Azure AD 관리 센터에 다시 로그인할 수 있으며 테넌트 삭제를 차단하는 구독과 필요한 작업이 없어야 합니다. Azure AD 테넌트를 성공적으로 삭제할 수 있어야 합니다.
  
  ![삭제 시 구독 검사 통과 화면](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/)
