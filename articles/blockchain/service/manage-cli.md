---
title: Manage Azure Blockchain Service using Azure CLI
description: How to manage Azure Blockchain Service with Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455586"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Manage Azure Blockchain Service using Azure CLI

In addition to the Azure portal, you can use Azure CLI to manage blockchain members and transaction nodes for your Azure Blockchain Service.

Make sure that you have installed the latest [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) and logged in to an Azure account in with `az login`.

In the following examples, replace example `<parameter names>` with your own values.

## <a name="create-blockchain-member"></a>Create blockchain member

Example creates a blockchain member in Azure Blockchain Service that runs the Quorum ledger protocol in a new consortium.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. 예: `myblockchainmember.blockchain.azure.com` |
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. 예: `eastus` 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **암호** | 멤버 계정 암호 멤버 계정 암호는 기본 인증을 사용하여 블록체인 멤버의 공용 엔드포인트를 인증할 때 사용됩니다. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolumn(;)|
| **protocol** | Public preview supports Quorum. |
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 암호입니다. The password is used for joining a consortium. |
| **ruleName** | Rule name for whitelisting an IP address range. Optional parameter for firewall rules.|
| **startIpAddress** | Start of the IP address range for whitelisting. Optional parameter for firewall rules. |
| **endIpAddress** | End of the IP address range for whitelisting. Optional parameter for firewall rules. |
| **skuName** | 계층 유형입니다. 표준에는 S0를 사용하고, 기본에는 B0를 사용합니다. |

## <a name="change-blockchain-member-password"></a>Change blockchain member password

Example changes a blockchain member's password.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Name that identifies your Azure Blockchain Service member. |
| **암호** | 멤버 계정 암호 The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

Create a transaction node inside an existing blockchain member. By adding transaction nodes, you can increase security isolation and distribute load. For example, you could have a transaction node endpoint for different client applications.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. 예: `eastus` 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **암호** | The transaction node password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |
| **ruleName** | Rule name for whitelisting an IP address range. Optional parameter for firewall rules. |
| **startIpAddress** | Start of the IP address range for whitelisting. Optional parameter for firewall rules. |
| **endIpAddress** | End of the IP address range for whitelisting. Optional parameter for firewall rules.|

## <a name="change-transaction-node-password"></a>Change transaction node password

Example changes a transaction node password.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |
| **암호** | The transaction node password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Change consortium management account password

The consortium management account is used for consortium membership management. Each member is uniquely identified by a consortium management account and you can change the password of this account with the following command.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Name that identifies your Azure Blockchain Service member. |
| **consortiumManagementAccountPassword** | The consortium management account password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |
  
## <a name="update-firewall-rules"></a>Update firewall rules

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member. |
| **ruleName** | Rule name for whitelisting an IP address range. Optional parameter for firewall rules.|
| **startIpAddress** | Start of the IP address range for whitelisting. Optional parameter for firewall rules.|
| **endIpAddress** | End of the IP address range for whitelisting. Optional parameter for firewall rules.|

## <a name="list-api-keys"></a>List API keys

API keys can be used for node access similar to user name and password. There are two API keys to support key rotation. Use the following command to list your API keys.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |

## <a name="regenerate-api-keys"></a>Regenerate API keys

Use the following command to regenerate your API keys.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |
| **keyName** | Replace \<keyValue\> with either key1 or key2. |

## <a name="delete-a-transaction-node"></a>Delete a transaction node

Example deletes a blockchain member transaction node.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the transaction node name to be deleted. |

## <a name="delete-a-blockchain-member"></a>Delete a blockchain member

Example deletes a blockchain member.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member to be deleted. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Grant access for Azure AD user

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **role** | Name of the Azure AD role. |
| **assignee** | Azure AD user ID. 위치(예:`user@contoso.com` |
| **scope** | Scope of the role assignment. Can be either a blockchain member or transaction node. |

**예제:**

Grant node access for Azure AD user to blockchain **member**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**예제:**

Grant node access for Azure AD user to blockchain **transaction node**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Grant node access for Azure AD group or application role

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **role** | Name of the Azure AD role. |
| **assignee-object-id** | Azure AD group ID or application ID. |
| **scope** | Scope of the role assignment. Can be either a blockchain member or transaction node. |

**예제:**

Grant node access for **application role**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remove Azure AD node access

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 매개 변수를 포함해야 합니다. | 설명 |
|---------|-------------|
| **role** | Name of the Azure AD role. |
| **assignee** | Azure AD user ID. 위치(예:`user@contoso.com` |
| **scope** | Scope of the role assignment. Can be either a blockchain member or transaction node. |

## <a name="next-steps"></a>다음 단계

Learn how to [Configure Azure Blockchain Service transaction nodes with the Azure portal](configure-transaction-nodes.md).
