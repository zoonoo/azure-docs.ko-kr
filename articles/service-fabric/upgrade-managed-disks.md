---
title: Azure 관리 디스크를 사용하도록 클러스터 노드 업그레이드
description: 클러스터가동 중지 시간이 거의 또는 전혀 없는 Azure 관리 디스크를 사용하도록 기존 Service Fabric 클러스터를 업그레이드하는 방법은 다음과 같습니다.
ms.topic: how-to
ms.date: 3/01/2020
ms.openlocfilehash: 2bda5572eda5579bb31c3613b220885f27220d99
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758054"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Azure 관리 디스크를 사용하도록 클러스터 노드 업그레이드

[Azure 관리 디스크는](../virtual-machines/windows/managed-disks-overview.md) 데이터의 영구 저장소를 위해 Azure 가상 컴퓨터와 함께 사용할 수 있는 권장 디스크 저장소입니다. 노드 유형의 기초가 되는 가상 시스템 크기 집합을 업그레이드하여 관리되는 디스크를 사용하여 서비스 패브릭 워크로드의 복원력을 향상시킬 수 있습니다. 클러스터가동 중지 시간이 거의 또는 전혀 없는 Azure 관리 디스크를 사용하도록 기존 Service Fabric 클러스터를 업그레이드하는 방법은 다음과 같습니다.

관리되는 디스크를 사용하도록 서비스 패브릭 클러스터 노드를 업그레이드하는 일반적인 전략은 다음과 같은 것입니다.

1. 해당 노드 유형의 중복 가상 시스템 스케일 집합을 배포하지만 관리 `osDisk` [디스크](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) 개체가 가상 시스템 규모 집합 배포 템플릿섹션에 추가된 경우 새 스케일 집합은 고객이 마이그레이션 하는 동안 서비스 중단을 경험 하지 않도록 원래와 동일한 로드 밸러버/IP에 바인딩해야 합니다.

2. 원래 및 업그레이드된 축척 집합이 나란히 실행되면 시스템 서비스(또는 상태 확장 서비스의 복제본)가 새 축척 집합으로 마이그레이션되도록 원래 노드 인스턴스를 한 번에 하나씩 비활성화합니다.

3. 클러스터와 새 노드가 정상인지 확인한 다음 삭제된 노드의 원래 축척 집합 및 노드 상태를 제거합니다.

이 문서에서는 클러스터 가동 중지 시간을 피하면서 관리되는 디스크를 사용하도록 예제 클러스터의 기본 노드 유형을 업그레이드하는 단계를 안내합니다(아래 참고 참조). 예제 테스트 클러스터의 초기 상태는 5개의 노드가 있는 단일 스케일 집합으로 뒷받침되는 하나의 노드 유형의 [Silver 내구성으로](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)구성됩니다.

> [!CAUTION]
> 클러스터 DNS에 종속성이 있는 경우에만 이 프로시저를 통해 중단이 발생합니다(예: [서비스 패브릭 탐색기에](service-fabric-visualizing-your-cluster.md)액세스하는 경우). [프런트 엔드 서비스에 대한](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) 아키텍처 모범 사례는 중단 없이 노드 스와핑을 가능하게 하기 위해 노드 유형 앞에 일종의 로드 [밸런서를](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) 두는 것입니다.

