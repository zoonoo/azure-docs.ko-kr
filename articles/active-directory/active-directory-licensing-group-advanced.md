---

title: "Azure Active Directory 그룹 기반 라이선스 추가 시나리오 | Microsoft Docs"
description: "Azure Active Directory 그룹 기반 라이선스의 추가 시나리오"
services: active-directory
keywords: "Azure AD 라이선스"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a7240c52e9351a60e3cf577d8112862c7dc8d913
ms.lasthandoff: 03/14/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Azure Active Directory에서 라이선스 관리를 위해 그룹을 사용하여 시나리오, 제한 사항 및 알려진 문제

다음 정보와 예제를 사용하면 Azure AD(Azure Active Directory) 그룹 기반 라이선스에 대해 자세히 이해할 수 있습니다.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>동적 그룹에서 그룹 기반 라이선스 사용

모든 보안 그룹에서 그룹 기반 라이선스를 사용할 수 있으며, 이는 Azure AD 동적 그룹과 결합할 수 있음을 의미합니다. 동적 그룹은 사용자 개체 특성에 대한 규칙을 실행하여 그룹에서 사용자를 자동으로 추가하거나 제거합니다.

예를 들어 사용자에게 할당하려는 제품의 각 집합에 하나씩 여러 동적 그룹을 만들 수 있습니다. 각 그룹에는 사용자에 대한 특정 특성을 보는 규칙이 있으며, 그룹이 받아야 하는 라이선스 집합을 설명합니다. 특성을 온-프레미스에 할당하여 Azure AD와 동기화할 수 있거나 클라우드에서 특성을 직접 관리할 수 있습니다.

특성을 지정하면 사용자가 이러한 동적 라이선스 그룹 중 하나 이상에 추가됩니다. 그런 직후에 라이선스가 사용자에게 할당됩니다. 특성을 제거하면 사용자는 그룹에서 나가고 라이선스가 제거됩니다.

### <a name="example"></a>예

어떤 사용자가 어떤 Microsoft Online Services에 액세스해야 하는지 결정하는 온-프레미스 ID 관리 솔루션을 사용하는 것이 좋습니다. 이 솔루션은 **extensionAttribute1**을 사용하여 사용자가 가지고 있어야 하는 라이선스를 나타내는 문자열 값을 저장합니다. Azure AD Connect는 이 값을 Azure AD와 동기화합니다.

이 예의 목표는 사용자에게 Office 365 Enterprise E5 및 Enterprise Mobility + Security 라이선스를 배포하는 것입니다. Azure AD에서 각 제품에 대해 하나씩 두 개의 동적 그룹을 만듭니다. 이는 일부 사용자에게 제품 중 하나만 필요하고 다른 제품은 필요하지 않을 수 있기 때문입니다. 그런 다음 각 그룹에서 동적 멤버 자격 규칙과 라이선스 설정을 지정합니다. 이들은 다음과 같습니다.

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: 기본 서비스

