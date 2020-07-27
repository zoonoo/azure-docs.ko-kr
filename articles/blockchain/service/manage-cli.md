---
title: Azure CLI를 사용 하 여 Azure Blockchain 서비스 관리
description: Azure CLI를 사용 하 여 Azure Blockchain 서비스를 관리 하는 방법
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170855"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure Blockchain 서비스 관리

Azure Portal 외에도 Azure CLI를 사용 하 여 Azure Blockchain 서비스의 블록 체인 구성원 및 트랜잭션 노드를 관리할 수 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬로 설치 하 고 사용 하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

## <a name="prepare-your-environment"></a>환경 준비

1. 로그인합니다.

    로컬에 설치된 CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 로그인합니다.

    ```azurecli
    az login
    ```

    터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

1. Azure CLI 확장을 설치합니다.

    Azure CLI에 대한 확장 참조를 사용하는 경우 먼저 확장을 설치해야 합니다.  Azure CLI 확장은 아직 핵심 CLI의 일부로 제공되지 않는 실험적 명령과 시험판 명령에 대한 액세스를 제공합니다.  확장 업데이트 및 제거를 포함하여 확장에 대해 자세한 내용을 보려면 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.

    다음 명령을 실행 하 여 [Azure Blockchain 서비스에 대 한 확장](/cli/azure/ext/blockchain/blockchain) 을 설치 합니다.

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Blockchain 구성원 만들기