업그레이드 시나리오를 완료하는 데 사용할 Azure 리소스 관리자용 [템플릿 및 cmdlet은](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) 다음과 같습니다. 템플릿 변경 사항은 [아래의 기본 노드 유형에 대해 업그레이드된 축척 집합 배포에서](#deploy-an-upgraded-scale-set-for-the-primary-node-type) 설명합니다.

## <a name="set-up-the-test-cluster"></a>테스트 클러스터 설정

초기 서비스 패브릭 테스트 클러스터를 설정해 보겠습니다. 먼저 이 시나리오를 완료하는 데 사용할 Azure 리소스 관리자 샘플 템플릿을 [다운로드합니다.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

다음으로 Azure 계정에 로그인합니다.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

다음 명령은 새 자체 서명된 인증서를 생성하고 테스트 클러스터를 배포하는 데 안내합니다. 사용하려는 인증서가 이미 있는 경우 [기존 인증서를 사용하여 클러스터를 배포합니다.](#use-an-existing-certificate-to-deploy-the-cluster)

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>자체 서명된 인증서를 생성하고 클러스터 배포

먼저 Service Fabric 클러스터 배포에 필요한 변수를 할당합니다. 및 특정 `resourceGroupName`계정 `certSubjectName` `parameterFilePath`및 `templateFilePath` 환경에 대한 값을 조정합니다.

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> 새 Service `certOutputFolder` Fabric 클러스터를 배포하는 명령을 실행하기 전에 로컬 컴퓨터에 위치가 있는지 확인합니다.

다음으로 [*Initial-1NodeType-관리되지 않는 디스크.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) 파일을 열고 `clusterName` PowerShell에서 설정한 동적 값에 해당하는 값을 조정하고 `dnsName` 변경 내용을 저장합니다.

그런 다음 서비스 패브릭 테스트 클러스터를 배포합니다.

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

배포가 완료되면 로컬 컴퓨터에서 *.pfx* `$certPfx`파일()을 찾아 인증서 저장소로 가져옵니다.

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

이 작업은 [새 클러스터에 연결하고](#connect-to-the-new-cluster-and-check-health-status) 상태를 확인하는 데 사용할 인증서 지문이 반환됩니다. 클러스터 배포에 대한 대체 접근 방식인 다음 섹션을 건너뜁니다.

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>기존 인증서를 사용하여 클러스터 배포

기존 Azure Key Vault 인증서를 사용하여 테스트 클러스터를 배포할 수도 있습니다. 이렇게 하려면 Key Vault 및 인증서 [지문에 대한 참조를 얻어야](#obtain-your-key-vault-references) 합니다.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

[*Initial-1NodeType-관리되지 않는 디스크.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) 파일을 열고 고유한 `clusterName` `dnsName` 값을 변경합니다.

마지막으로 클러스터에 대한 리소스 그룹 이름을 지정하고 `templateFilePath` `parameterFilePath` *Initial-1NodeType-관리되지 않는 Disks* 파일의 및 위치를 설정합니다.

> [!NOTE]
> 지정된 리소스 그룹은 이미 존재해야 하며 키 자격 증명 모음과 동일한 지역에 있어야 합니다.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

마지막으로 다음 명령을 실행하여 초기 테스트 클러스터를 배포합니다.

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>새 클러스터에 연결하고 상태를 확인합니다.

클러스터에 연결하고 5개의 노드가 모두 정상인지 확인합니다(클러스터의 `clusterName` `thumb` 변수 교체).

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

이를 통해 업그레이드 절차를 시작할 준비가 되었습니다.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>기본 노드 유형에 대해 업그레이드된 스케일 세트 배포

노드 유형을 업그레이드하거나 *수직으로 확장하려면*원하는 업그레이드/변경 및 자체 별도의 서브넷 및 인바운드 NAT 주소 풀을 포함한다는 점을 제외하면 `nodeTypeRef`원래 `subnet`축척 집합과 `loadBalancerBackendAddressPools`동일한 노드 유형의 가상 시스템 집합 의 복사본을 배포해야 합니다. 기본 노드 유형을 업그레이드하기 때문에 새 축척 집합은 원래`isPrimary: true`축척 집합과 마찬가지로 기본() 집합으로 표시됩니다. 기본 노드 유형이 아닌 업그레이드의 경우 생략하기만 하면 됩니다.

편의를 위해 *업그레이드-1NodeType-2ScaleSet-ManagedDisk* [템플릿](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) 및 [매개 변수](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) 파일에서 필요한 변경 사항이 이미 변경되었습니다.

다음 섹션에서는 템플릿 변경 사항을 자세히 설명합니다. 원하는 경우 설명을 건너뛰고 [업그레이드 절차의 다음 단계로](#obtain-your-key-vault-references)계속할 수 있습니다.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>업그레이드된 축척 세트로 클러스터 템플릿 업데이트

기본 노드 유형에 대해 업그레이드된 축척 집합을 추가하기 위한 원래 클러스터 배포 템플릿의 섹션별 수정 사항은 다음과 같습니다.

#### <a name="parameters"></a>매개 변수

새 축척 집합의 인스턴스 이름, 개수 및 크기에 대한 매개변수를 추가합니다. `vmNodeType1Name` 개수 및 크기 값은 원래 축척 집합과 동일하면서 새 축척 집합에 고유합니다.

**템플릿 파일**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**매개 변수 파일**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>variables

배포 템플릿 `variables` 섹션에서 새 축척 집합의 인바운드 NAT 주소 풀에 대한 항목을 추가합니다.

**템플릿 파일**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>리소스

배포 템플릿 *리소스* 섹션에서 새 가상 컴퓨터 크기 집합을 추가하여 다음 사항을 염두에 두어야 합니다.

* 새 축척 세트는 원본과 동일한 노드 유형을 참조합니다.

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 새 축척 집합은 동일한 로드 밸런서 백엔드 주소 및 서브넷을 참조하지만 다른 로드 밸런서 인바운드 NAT 풀을 사용합니다.

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* 원래 축척 집합과 마찬가지로 새 축척 집합은 기본 노드 유형으로 표시됩니다. (기본 노드가 아닌 노드 유형을 업그레이드할 때는 이 변경을 생략합니다.)

    ```json
    "isPrimary": true,
    ```

* 원래 축척 집합과 달리 새 축척 집합은 관리디스크를 사용하도록 업그레이드됩니다.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

템플릿 및 매개 변수 파일의 모든 변경 내용을 구현한 후에는 다음 섹션으로 이동하여 Key Vault 참조를 획득하고 클러스터에 업데이트를 배포합니다.

### <a name="obtain-your-key-vault-references"></a>키 볼트 참조 받기

업데이트된 구성을 배포하려면 먼저 Key Vault에 저장된 클러스터 인증서에 대한 몇 가지 참조를 얻습니다. 이러한 값을 찾는 가장 쉬운 방법은 Azure 포털을 사용하는 것입니다. 필요한 사항:

* **클러스터 인증서의 키 볼트 URL입니다.** Azure 포털의 키 자격 증명 모음에서*원하는* >  **인증서** > **보안 식별자를**인증서선택합니다.

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **클러스터 인증서의 지문입니다.** (초기 [클러스터에 연결한](#connect-to-the-new-cluster-and-check-health-status) 경우 상태를 확인하기 위해 이미 이 문제가 있을 수 있습니다.) Azure 포털에서 동일한 인증서 블레이드 **(인증서** > *원하는 인증서)에서* **X.509 SHA-1 지문 (육각)을**복사하십시오.

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **키 볼트의 리소스 ID입니다.** Azure 포털의 키 자격 증명 모음에서 **속성** > **리소스 ID를**선택합니다.

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포

`parameterFilePath` 필요에 `templateFilePath` 따라 다음 명령을 실행합니다.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

배포가 완료되면 클러스터 상태를 다시 확인하고 10개 노드(원래 노드5개, 새 축척 집합의 5개)가 정상인지 확인합니다.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>시드 노드를 새 축척 세트로 마이그레이션

이제 원래 축척 집합의 노드를 비활성화할 준비가 되었습니다. 이러한 노드가 비활성화되면 시스템 서비스 및 시드 노드는 기본 노드 유형으로 표시되기 때문에 새 축척 집합의 VM으로 마이그레이션됩니다.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

서비스 패브릭 탐색기를 사용하여 시드 노드가 새 축척 집합으로 마이그레이션되고 *원래* 배율 집합에서 *비활성화* 상태로 설정된 노드의 진행을 모니터링합니다.

![서비스 패브릭 탐색기 비활성화된 노드의 상태를 표시](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 원래 축척 집합의 모든 노드에서 비활성화 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다. 데이터 일관성을 보장하기 위해 한 번에 하나의 시드 노드만 변경할 수 있습니다. 각 시드 노드를 변경하려면 클러스터 업데이트가 필요합니다. 따라서 시드 노드를 교체하려면 두 개의 클러스터 업그레이드(노드 추가 및 제거를 위해 각각 하나씩)가 필요합니다. 이 샘플 시나리오에서 5개의 시드 노드를 업그레이드하면 10개의 클러스터 업그레이드가 발생합니다.

## <a name="remove-the-original-scale-set"></a>원래 축척 세트 제거

비활성화 작업이 완료되면 배율 집합을 제거합니다.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

서비스 패브릭 탐색기에서 제거된 노드(따라서 *클러스터 상태)가* *이제 오류* 상태로 표시됩니다.

![서비스 패브릭 탐색기 오류 상태에 비활성화된 노드표시](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

서비스 패브릭 클러스터에서 사용되지 않는 노드를 제거하여 클러스터 상태를 *정상으로*복원합니다.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![오류 상태의 다운 노드가 제거된 서비스 패브릭 탐색기](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>다음 단계

이 연습에서는 서비스 패브릭 클러스터의 가상 시스템 규모 집합을 업그레이드하여 관리디스크를 사용하는 동시에 프로세스 중에 서비스 중단을 방지하는 방법을 배웠습니다. 관련 항목에 대한 자세한 내용은 다음 리소스를 확인하십시오.

방법 배우기:

* [Service Fabric 클러스터 주 노드 형식 강화](service-fabric-scale-up-node-type.md)

* [관리 디스크를 사용하도록 축척 세트 템플릿 변환](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Service Fabric 노드 유형 제거](service-fabric-how-to-remove-node-type.md)

참고 항목:

* [샘플: Azure 관리 디스크를 사용하도록 클러스터 노드업그레이드](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

* [수직 크기 조정 관련 고려 사항](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)