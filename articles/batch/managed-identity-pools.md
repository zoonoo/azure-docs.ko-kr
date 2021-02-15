---
title: Batch 풀에서 관리 되는 id 구성
description: Batch 풀에서 사용자 할당 관리 id를 사용 하도록 설정 하는 방법 및 노드 내에서 관리 되는 id를 사용 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: references_regions
ms.openlocfilehash: 4a59383d119f88bb3ee180f629ba0a6ea6ac2f44
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417620"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Batch 풀에서 관리 되는 id 구성

[Azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하면 개발자가 azure ad (Azure Active Directory)에서 azure 리소스에 대 한 id를 제공 하 고이를 사용 하 여 Azure Active Directory (azure ad) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

이 항목에서는 Batch 풀에서 사용자 할당 관리 id를 사용 하도록 설정 하는 방법 및 노드 내에서 관리 되는 id를 사용 하는 방법에 대해 설명 합니다.

> [!IMPORTANT]
> 사용자 할당 관리 id를 사용 하는 Azure Batch 풀에 대 한 지원은 현재 미국 서 부 2, 미국 동부, 미국 동부, US Gov 애리조나 및 US Gov 버지니아 지역에 대해 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

먼저 Batch 계정과 동일한 테 넌 트에서 [사용자 할당 관리 id를 만듭니다](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) . 이 관리 되는 id는 동일한 리소스 그룹에 있거나 동일한 구독에 있을 필요는 없습니다.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>사용자 할당 관리 id를 사용 하 여 Batch 풀 만들기

하나 이상의 사용자 할당 관리 id를 만든 후에는 [batch .net 관리 라이브러리](/dotnet/api/overview/azure/batch#management-library)를 사용 하 여 해당 관리 id를 사용 하 여 batch 풀을 만들 수 있습니다.

> [!IMPORTANT]
> 관리 id를 사용 하려면 [가상 컴퓨터 구성을](nodes-and-pools.md#virtual-machine-configuration) 사용 하 여 풀을 구성 해야 합니다.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> 관리 id를 사용 하 여 풀을 만드는 작업은 현재 [Batch .net 클라이언트 라이브러리](/dotnet/api/overview/azure/batch#client-library)에서 지원 되지 않습니다.

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>일괄 처리 노드에서 사용자 할당 관리 id 사용

풀을 만든 후에는 사용자 할당 관리 id가 SSH (Secure Shell) 또는 RDP (원격 데스크톱)를 통해 풀 노드에 액세스할 수 있습니다. 관리 id가 관리 되는 [id를 지 원하는 Azure 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)에 직접 액세스할 수 있도록 작업을 구성할 수도 있습니다.

일괄 처리 노드 내에서 관리 되는 id 토큰을 가져와 [azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)를 통해 azure AD 인증을 통해 인증 하는 데 사용할 수 있습니다.

Windows의 경우 인증을 위해 액세스 토큰을 가져오는 PowerShell 스크립트는 다음과 같습니다.

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Linux의 경우 Bash 스크립트는 다음과 같습니다.

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

자세한 내용은 [AZURE VM에서 azure 리소스에 대해 관리 되는 id를 사용 하 여 액세스 토큰을 가져오는 방법](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.
- [사용자 관리 id에서 고객 관리 키](batch-customer-managed-key.md)를 사용 하는 방법에 대해 알아봅니다.
