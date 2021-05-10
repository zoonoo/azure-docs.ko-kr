---
title: 배치 풀의 관리 ID 구성
description: 배치 풀에서 사용자가 할당한 관리 ID를 사용하도록 설정하는 방법과 노드 내에서 관리 ID를 사용하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: d69e983a4b17298150942c924a3c694e2cceaf72
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967254"
---
# <a name="configure-managed-identities-in-batch-pools"></a>배치 풀의 관리 ID 구성

[Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 개발자가 Azure AD(Azure Active Directory)에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD(Azure Active Directory) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

이 항목에서는 배치 풀에서 사용자가 할당한 관리 ID를 사용하도록 설정하는 방법과 노드 내에서 관리 ID를 사용하는 방법에 대해 설명합니다.

> [!IMPORTANT]
> 사용자가 할당한 관리 ID를 가진 Azure Batch 풀에 대한 지원은 현재 미국 서부 2, 미국 중남부, 미국 동부, US Gov 애리조나, US Gov 버지니아 지역에 대해 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

먼저 배치 계정과 동일한 테넌트에서 [사용자가 할당한 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 수행합니다. 이 관리 ID는 동일한 리소스 그룹 또는 동일한 구독에 있을 필요는 없습니다.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>사용자가 할당한 관리 ID를 사용하여 배치 풀 만들기

하나 이상의 사용자가 할당한 관리 ID를 만든 후에는 [Batch .NET 관리 라이브러리](/dotnet/api/overview/azure/batch#management-library)를 사용하여 해당 관리 ID로 배치 풀을 만들 수 있습니다.

> [!IMPORTANT]
> 관리 ID를 사용하려면 [가상 머신 구성](nodes-and-pools.md#virtual-machine-configuration)을 사용하여 풀을 구성해야 합니다.

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
        },
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
> 관리 ID를 사용하여 풀을 만드는 작업은 현재 [Batch .NET 클라이언트 라이브러리](/dotnet/api/overview/azure/batch#client-library)에서 지원되지 않습니다.

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>배치 노드에서 사용자가 할당한 관리 ID 사용

풀을 만든 후에는 사용자가 할당한 관리 ID가 SSH(Secure Shell) 또는 RDP(원격 데스크톱)를 통해 풀 노드에 액세스할 수 있습니다. 관리 ID가 [관리 ID를 지원하는 Azure 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)에 직접 액세스할 수 있도록 작업을 구성할 수도 있습니다.

배치 노드 내에서 관리 ID 토큰을 가져와 [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)를 사용하여 Azure AD 인증을 통해 인증할 수 있습니다.

Windows의 경우 인증할 액세스 토큰을 가져오는 PowerShell 스크립트는 다음과 같습니다.

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Linux의 경우 Bash 스크립트는 다음과 같습니다.

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

자세한 내용은 [Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하여 액세스 토큰을 획득하는 방법](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아봅니다.
- [사용자 관리 ID에서 고객 관리형 키](batch-customer-managed-key.md)를 사용하는 방법에 대해 알아봅니다.
- [배치 풀에서 자동 인증서 순환을 사용하도록 설정](automatic-certificate-rotation.md)하는 방법에 대해 알아봅니다.