![Office 365 Enterprise E5 기본 서비스의 스크린샷](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: 허가된 사용자

![Enterprise Mobility + Security 허가된 사용자의 스크린샷](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

이 예에서는 한 사용자를 수정하고 extensionAttribute1을 `EMS;E5_baseservices;` 값으로 설정합니다. 이는 사용자가 두 라이선스를 모두 가지려고 하기 때문입니다. 이 수정 작업은 온-프레미스에서 수행할 수 있습니다. 변경 내용이 클라우드와 동기화되면 사용자가 두 그룹에 자동으로 추가되고 라이선스가 할당됩니다.

![사용자의 extensionAttribute1을 설정하는 방법을 보여 주는 스크린샷](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>동적 그룹 멤버 자격 규칙 수정

기존 그룹의 멤버 자격 규칙을 수정할 때는 주의해야 합니다. 규칙을 변경하면 모든 사용자가 그룹에서 제거됩니다. 규칙을 평가한 다음 새 조건에 따라 사용자가 그룹에 추가됩니다.

그룹에 라이선스가 할당되어 있으면 모든 사용자가 프로세스 중에 제거된 라이선스를 가집니다. 새 규칙이 평가되고 사용자가 다시 추가된 후에만 새 라이선스가 적용됩니다. 이는 사용자가 서비스를 손실하거나 경우에 따라 데이터를 손실할 수 있다는 의미입니다.

라이선스 할당에 사용되는 그룹에 대한 멤버 자격 규칙을 변경하지 않는 것이 좋습니다. 대신, 새 그룹으로 새 규칙으로 만들고 원래 그룹과 동일한 라이선스 설정을 지정합니다. 멤버가 추가되고 라이선스가 모든 사용자에 적용될 때까지 기다립니다. 그런 후에만 원래 그룹을 삭제할 수 있습니다. 이 방법을 통해 사용자의 액세스 손실 또는 데이터 손실 없이 새 멤버 자격 규칙으로 안전하고 단계적으로 전환할 수 있습니다.


## <a name="multiple-groups-and-multiple-licenses"></a>여러 그룹 및 여러 라이선스

사용자는 라이선스를 가진 여러 그룹의 구성원이 될 수 있습니다. 고려할 사항은 다음과 같습니다.

- 동일한 제품에 대한 라이선스가 여러 개 있으면 겹칠 수 있어 사용 가능한 모든 서비스가 사용자에게 적용됩니다. 다음 예에서는 두 개의 라이선스 그룹을 보여 줍니다. *E3 기본 서비스*에는 모든 사용자에게 처음 배포할 기본 서비스가 포함되어 있으며, *E3 확장 서비스*에는 일부 사용자에게만 배포할 수 있는 추가 서비스(Sway 및 Planner)가 포함되어 있습니다. Yammer는 향후 배포를 위해 비활성화된 상태로 유지됩니다. 이 예제에서 사용자는 두 그룹 모두에 추가되었습니다.

  ![사용하도록 설정된 서비스의 스크린샷](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  결과적으로 사용자가 제품의 12개 서비스 중 7개를 사용하도록 설정하는 한편, 하나의 라이선스만 이 제품에 사용합니다.

- *E3* 라이선스를 선택하면 어떤 그룹으로 인해 사용자가 어떤 서비스를 사용할 수 있는지에 대한 정보를 포함하여 자세한 정보가 표시됩니다.

  ![그룹별로 사용하도록 설정된 서비스의 스크린샷](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>그룹 라이선스와 공존하는 직접 라이선스

사용자가 그룹의 라이선스를 상속하면 사용자 개체에서 해당 라이선스 할당을 바로 제거하거나 수정할 수 없습니다. 따라서 그룹에서 변경한 다음 시스템을 통해 모든 사용자에게 전파해야 합니다.

그러나 상속된 라이선스 외에도 동일한 제품 라이선스를 사용자에게 직접 할당할 수 있습니다. 예를 들어 다른 사용자에게 영향을 주지 않고 한 명의 사용자만 제품의 추가 서비스를 사용하도록 설정하는 경우에 해당 라이선스를 직접 할당할 수 있습니다.

직접 할당된 라이선스를 제거하더라도 상속된 라이선스에는 영향을 주지 않습니다. 예를 들어 그룹에서 Office 365 Enterprise E3 라이선스를 상속받는 다음과 같은 사용자를 생각해보겠습니다.

1. 처음에 사용자는 *E3 기본 서비스* 그룹에서만 라이선스를 상속받습니다. 이는 다음 이미지와 같이 4개의 서비스 계획을 사용하도록 설정할 수 있습니다.

  ![E3 그룹에서 사용하도록 설정된 서비스의 스크린샷](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. **할당**을 클릭하면 E3 라이선스를 사용자에게 직접 할당할 수 있습니다. 이 경우 Yammer Enterprise를 제외한 모든 서비스 계획을 사용하지 않도록 설정합니다.

  ![사용자에게 라이선스를 직접 할당하는 방법의 스크린샷](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 결과적으로 사용자는 여전히 E3 제품의 라이선스 하나만 사용합니다. 그러나 직접 할당을 사용하면 해당 사용자에 대해서만 Yammer Enterprise 서비스를 사용하도록 설정할 수 있습니다. 다음 이미지에서는 그룹 멤버 자격과 직접 할당으로 사용하도록 설정한 서비스를 보여 줍니다.

  ![상속된 할당 대 직접 할당의 스크린샷](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 직접 할당을 사용할 때 허용되는 작업은 다음과 같습니다.

  a. Yammer Enterprise는 사용자 개체에서 직접 해제 수 있습니다. 다른 서비스 토글과 달리 **사용/해제** 토글이 이 서비스에 대해 활성화되어 있습니다. 이는 사용자에 대해 이 서비스를 직접 활성화하여 수정할 수 있기 때문입니다.

  b. 직접 할당된 라이선스의 일부로서 추가 서비스도 사용할 수 있습니다.

  c. **제거** 단추를 사용하여 사용자로부터 직접 라이선스를 제거할 수 있습니다. 이제 사용자는 상속된 그룹 라이선스만 가지며 원래 서비스만 사용할 수 있게 됩니다.

    ![직접 할당을 제거하는 방법을 보여 주는 스크린샷](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>사용 위치

일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자에 대해 **사용 위치** 속성을 지정해야 합니다. 이 작업은 [Azure Portal](https://portal.azure.com)의 **사용자** &gt; **프로필** &gt; **설정**에서 수행할 수 있습니다.

그룹 라이선스 할당의 경우 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 사용자가 다른 위치에 있는 경우 라이선스가 있는 그룹에 사용자를 추가하기 전에 해당 사실이 사용자 개체에 제대로 반영되는지 확인합니다.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell을 사용하여 누가 상속됨과 직접 라이선스를 가지고 있는지 확인

Azure AD 릴리스의 미리 보기 기간 동안에는 PowerShell을 사용하여 그룹 라이선스 할당을 완벽하게 제어할 수 없습니다. 그러나 사용자 상태에 대한 기본 정보를 검색하는 데 사용하거나 그룹에서 라이선스를 상속받았는지 또는 직접 할당했는지 확인하는 데 사용할 수 있습니다. 아래 코드 샘플에서는 관리자가 라이선스 할당에 대한 기본 보고서를 생성할 수 있는 방법을 보여 줍니다.

1. `connect-msolservice` cmdlet을 실행하여 인증하고 테넌트에 연결합니다.

2. `Get-MsolAccountSku`는 테넌트에서 프로비전된 모든 제품 라이선스를 검색하는 데 사용할 수 있습니다.

  ![Get-msolaccountsku cmdlet의 스크린샷](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 이 예제에서는 어떤 사용자가 Enterprise Mobility + Security 라이선스를 직접, 그룹에서 또는 둘 다를 통해 할당했는지 확인하려고 합니다. 다음 PowerShell 스크립트를 사용할 수 있습니다.
  
  ```
  \# Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses
     {
        \# we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          \# This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. 스크립트의 나머지 부분은 모든 사용자를 가져오고 각 사용자에 대해 이러한 함수를 실행합니다. 그런 다음 출력 형식을 테이블에 지정합니다.
    
  ```
  \# the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. 전체 스크립트의 출력은 다음과 같이 표시됩니다.

  ![PowerShell 스크립트 출력의 스크린샷](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

그룹 기반 라이선스를 사용하는 경우 제한 사항 및 알려진 문제점에 대한 다음 목록을 잘 알고 있는 것이 좋습니다.

- 그룹 기반 라이선스는 현재 "중첩된 그룹"(다른 그룹을 포함하는 그룹)을 지원하지 않습니다. 중첩된 그룹에 라이선스를 적용하는 경우 그룹의 최상위 수준 사용자 멤버에게만 라이선스가 적용됩니다.

- 하나 이상의 라이선스는 그룹 멤버 자격에서 상속되기 때문에 수정할 수 없습니다. 그룹 기반 라이선스를 보거나 수정하려면 Azure 관리 포털을 방문합니다.

- [Office 365 관리자 포털](https://portal.office.com )은 현재 그룹 기반 라이선스를 지원하지 않습니다. 사용자가 그룹에서 라이선스를 상속받는 경우 이 라이선스는 Office 관리 포털에 일반 사용자 라이선스로 표시됩니다. 해당 라이선스를 수정하거나(예: 라이선스에서 서비스를 사용하지 않도록 설정) 라이선스를 제거하려고 시도하는 경우 포털에서 오류 메시지를 반환합니다. 상속된 그룹 라이선스는 사용자가 직접 수정할 수 없습니다.

- 사용자가 그룹에서 제거되고 라이선스를 상실하는 경우 해당 라이선스(Exchange Online 또는 SharePoint Online)의 서비스 계획이 "일시 중단됨" 상태로 설정됩니다. 서비스 계획은 최종 사용 안 함 상태로 설정되지 않습니다. 이는 관리자가 그룹 멤버 자격 관리에서 실수하는 경우 사용자 데이터를 실수로 제거하지 않도록 방지하기 위한 예방 조치입니다.

- 매우 큰 그룹(예: 100,000명의 사용자)에 대해 라이선스를 할당하거나 수정하면 성능에 영향을 줄 수 있습니다. 특히 Azure AD 자동화에서 생성된 변경 볼륨은 Azure AD와 온-프레미스 시스템 간의 디렉터리 동기화 성능에 부정적인 영향을 줄 수 있습니다. 이는 사용 환경의 디렉터리 동기화를 지연시킬 수 있습니다.

- 라이선스 관리 자동화는 모든 유형의 환경 변경 사항에 자동으로 응답하지 않습니다. 예를 들어 라이선스가 부족하여 일부 사용자가 오류 상태에 있을 수 있습니다. 사용 가능한 사용자 수를 확보하기 위해 직접 할당된 일부 라이선스를 다른 사용자에게서 제거할 수 있습니다. 그러나 시스템은 이러한 변경에 자동으로 대응하지 않고 해당 오류 상태에 있는 사용자를 수정합니다.

  이러한 제한 사항을 해결하려면 Azure AD의 **그룹** 블레이드로 이동하여 **다시 처리**를 클릭하면 됩니다. 이렇게 하면 해당 그룹의 모든 사용자를 처리하고 가능한 경우 오류 상태를 해결합니다.

## <a name="next-steps"></a>다음 단계

그룹 기반 라이선스를 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)