예제에서는 새 컨소시엄에서 쿼럼 원장 프로토콜을 실행 하는 Azure Blockchain 서비스에서 [blockchain 멤버를 만듭니다](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) .

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. 예: `myblockchainmember.blockchain.azure.com` |
| **location** | 블록체인 멤버가 만들어지는 Azure 지역입니다. `eastus`)을 입력합니다. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. 기능은 일부 지역에서 사용하지 못할 수도 있습니다. |
| **password** | 멤버의 기본 트랜잭션 노드에 대한 암호입니다. 블록체인 멤버의 기본 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 소문자 1 자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 백슬래시 ( \` ), 큰따옴표 ("), 작은따옴표 ('), 대시 (-), semicolumn (;)) 사이 여야 합니다.|
| **protocol** | Blockchain 프로토콜입니다. 현재 *쿼럼* 프로토콜이 지원 됩니다. |
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. 컨소시엄에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요. |
| **컨소시엄-관리-계정-암호** | 컨소시엄 계정 암호는 멤버 계정 암호라고도 합니다. 멤버 계정 암호는 멤버용으로 생성된 Ethereum 계정의 프라이빗 키를 암호화하는 데 사용됩니다. 컨소시엄 관리용 멤버 계정과 멤버 계정 암호를 사용합니다. |
| **sku** | 계층 유형입니다. *표준* 또는 *기본*입니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 프로덕션 등급 배포에 *표준* 계층을 사용합니다. Blockchain Data Manager를 사용하거나 대량의 프라이빗 트랜잭션을 전송하는 경우 *표준* 계층도 사용해야 합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Blockchain 구성원 암호 또는 방화벽 규칙 변경

이 예에서는 [blockchain 구성원](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)의 암호, consortium 관리 암호 및 방화벽 규칙을 업데이트 합니다.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 구성원을 식별 하는 이름입니다. |
| **password** | 멤버의 기본 트랜잭션 노드에 대한 암호입니다. 블록체인 멤버의 기본 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 소문자 1 자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 백슬래시 ( \` ), 큰따옴표 ("), 작은따옴표 ('), 대시 (-), semicolumn (;)) 사이 여야 합니다.|
| **컨소시엄-관리-계정-암호** | 컨소시엄 계정 암호는 멤버 계정 암호라고도 합니다. 멤버 계정 암호는 멤버용으로 생성된 Ethereum 계정의 프라이빗 키를 암호화하는 데 사용됩니다. 컨소시엄 관리용 멤버 계정과 멤버 계정 암호를 사용합니다. |
| **방화벽-규칙** | IP 허용 목록의 시작 및 끝 IP 주소입니다. |

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

기존 blockchain 멤버 안에 [트랜잭션 노드를 만듭니다](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) . 트랜잭션 노드를 추가 하 여 보안 격리를 강화 하 고 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 응용 프로그램에 대 한 트랜잭션 노드 끝점이 있을 수 있습니다.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **location** | Blockchain 구성원의 Azure 지역입니다. |
| **멤버 이름** | Azure Blockchain 서비스 구성원을 식별 하는 이름입니다. |
| **password** | 트랜잭션 노드의 암호입니다. 트랜잭션 노드 공용 끝점에 연결할 때 기본 인증에 암호를 사용 합니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 소문자 1 자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 백슬래시 ( \` ), 큰따옴표 ("), 작은따옴표 ('), 대시 (-), semicolumn (;)) 사이 여야 합니다.|
| **name** | 트랜잭션 노드 이름입니다. |

## <a name="change-transaction-node-password"></a>트랜잭션 노드 암호 변경

예 [는 트랜잭션 노드 암호를 업데이트](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) 합니다.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **멤버 이름** | Azure Blockchain 서비스 구성원을 식별 하는 이름입니다. |
| **password** | 트랜잭션 노드의 암호입니다. 트랜잭션 노드 공용 끝점에 연결할 때 기본 인증에 암호를 사용 합니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 소문자 1 자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 백슬래시 ( \` ), 큰따옴표 ("), 작은따옴표 ('), 대시 (-), semicolumn (;)) 사이 여야 합니다.|
| **name** | 트랜잭션 노드 이름입니다. |

## <a name="list-api-keys"></a>API 키 나열

API 키는 사용자 이름 및 암호와 유사한 노드 액세스에 사용할 수 있습니다. 키 회전을 지 원하는 두 개의 API 키가 있습니다. 다음 명령을 사용 하 여 [API 키를 나열](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)합니다.

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 Blockchain 구성원의 이름 |

## <a name="regenerate-api-keys"></a>API 키 다시 생성

다음 명령을 사용 하 여 [API 키를 다시 생성](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)합니다.

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |
| **keyName** | \<keyValue\>Key1, key2 또는 both 중 하나로 대체 합니다. |

## <a name="delete-a-transaction-node"></a>트랜잭션 노드 삭제

예에서는 [blockchain 구성원 트랜잭션 노드를 삭제](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete)합니다.

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **멤버 이름** | 삭제할 트랜잭션 노드 이름도 포함 하는 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |
| **name** | 삭제할 트랜잭션 노드 이름입니다. |

## <a name="delete-a-blockchain-member"></a>Blockchain 구성원 삭제

이 예에서는 [blockchain 멤버를 삭제](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete)합니다.

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 삭제할 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD 사용자에 대 한 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 매개 변수 | Description |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **assignee** | Azure AD 사용자 ID입니다. 예, `user@contoso.com` |
| **범위** | 역할 할당의 범위입니다. Blockchain 멤버 또는 트랜잭션 노드일 수 있습니다. |

**예:**

Azure AD 사용자에 대 한 노드 액세스를 blockchain **구성원**에 게 부여:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**예:**

Blockchain **트랜잭션 노드에**Azure AD 사용자에 대 한 노드 액세스를 부여 합니다.

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD 그룹 또는 응용 프로그램 역할에 대 한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 매개 변수 | Description |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **담당자-개체-id** | Azure AD 그룹 ID 또는 응용 프로그램 ID입니다. |
| **범위** | 역할 할당의 범위입니다. Blockchain 멤버 또는 트랜잭션 노드일 수 있습니다. |

**예:**

**응용 프로그램 역할** 에 대 한 노드 액세스 권한 부여

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

| 매개 변수 | Description |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **assignee** | Azure AD 사용자 ID입니다. 예, `user@contoso.com` |
| **범위** | 역할 할당의 범위입니다. Blockchain 멤버 또는 트랜잭션 노드일 수 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure Portal를 사용 하 여 [Azure Blockchain 서비스 트랜잭션 노드를 구성](configure-transaction-nodes.md)하는 방법을 알아봅니다.
