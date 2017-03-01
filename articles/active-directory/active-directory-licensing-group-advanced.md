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
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dad9a176f5f60b9165c5a55628929460047e5fdd
ms.openlocfilehash: a1510240350d88ee140acb11b3f86fd7985b9427
ms.lasthandoff: 02/22/2017


---

# <a name="azure-active-directory-group-based-licensing-additional-scenarios"></a>Azure Active Directory 그룹 기반 라이선스 추가 시나리오

## <a name="group-based-licensing-using-dynamic-groups"></a>동적 그룹을 사용하여 그룹 기반 라이선스

그룹 기반 라이선스는 모든 보안 그룹과 함께 사용할 수 있으며, 이는 Azure AD 동적 그룹과 함께 사용할 수 있음을 의미합니다. 동적 그룹은 사용자 개체 특성에 대한 규칙을 실행하여 사용자를 자동으로 추가하고 그룹에서 사용자를 제거합니다.

예를 들어 사용자에게 할당하려는 제품의 각 집합에 하나씩 여러 동적 그룹을 만들 수 있습니다. 각 그룹에는 사용자에 대한 특정 특성을 보는 규칙이 있으며, 그룹이 받아야 하는 라이선스 집합을 설명합니다. 특성은 온-프레미스에 할당되어 Azure AD와 동기화되거나, 클라우드에서 특성을 직접 관리할 수 있습니다.

특성을 지정하는 경우 사용자는 하나 이상의 이러한 동적 라이선스 그룹에 추가됩니다. 그런 다음 바로 라이선스가 사용자에 할당됩니다. 특성을 제거하면 사용자는 그룹에서 나가고 라이선스가 제거됩니다.

### <a name="example"></a>예제

이 예제에는 어떤 사용자가 어떤 Microsoft Online Services에 액세스하도록 결정하는 온-프레미스 ID 관리 솔루션이 있습니다. 이는 extensionAttribute1을 사용하여 사용자가 가져야 하고 Azure AD Connect가 Azure AD와 동기화하는 데 필요한 라이선스를 나타내는 문자열 값을 저장합니다. 내 사용자에게 Office 365 E5 및 EMS 라이선스를 배포하려고 합니다. Azure AD에서 각 제품에 하나씩 두 개의 동적 그룹을 만들었습니다. 이는 일부 사용자가 제품 중 하나만 필요로 하고 다른 제품은 필요로 하지 않기 때문입니다. 각 그룹에 대해 동적 멤버 자격 규칙과 라이선스 설정을 지정했습니다. 이들은 다음과 같습니다.

#### <a name="o365-e5--base-services"></a>O365 E5 - 기본 서비스

