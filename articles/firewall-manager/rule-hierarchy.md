---
title: Azure Firewall 정책을 사용하여 규칙 계층 구조 정의
description: Azure Firewall 정책을 사용하여 규칙 계층 구조를 정의하고 준수를 적용하는 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331739"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Azure Firewall 정책을 사용하여 규칙 계층 구조 정의

보안 관리자는 방화벽을 관리하고 온-프레미스 및 클라우드 배포에 대한 준수를 보장해야 합니다. 주요 구성 요소는 자동화된 방식으로 방화벽 규칙을 만들 수 있도록 애플리케이션 팀에 CI/CD 파이프라인을 구현하는 유연성을 제공하는 기능입니다.

Azure Firewall 정책을 사용하여 규칙 계층 구조를 정의하고 준수를 적용할 수 있습니다.

- 자식 애플리케이션 팀 정책 위에 중앙 기본 정책을 오버레이하는 계층 구조를 제공합니다. 기본 정책은 우선 순위가 높고 자식 정책보다 먼저 실행됩니다.
- Azure 사용자 지정 역할 정의를 사용하여 실수로 인한 기본 정책 제거를 방지하고 구독 또는 리소스 그룹 내에서 규칙 컬렉션 그룹에 대한 선택적 액세스를 제공합니다. 

## <a name="solution-overview"></a>솔루션 개요

이 예제의 개략적인 단계는 다음과 같습니다.

1. 보안 팀 리소스 그룹에 기본 방화벽 정책을 만듭니다. 
3. 기본 정책에서 IT 보안 관련 규칙을 정의합니다. 이렇게 하면 트래픽을 허용/거부하는 일반적인 규칙 집합이 추가됩니다.
4. 기본 정책을 상속하는 애플리케이션 팀 정책을 만듭니다. 
5. 정책에서 애플리케이션 팀별 규칙을 정의합니다. 기존 방화벽에서 규칙을 마이그레이션할 수도 있습니다.
6. Azure Active Directory 사용자 지정 역할을 만들어 규칙 컬렉션 그룹에 세분화된 액세스 권한을 제공하고 방화벽 정책 범위에서 역할을 추가합니다. 다음 예에서는 영업 팀 구성원이 영업 팀 방화벽 정책의 규칙 컬렉션 그룹을 편집할 수 있습니다. 데이터베이스 및 엔지니어링 팀에도 마찬가지입니다.
7. 해당 방화벽에 정책을 연결합니다. Azure 방화벽에는 할당된 정책이 하나만 있을 수 있습니다. 이렇게 하려면 각 애플리케이션 팀에 고유한 방화벽이 있어야 합니다.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="팀 및 요구 사항" border="false":::

### <a name="create-the-firewall-policies"></a>방화벽 정책 만들기

- 기본 방화벽 정책입니다.

각 애플리케이션 팀을 위해 다음 정책을 만듭니다.

- 판매 방화벽 정책. 판매 방화벽 정책은 기본 방화벽 정책을 상속합니다.
- 데이터베이스 방화벽 정책. 데이터베이스 방화벽 정책은 기본 방화벽 정책을 상속합니다.
- 엔지니어링 방화벽 정책. 엔지니어링 방화벽 정책은 기본 방화벽 정책을 상속합니다.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="정책 계층 구조" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>규칙 컬렉션 그룹에 액세스하기 위한 사용자 지정 역할 만들기 

사용자 지정 역할은 각 애플리케이션 팀에 대해 정의됩니다. 역할은 작업 및 범위를 정의합니다. 애플리케이션 팀은 해당 애플리케이션에 대한 규칙 컬렉션 그룹을 편집할 수 있습니다.

다음 개략적인 절차를 사용하여 사용자 지정 역할을 정의합니다.

1. 구독을 가져옵니다.

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. 다음 명령 실행:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Get-AzRoleDefinition명령을 사용하여 Reader 역할을 JSON 형식으로 출력합니다. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. 편집기에서 ReaderSupportRole.json 파일을 엽니다.

   다음은 JSON 출력을 보여줍니다. 여러 속성에 대한 내용은  [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. 다음 연산을 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

    **Actions** 속성에 추가하도록 JSON 파일을 편집합니다. 읽기 작업 뒤에 꼭 쉼표를 추가해야 합니다. 이 작업을 통해 사용자는 규칙 컬렉션 그룹을 만들고 업데이트할 수 있습니다.
6.  **AssignableScopes** 에서 구독 ID를 다음 형식으로 추가합니다. 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   명시적 구독 ID를 추가해야 합니다. 그렇지 않으면 역할을 구독으로 가져올 수 없습니다.
7.  **Id** 속성 줄을 삭제하고  **IsCustom** 속성을 true로 변경합니다.
8.  **Name** 및 **Description** 속성을 ‘AZFM Rule Collection Group Author’(AZFM 규칙 컬렉션 그룹 작성자) 및 ‘Users in this role can edit Firewall Policy rule collection groups’(이 역할의 사용자는 방화벽 정책 규칙 컬렉션을 편집할 수 있음)로 변경합니다. 

JSON 파일은 다음과 비슷해야 합니다.

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. 새로운 사용자 지정 역할을 만들려면 New-AzRoleDefinition 명령을 사용하여 JSON 역할 정의 파일을 지정합니다. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>사용자 지정 역할 나열

Get-AzRoleDefinition 명령을 사용하여 모든 사용자 지정 역할을 나열할 수 있습니다.

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Azure Portal에서 사용자 지정 역할을 볼 수도 있습니다. 구독으로 이동하여 **액세스 제어(IAM)** , **역할** 을 선택합니다.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="SalesAppPolicy 읽기 권한":::

자세한 내용은 [자습서: Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기](../role-based-access-control/tutorial-custom-role-powershell.md)를 참조하세요.

### <a name="add-users-to-the-custom-role"></a>사용자 지정 역할에 사용자 추가

포털에서 AZFM 규칙 컬렉션 그룹 작성자 역할에 사용자를 추가하고 방화벽 정책에 대한 액세스 권한을 제공할 수 있습니다.

1. 포털에서 애플리케이션 팀 방화벽 정책(예: SalesAppPolicy)을 선택합니다.
2. **액세스 제어** 를 선택합니다.
3. **역할 할당 추가** 를 선택합니다.
4. 사용자/사용자 그룹(예: 영업 팀)을 역할에 추가합니다.

다른 방화벽 정책에 대해 이 절차를 반복합니다.

### <a name="summary"></a>요약

사용자 지정 역할이 있는 방화벽 정책에서는 방화벽 정책 규칙 컬렉션 그룹에 대한 선택적 액세스 권한을 제공합니다.

사용자는 다음 작업을 수행할 권한이 없습니다.
- Azure Firewall 또는 방화벽 정책을 삭제합니다.
- 방화벽 정책 계층, DNS 설정 또는 위협 인텔리전스를 업데이트합니다.
- AZFM 규칙 컬렉션 그룹 작성자 그룹의 구성원이 아닌 방화벽 정책을 업데이트합니다.

보안 관리자는 기본 정책을 사용하여 가드 레일을 적용하고 기업에서 요구하는 특정 유형의 트래픽(예: ICMP)을 차단할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Azure Firewall 정책](policy-overview.md)에 대해 자세히 알아보세요.

