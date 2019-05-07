---
title: Azure CLI를 사용 하 여 Azure Blockchain 서비스 관리
description: 만들기 및 Azure CLI를 사용 하 여 Azure Blockchain 서비스를 관리 하는 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154447"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Azure CLI 사용 하 여 Azure Blockchain 서비스 관리

Azure portal 외에도 신속 하 게 만들고 관리 blockchain 멤버 및 트랜잭션 노드가 Azure Blockchain 서비스에 대 한 Azure CLI를 사용할 수 있습니다.

최신 설치 했는지 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 사용 하 여 Azure 계정에 로그인 하 고 `az login`입니다.

다음 예제에서는 예제로 바꿉니다 `<parameter names>` 를 고유한 값입니다.

## <a name="create-blockchain-member"></a>블록 체인 멤버 만들기

예제에서는 새 컨소시엄에 쿼럼 원장 프로토콜을 실행 하는 Azure Blockchain Service에서 blockchain 멤버를 만듭니다.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service blockchain 멤버를 식별 하는 고유 이름입니다. 공용 끝점 주소에 대 한 이름이 사용 됩니다. 예: `myblockchainmember.blockchain.azure.com`. |
| **위치** | Azure blockchain 구성원을 만들 지역입니다. 예: `eastus`. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **암호** | 멤버 계정 암호입니다. 기본 인증을 사용 하 여 blockchain 멤버의 공용 끝점을 인증할 수 멤버 계정 암호가 사용 됩니다. 암호는 다음 네 가지 요구 사항을 세 가지를 충족 해야 합니다: 억음 악센트 길이 요구 사항 12 & 72 자, 소문자 1 자, 1 개의 대문자, 숫자 및 번호가 아닌 sign(#) percent(%), 쉼표 (,), star(*)에는 특수 문자 1 사이 여야 (\`), quote("), 단일 작은, 대시 (-) 및 semicolumn(;)를 두 번|
| **protocol** | 공개 미리 보기는 쿼럼을 지원 합니다. |
| **consortium** | 만들거나 조인 consortium의 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 암호입니다. 암호는 컨소시엄을 조인 하는 데 사용 됩니다. |
| **ruleName** | 허용 목록 IP 주소 범위에 대 한 규칙 이름입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|
| **startIpAddress** | 허용 목록에 대 한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **endIpAddress** | 허용 목록에 대 한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **skuName** | 계층 유형입니다. Basic에 대 한 표준 및 B0 S0을 사용 합니다. |

## <a name="change-blockchain-member-password"></a>블록 체인 멤버 암호 변경

블록 체인 멤버의 암호를 변경 하는 예제입니다.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 멤버를 식별 하는 이름입니다. |
| **암호** | 멤버 계정 암호입니다. 암호는 다음 네 가지 요구 사항을 세 가지를 충족 해야 합니다: 억음 악센트 길이 요구 사항 12 & 72 자, 소문자 1 자, 1 개의 대문자, 숫자 및 번호가 아닌 sign(#) percent(%), 쉼표 (,), star(*)에는 특수 문자 1 사이 여야 (\`), quote("), 단일 작은, 대시 (-) 및 세미콜론 (;)를 두 번입니다. |


## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

기존 블록 체인 멤버 내에서 트랜잭션 노드를 만듭니다. 트랜잭션 노드를 추가 하 여 보안 격리 높이고 부하 분산할 수 있습니다. 예를 들어, 여러 클라이언트 응용 프로그램에 대 한 트랜잭션 노드 끝점이 있을 수 있습니다.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름을 포함 하는 Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |
| **위치** | Azure blockchain 구성원을 만들 지역입니다. 예: `eastus`. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **암호** | 트랜잭션 노드 암호입니다. 암호는 다음 네 가지 요구 사항을 세 가지를 충족 해야 합니다: 억음 악센트 길이 요구 사항 12 & 72 자, 소문자 1 자, 1 개의 대문자, 숫자 및 번호가 아닌 sign(#) percent(%), 쉼표 (,), star(*)에는 특수 문자 1 사이 여야 (\`), quote("), 단일 작은, 대시 (-) 및 세미콜론 (;)를 두 번입니다. |
| **ruleName** | 허용 목록 IP 주소 범위에 대 한 규칙 이름입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **startIpAddress** | 허용 목록에 대 한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **endIpAddress** | 허용 목록에 대 한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|

## <a name="change-transaction-node-password"></a>트랜잭션 노드 암호 변경

트랜잭션 노드 암호를 변경 하는 예제입니다.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름을 포함 하는 Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |
| **암호** | 트랜잭션 노드 암호입니다. 암호는 다음 네 가지 요구 사항을 세 가지를 충족 해야 합니다: 억음 악센트 길이 요구 사항 12 & 72 자, 소문자 1 자, 1 개의 대문자, 숫자 및 번호가 아닌 sign(#) percent(%), 쉼표 (,), star(*)에는 특수 문자 1 사이 여야 (\`), quote("), 단일 작은, 대시 (-) 및 세미콜론 (;)를 두 번입니다. |

## <a name="change-consortium-management-account-password"></a>컨소시엄 관리 계정 암호 변경

컨소시엄 관리 계정 컨소시엄 멤버 자격 관리에 사용 됩니다. 각 멤버는 컨소시엄 관리 계정에서 고유 하 게 식별 하 고 다음 명령 사용 하 여이 계정의 암호를 변경할 수 있습니다.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 멤버를 식별 하는 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 계정 암호입니다. 암호는 다음 네 가지 요구 사항을 세 가지를 충족 해야 합니다: 억음 악센트 길이 요구 사항 12 & 72 자, 소문자 1 자, 1 개의 대문자, 숫자 및 번호가 아닌 sign(#) percent(%), 쉼표 (,), star(*)에는 특수 문자 1 사이 여야 (\`), quote("), 단일 작은, 대시 (-) 및 세미콜론 (;)를 두 번입니다. |
  
## <a name="update-firewall-rules"></a>방화벽 규칙 업데이트

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |
| **ruleName** | 허용 목록 IP 주소 범위에 대 한 규칙 이름입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|
| **startIpAddress** | 허용 목록에 대 한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|
| **endIpAddress** | 허용 목록에 대 한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|

## <a name="list-api-keys"></a>목록 API 키

사용자 이름 및 암호와 유사한 노드 액세스에 대 한 API 키를 사용할 수 있습니다. 키 회전을 지원 하기 위해 API 키를 두 가지입니다. 다음 명령을 사용 하 여 API 키를 나열 합니다.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름을 포함 하는 Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |

## <a name="regenerate-api-keys"></a>API 키를 다시 생성

다음 명령을 사용 하 여 API 키를 다시 생성 합니다.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름을 포함 하는 Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |
| **keyName** | 바꿉니다 \<keyValue\> key1 또는 key2 합니다. |

## <a name="delete-a-transaction-node"></a>트랜잭션 노드를 삭제 합니다.

멤버 트랜잭션 블록 체인 노드를 삭제 하는 예제입니다.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 삭제할 새 트랜잭션 노드 이름을 포함 하는 Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |

## <a name="delete-a-blockchain-member"></a>블록 체인 멤버 삭제

블록 체인 멤버를 삭제 하는 예제입니다.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 삭제할 Azure Blockchain 서비스 blockchain 멤버의 이름입니다. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD 사용자에 대 한 액세스 권한 부여

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **assignee** | Azure AD 사용자 id입니다. 예를 들어 `user@contoso.com` |
| **scope** | 역할 할당의 범위입니다. 트랜잭션 노드의 블록 체인 멤버를 수 있습니다. |

**예제:**

블록 체인에 Azure AD 사용자에 대 한 노드 액세스 **멤버**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**예제:**

블록 체인에 Azure AD 사용자에 대 한 노드 액세스 **트랜잭션 노드**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD 그룹 또는 응용 프로그램에 대 한 노드 액세스 권한을 부여할 역할

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **assignee-object-id** | Azure AD 그룹 ID 또는 응용 프로그램 id입니다. |
| **scope** | 역할 할당의 범위입니다. 트랜잭션 노드의 블록 체인 멤버를 수 있습니다. |

**예제:**

노드 액세스 권한을 부여할 **응용 프로그램 역할**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD 노드 액세스를 제거 합니다.

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **assignee** | Azure AD 사용자 id입니다. 예를 들어 `user@contoso.com` |
| **scope** | 역할 할당의 범위입니다. 트랜잭션 노드의 블록 체인 멤버를 수 있습니다. |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure portal을 사용 하 여 Azure Blockchain Service 트랜잭션 노드를 구성 합니다.](configure-transaction-nodes.md)
