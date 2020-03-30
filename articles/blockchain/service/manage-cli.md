---
title: Azure CLI를 사용하여 Azure 블록 체인 서비스 관리
description: Azure CLI를 사용하여 Azure 블록 체인 서비스를 관리하는 방법
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455586"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLI를 사용하여 Azure 블록 체인 서비스 관리

Azure 포털 외에도 Azure CLI를 사용하여 Azure 블록 체인 서비스에 대한 블록 체인 멤버 및 트랜잭션 노드를 관리할 수 있습니다.

최신 [Azure CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli) 설치하고 `az login`에서 Azure 계정에 로그인했는지 확인합니다.

다음 예제에서는 예제를 `<parameter names>` 사용자 고유의 값으로 바꿉습니다.

## <a name="create-blockchain-member"></a>블록체인 멤버 만들기

예를 들어 새 컨소시엄에서 쿼럼 원장 프로토콜을 실행하는 Azure 블록 체인 서비스에서 블록 체인 멤버를 만듭니다.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **(이름)** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. `myblockchainmember.blockchain.azure.com`)을 입력합니다. |
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. `eastus`)을 입력합니다. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **다시 설정** | 멤버 계정 암호 멤버 계정 암호는 기본 인증을 사용하여 블록체인 멤버의 공용 엔드포인트를 인증할 때 사용됩니다. 암호는 다음 네 가지 요구 사항 중 세 가지 를 충족해야 합니다: 길이는 12& 72자, 1 소문자, 1 대문자, 1 숫자 및 숫자 기호(#),\`퍼센트(%), 쉼표(%), 쉼표(), 별(*), 따옴표(),double quote("), single quote("), 대시(-) 및 반열(;) 사이에 있어야 합니다.|
| **프로토콜** | 공개 미리 보기는 쿼럼을 지원합니다. |
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 암호입니다. 암호는 컨소시엄에 참여하는 데 사용됩니다. |
| **규칙 이름** | IP 주소 범위를 허용 목록에 추가하기 위한 규칙 이름입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다.|
| **시작 입어 주소** | 허용 목록에 대한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다. |
| **엔드IpAddress** | 허용 목록에 대한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다. |
| **스쿠네임** | 계층 유형입니다. 표준에는 S0를 사용하고, 기본에는 B0를 사용합니다. |

## <a name="change-blockchain-member-password"></a>블록체인 회원 비밀번호 변경

예를 들어 블록 체인 회원의 암호를 변경합니다.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **(이름)** | Azure 블록 체인 서비스 멤버를 식별하는 이름입니다. |
| **다시 설정** | 멤버 계정 암호 암호는 다음 네 가지 요구 사항 중 세 가지 를 충족해야 합니다: 길이는 12& 72자, 1 소문자, 1 대문자, 1 숫자 및 숫자 기호(#),\`퍼센트(%), 쉼표(%), 쉼표(), 별()), 따옴표()), 큰따옴표("), 단일 따옴표("), 대시(-) 및 세미콜론(;) 사이에 있어야 합니다. |

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

기존 블록 체인 멤버 내에서 트랜잭션 노드를 만듭니다. 트랜잭션 노드를 추가하면 보안 격리를 늘리고 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 응용 프로그램에 대한 트랜잭션 노드 끝점을 가질 수 있습니다.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **(이름)** | 새 트랜잭션 노드 이름도 포함하는 Azure 블록 체인 서비스 블록 체인 멤버의 이름입니다. |
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. `eastus`)을 입력합니다. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **다시 설정** | 트랜잭션 노드 암호입니다. 암호는 다음 네 가지 요구 사항 중 세 가지 를 충족해야 합니다: 길이는 12& 72자, 1 소문자, 1 대문자, 1 숫자 및 숫자 기호(#),\`퍼센트(%), 쉼표(%), 쉼표(), 별()), 따옴표()), 큰따옴표("), 단일 따옴표("), 대시(-) 및 세미콜론(;) 사이에 있어야 합니다. |
| **규칙 이름** | IP 주소 범위를 허용 목록에 추가하기 위한 규칙 이름입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다. |
| **시작 입어 주소** | 허용 목록에 대한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다. |
| **엔드IpAddress** | 허용 목록에 대한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다.|

## <a name="change-transaction-node-password"></a>트랜잭션 노드 암호 변경

트랜잭션 노드 암호를 변경하는 예제입니다.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure 블록 체인 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **(이름)** | 새 트랜잭션 노드 이름도 포함하는 Azure 블록 체인 서비스 블록 체인 멤버의 이름입니다. |
| **다시 설정** | 트랜잭션 노드 암호입니다. 암호는 다음 네 가지 요구 사항 중 세 가지 를 충족해야 합니다: 길이는 12& 72자, 1 소문자, 1 대문자, 1 숫자 및 숫자 기호(#),\`퍼센트(%), 쉼표(%), 쉼표(), 별()), 따옴표()), 큰따옴표("), 단일 따옴표("), 대시(-) 및 세미콜론(;) 사이에 있어야 합니다. |

## <a name="change-consortium-management-account-password"></a>컨소시엄 관리 계정 암호 변경

컨소시엄 관리 계정은 컨소시엄 멤버십 관리에 사용됩니다. 각 구성원은 컨소시엄 관리 계정으로 고유하게 식별되며 다음 명령으로 이 계정의 암호를 변경할 수 있습니다.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **(이름)** | Azure 블록 체인 서비스 멤버를 식별하는 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 계정 암호입니다. 암호는 다음 네 가지 요구 사항 중 세 가지 를 충족해야 합니다: 길이는 12& 72자, 1 소문자, 1 대문자, 1 숫자 및 숫자 기호(#),\`퍼센트(%), 쉼표(%), 쉼표(), 별()), 따옴표()), 큰따옴표("), 단일 따옴표("), 대시(-) 및 세미콜론(;) 사이에 있어야 합니다. |
  
## <a name="update-firewall-rules"></a>방화벽 규칙 업데이트

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure 블록 체인 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **(이름)** | Azure 블록 체인 서비스 블록 체인 회원의 이름입니다. |
| **규칙 이름** | IP 주소 범위를 허용 목록에 추가하기 위한 규칙 이름입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다.|
| **시작 입어 주소** | 허용 목록에 대한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다.|
| **엔드IpAddress** | 허용 목록에 대한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대한 선택적 매개 변수입니다.|

## <a name="list-api-keys"></a>API 키 목록

API 키는 사용자 이름 및 암호와 유사한 노드 액세스에 사용할 수 있습니다. 키 회전을 지원하는 두 개의 API 키가 있습니다. 다음 명령을 사용하여 API 키를 나열합니다.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure 블록 체인 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **(이름)** | 새 트랜잭션 노드 이름도 포함하는 Azure 블록 체인 서비스 블록 체인 멤버의 이름입니다. |

## <a name="regenerate-api-keys"></a>API 키 재생성

다음 명령을 사용하여 API 키를 다시 생성합니다.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure 블록 체인 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **(이름)** | 새 트랜잭션 노드 이름도 포함하는 Azure 블록 체인 서비스 블록 체인 멤버의 이름입니다. |
| **키 이름** | 키값을 \<\> 키1 또는 key2로 바꿉습니다. |

## <a name="delete-a-transaction-node"></a>트랜잭션 노드 삭제

예를 들어 블록 체인 멤버 트랜잭션 노드를 삭제합니다.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure 블록 체인 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **(이름)** | 삭제할 트랜잭션 노드 이름도 포함하는 Azure 블록 체인 서비스 블록 체인 멤버의 이름입니다. |

## <a name="delete-a-blockchain-member"></a>블록체인 회원 삭제

예를 들어 블록 체인 멤버를 삭제합니다.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **리소스 그룹** | Azure 블록 체인 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **(이름)** | 삭제할 Azure 블록 체인 서비스 블록 체인 멤버의 이름입니다. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD 사용자에 대한 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **역할** | Azure AD 역할의 이름입니다. |
| **assignee** | Azure AD 사용자 ID입니다. 예를 들어 `user@contoso.com` |
| **scope** | 역할 할당의 범위입니다. 블록 체인 멤버 또는 트랜잭션 노드가 될 수 있습니다. |

**예제:**

**Azure**AD 사용자에 대한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**예제:**

Azure AD 사용자에 대한 노드 액세스 권한 부여 - 블록 체인 **트랜잭션 노드**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD 그룹 또는 응용 프로그램 역할에 대한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **역할** | Azure AD 역할의 이름입니다. |
| **할당자- 개체 ID** | Azure AD 그룹 ID 또는 응용 프로그램 ID입니다. |
| **scope** | 역할 할당의 범위입니다. 블록 체인 멤버 또는 트랜잭션 노드가 될 수 있습니다. |

**예제:**

응용 프로그램 **역할에** 대한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD 노드 액세스 제거

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **역할** | Azure AD 역할의 이름입니다. |
| **assignee** | Azure AD 사용자 ID입니다. 예를 들어 `user@contoso.com` |
| **scope** | 역할 할당의 범위입니다. 블록 체인 멤버 또는 트랜잭션 노드가 될 수 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure 포털을 사용하여 [Azure 블록 체인 서비스 트랜잭션 노드를 구성하는](configure-transaction-nodes.md)방법에 대해 알아봅니다.