![O365 E5 기본 서비스](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5 - 사용이 허가된 사용자

![O365 E5 사용이 허가된 사용자](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

사용자들에게 두 라이선스 모두 가지도록 하려 하기 때문에 온-프레미스에서 한 명의 사용자를 수정하고 해당 extensionAttribute1을 `EMS;E5_baseservices;`의 값으로 수정했습니다. 변경 내용이 클라우드와 동기화된 후 사용자가 두 그룹에 자동으로 추가된 다음 라이선스가 할당되었습니다.

![사용자의 extensionAttribute1 설정](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>동적 그룹 멤버 자격 규칙 수정

기존 그룹의 멤버 자격 규칙을 수정할 때는 주의가 필요합니다. 규칙이 변경되면 모든 사용자가 그룹에서 제거되고, 규칙이 평가되고 사용자는 새 조건에 기반하여 그룹에 추가됩니다.

그룹에 라이선스가 할당되어 있는 경우 모든 사용자는 프로세스 시 제거된 해당 라이선스를 가집니다. 새 규칙이 평가되고 사용자가 다시 추가된 후에 새 라이선스가 적용됩니다. 이는 사용자가 서비스 손실 또는 경우에 따라 데이터 손실을 겪을 수 있다는 의미입니다.

라이선스 할당에 사용된 그룹에 대해 멤버 자격 규칙을 변경하지 않는 것이 좋습니다. 대신, 새 그룹으로 새 규칙으로 만들고 원래 그룹과 동일한 라이선스 설정을 지정합니다. 멤버가 추가되고 라이선스가 모든 사용자에 적용될 때까지 기다립니다. 그런 다음에야 진행할 수 있고 원래 그룹을 삭제할 수 있습니다. 이 접근 방법을 통해 사용자의 액세스 또는 데이터 손실 없이 새 멤버 자격 규칙으로 안전하고 단계적으로 전환할 수 있습니다.


## <a name="multiple-groups-and-multiple-licenses"></a>여러 그룹 및 여러 라이선스

사용자는 라이선스를 가진 여러 그룹의 구성원이 될 수 있습니다. 고려할 사항은 다음과 같습니다.

1. 동일한 제품에 대한 라이선스가 여러 개가 있으면 중첩될 수 있고 이로 인해 모든 사용 서비스가 사용자에게 적용됩니다. 예를 들어 두 개의 라이선스 그룹 만들었습니다. 하나는 *E3 – 기본 서비스*로 모든 사용자에게 먼저 배포하려는 기본 서비스를 포함하고 있고, 다른 하나는 *E3 – 서비스 확장*로 나중에 배포하기 위해 Yammer가 비활성 상태로 있는 동안 일부 사용자와 함께 사용해보려는 추가 서비스(Sway 및 Planner)를 포함하고 있습니다. 이 예제에서 사용자는 두 그룹 모두에 추가되었습니다.

  ![사용하도록 설정된 서비스 보기](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  그 결과, 그룹은 이 제품에 대해 한 라이선스를 사용하는 동안 사용된 제품의 12개 서비스 중 7개를 갖습니다.

2. *E3* 라이선스를 선택하면 어떤 그룹이 사용자를 위해 어떤 서비스를 사용했는지에 대한 정보 등의 자세한 내용을 표시합니다.

  ![그룹별로 사용하도록 설정된 서비스 보기](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>그룹 라이선스와 공존하는 직접 라이선스

사용자가 그룹의 라이선스를 상속하면 사용자 개체에서 해당 라이선스 할당을 바로 제거하거나 수정할 수 없습니다. 따라서 그룹에서 변경한 다음 시스템을 통해 모든 사용자에게 전파해야 합니다.

상속된 라이선스 외에도 동일한 SKU 라이선스를 사용자에게 직접 할당할 수 있습니다. 이는 예를 들어 다른 사용자에게 영향을 주지 않고 한 명의 사용자에 대해서만 SKU의 추가 서비스를 사용하도록 설정하는 경우에 사용할 수 있습니다.

직접 할당된 라이선스를 제거하더라도 상속된 라이선스에는 영향을 주지 않습니다. 몇 가지 서비스를 사용할 수 있는 그룹으로부터 *Office 365 Enterprise E3* 라이선스를 상속받은 사용자를 생각해보겠습니다.

1. 처음에 사용자는 *E3 – 기본 서비스* 그룹에게만 라이선스를 상속받습니다. 이를 통해 아래에 나열된 4개의 서비스 계획을 사용할 수 있습니다.

  ![E3 그룹에 사용하도록 설정된 서비스](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. **할당** 단추를 클릭하면 E3 라이선스를 사용자에게 직접 할당할 수 있습니다. 이 경우 Yammer Enterprise를 제외한 모든 서비스 계획을 사용하지 않도록 설정할 것입니다.

  ![라이선스를 사용자에게 할당](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 결과적으로, 사용자는 E3 제품의 1개의 라이선스만 계속 사용할 수 있는 반면, 직접 할당은 해당 사용자만 *Yammer Enterprise* 서비스를 사용할 수 있습니다. 그룹 멤버 자격과 직접 할당으로 어떤 서비스를 사용할 수 있는지는 다음 목록에서 확인할 수 있습니다.

  ![상속됨 및 직접 할당의 비교](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 직접 할당을 사용하면 다음과 같은 작업이 허용됩니다.

  a. *Yammer Enterprise*는 사용자 개체에서 직접 끌 수 있습니다. 다른 서비스의 토글이 아닌 켜기/끄기 토글을 사용할 수 있습니다. 이 서비스는 사용자에서 직접 사용하고 수정할 수 있기 때문입니다.

  b. 직접 할당된 라이선스의 일부로서 추가 서비스도 사용할 수 있습니다.

  c. **제거** 단추를 사용하여 사용자로부터 직접 라이선스를 제거할 수 있습니다. 이제 사용자는 상속된 그룹 라이선스만 가지며 원래 서비스만 사용할 수 있게 됩니다.

    ![직접 할당 제거](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>사용 위치

일부 Microsoft 서비스는 특정 위치에서만 사용 가능합니다. 라이선스를 사용자에게 할당할 수 있으려면 관리자가 사용자의 "사용 위치" 속성을 지정해야 합니다. 이 작업은 [Azure Portal](https://portal.azure.com)의 사용자-&gt; 프로필-&gt;설정 섹션에서 수행할 수 있습니다.

그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 사용자가 다른 위치에 있는 경우 라이선스가 있는 그룹에 사용자를 추가하기 전에 해당 사실이 사용자 개체에 제대로 반영되는지 확인합니다.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell을 사용하여 누가 상속됨과 직접 라이선스를 가지고 있는지 확인

공개 미리 보기에서는 PowerShell을 사용하여 그룹 라이선스 할당을 완벽하게 제어할 수 없습니다. 그러나 라이선스가 그룹에서 상속되거나 직접 할당된 경우 사용자 상태에 대한 기본 정보를 검색하는 데 사용할 수 있습니다. 아래 코드 샘플은 어떻게 관리자가 라이선스 할당에 대한 기본 보고서를 생성할 수 있는지 보여줍니다.

1. `connect-msolservice` cmdlet을 실행하여 인증하고 테넌트에 연결합니다.

2. `Get-MsolAccountSku`는 테넌트에서 프로비전된 모든 SKU 라이선스를 검색하는 데 사용할 수 있습니다.

  ![Get-msolaccountsku cmdlet 사용](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 이 예제에서는 어떤 사용자가 그룹 또는 둘 다에서 직접 할당된 *EMS* 라이선스를 가지고 있는지 알아보려 합니다. 사용자 개체와 SKU에 대한 이러한 질문에 답변할 수 있는 두 가지 함수가 포함된 PowerShell 스크립트를 사용합니다.
  ```
  \# Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)

      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if (\$license.GroupsAssigningLicense.Count -eq 0)
              {
                  return \$true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  if (\$assignmentSource -ieq \$user.ObjectId)
                  {
                      return \$true
                  }

              }
              return \$false
          }
      }
      return \$false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)
      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if (\$assignmentSource -ine \$user.ObjectId)
                  {
                      return \$true
                  }
              }
              return \$false
          }
      }
      return \$false
  }
  ```
4. 스크립트의 나머지 부분은 모든 사용자를 가져오고 각 사용자에서 이러한 함수를 실행한 다음 테이블의 출력 형식을 지정합니다.

  ```
  \# the license SKU we are interested in
  \$skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {\$\_.isLicensed -eq \$true -and \$\_.Licenses.AccountSKUID -eq \$skuId} | select \`
      ObjectId, \`
      @{Name="SkuId";Expression={\$skuId}}, \`
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly \$\_ \$skuId)}}, \`
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup \$\_ \$skuId)}}
  ```

5. 전체 스크립트의 출력은 다음과 같이 표시됩니다.

  ![PowerShell 스크립트 출력](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

1. 그룹 기반 라이선스는 현재 "중첩된 그룹"(다른 그룹을 포함하는 그룹)을 지원하지 않습니다. 중첩된 그룹에 라이선스를 적용하는 경우 그룹의 최상위 사용자 구성원만이 적용된 라이선스를 가집니다.

2. 그룹 기반 라이선스는 현재 [Azure Portal](https://portal.azure.com)을 통해서만 노출됩니다. 지금은 PowerShell을 사용하여 그룹에서 라이선스를 설정하거나 수정할 수 없습니다.

3. [Office 365 관리자 포털](https://portal.office.com )은 현재 그룹 기반 라이선스를 지원하지 않습니다. 사용자가 그룹에서 라이선스를 상속받은 경우 이 라이선스는 Office 관리자 포털에 일반 사용자 라이선스로서 표시됩니다. 해당 라이선스를 수정하려는 경우(예: 라이선스에서 서비스를 사용하지 않도록 설정하거나 라이선스를 제거하려는 경우) 포털은 오류 메시지를 반환합니다(상속된 그룹 라이선스를 사용자에서 직접 수정할 수 없기 때문).

  `To assign a license that contains Azure Information Protection Plan 1, you must also assign one of the following service plans: Azure Rights Management.`

4. 사용자가 그룹에서 제거되고 라이선스를 상실한 경우 해당 라이선스(Exchange Online 또는 SharePoint Online 등)의 서비스 계획은 최종 사용 안 함 상태가 아닌 "일시 중지" 상태로 설정됩니다. 이는 관리자가 그룹 구성원 자격 관리에서 실수하는 경우 사용자 데이터를 실수로 제거하는 것을 방지하기 위한 예방 조치로서 수행됩니다.

  해당 사용자에 대해 이러한 서비스 계획의 상태가 최종적으로 완전히 사용할 수 없게 되는 워크플로를 구현할 것입니다. 이것을 사용할 수 있기 전까지 일부 서비스는 그룹에서 제거되어 더 이상 라이선스가 없는 사용자를 위해 계속 작동합니다.

5. 매우 큰 그룹의 사용자(예를 들어 100,000명의 사용자)에서 라이선스를 할당하거나 수정할 때 Azure AD 자동화에 의한 변경 사항의 상당수는 Azure AD와 온-프레미스 시스템 간의 디렉터리 동기화의 성능에 부정적인 영향을 줄 수 있습니다. 이는 사용 환경의 디렉터리 동기화를 지연시킬 수 있습니다.

6. 라이선스 관리 자동화는 모든 유형의 환경 변경 사항에 자동으로 응답하지 않습니다. 예를 들어, 더 이상 라이선스가 없고 일부 사용자는 "라이선스 부족"의 오류 상태에 있게 됩니다. 그런 다음 사용 가능한 사용자 수를 확보하기 위해 다른 사용자에게 할당된 일부 라이선스를 직접 제거할 수 있습니다. 그러나 시스템은 이러한 변경 사항에 자동으로 대응하지 않고 해당 오류 상태에서 사용자를 수정합니다.

  이러한 제한 사항을 해결하기 위해 Azure AD의 그룹 블레이드로 이동하여 **다시 처리** 단추를 클릭할 수 있습니다. 이렇게 하면 해당 그룹의 모든 사용자를 처리하여 가능할 경우 오류 상태를 해결합니다.

## <a name="next-steps"></a>다음 단계

그룹 기반 라이선스를 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 문서를 읽어보세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)

