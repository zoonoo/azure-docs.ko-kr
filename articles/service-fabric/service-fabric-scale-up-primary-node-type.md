---
title: Azure Service Fabric 주 노드 유형 강화
description: 새 노드 유형을 추가 하 고 이전 노드 유형을 제거 하 여 Service Fabric 클러스터를 수직으로 확장 합니다.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251184"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric 클러스터 주 노드 형식 강화

이 문서에서는 가동 중지 시간을 최소화 하면서 Service Fabric 클러스터 주 노드 유형을 확장 하는 방법을 설명 합니다. Service Fabric 클러스터 노드 유형을 업그레이드 하는 일반적인 전략은 다음과 같습니다.

1. 업그레이드 (또는 수정 된) 가상 머신 확장 집합 SKU 및 구성에 의해 지원 되는 Service Fabric 클러스터에 새 노드 유형을 추가 합니다. 또한이 단계는 확장 집합에 대 한 새 부하 분산 장치, 서브넷 및 공용 IP를 설정 하는 작업을 포함 합니다.

1. 원본 및 업그레이드 된 확장 집합을 모두 나란히 실행 하는 경우에는 원래 노드 인스턴스를 한 번에 하나씩 사용 하지 않도록 설정 하 여 시스템 서비스 (또는 상태 저장 서비스의 복제본)를 새 확장 집합으로 마이그레이션합니다.

1. 클러스터 및 새 노드가 정상 인지 확인 하 고 삭제 된 노드에 대 한 원래 확장 집합 (및 관련 리소스) 및 노드 상태를 제거 합니다.

다음은 5 개의 노드가 포함 된 단일 확장 집합에 의해 지원 되는 [실버 내구성이](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)있는 샘플 클러스터의 주 노드 형식 VM의 vm 크기 및 운영 체제를 업데이트 하는 프로세스를 안내 합니다. 주 노드 유형을 업그레이드할 예정입니다.

- VM 크기에서 *표준 D4_V2* 로 *Standard_D2_V2*
- 컨테이너를 사용 하는 VM 운영 체제 *Windows server 2016 datacenter* 에서 컨테이너를 *사용 하는 Windows server 2019 datacenter* 로.

> [!WARNING]
> 프로덕션 클러스터에서 이 절차를 시도하기 전에 먼저 샘플 템플릿을 알아보고 테스트 클러스터에 대한 프로세스를 확인하는 것이 좋습니다. 클러스터를 짧은 시간 동안 사용할 수 없는 경우도 있습니다.
>
> 클러스터 상태가 비정상 인 경우 주 노드 유형 확장 프로시저를 시도 하지 마십시오. 이렇게 하면 클러스터를 추가로 불안정 하 게 됩니다.

다음은이 샘플 업그레이드 시나리오를 완료 하는 데 사용할 수 있는 단계별 Azure 배포 템플릿입니다. https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>테스트 클러스터 설정

초기 Service Fabric 테스트 클러스터를 설정 해 보겠습니다. 먼저이 시나리오를 완료 하는 데 사용할 Azure Resource Manager 샘플 템플릿을 [다운로드](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) 합니다.

다음으로, Azure 계정에 로그인합니다.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

그런 다음 파일 [*에서parameters.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) 를 열고의 값 `clusterName` 을 Azure 내에서 고유한 값으로 업데이트 합니다.

다음 명령은 자체 서명 된 새 인증서를 생성 하 고 테스트 클러스터를 배포 하는 과정을 안내 합니다. 사용 하려는 인증서가 이미 있는 경우 [기존 인증서를 사용](#use-an-existing-certificate-to-deploy-the-cluster)하 여 클러스터를 배포 하는 것으로 건너뜁니다.

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>자체 서명된 인증서 생성 및 클러스터 배포

먼저 Service Fabric 클러스터 배포에 필요한 변수를 할당합니다. 특정 계정 및 환경에 대한 `resourceGroupName`, `certSubjectName`, `parameterFilePath` 및 `templateFilePath`의 값을 조정합니다.

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> `certOutputFolder`새 Service Fabric 클러스터를 배포 하는 명령을 실행 하기 전에 로컬 컴퓨터에 위치가 있는지 확인 합니다.

그런 다음, Service Fabric 테스트 클러스트를 배포합니다.

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

배포가 완료되면 로컬 머신에서 *.pfx* 파일(`$certPfx`)을 찾아서 인증서 저장소로 가져옵니다.

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

작업은 이제 [새 클러스터에 연결](#connect-to-the-new-cluster-and-check-health-status) 하 고 상태를 확인 하는 데 사용할 수 있는 인증서 지문을 반환 합니다. 클러스터 배포에 대 한 대체 방법인 다음 섹션을 건너뜁니다.

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>기존 인증서를 사용 하 여 클러스터 배포

또는 기존 Azure Key Vault 인증서를 사용 하 여 테스트 클러스터를 배포할 수 있습니다. 이렇게 하려면 Key Vault 및 인증서 지문에 대 한 [참조를 가져와야](#obtain-your-key-vault-references) 합니다.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

다음으로 클러스터에 대 한 리소스 그룹 이름을 지정 하 고 및 위치를 설정 합니다 `templateFilePath` `parameterFilePath` .

> [!NOTE]
> 지정 된 리소스 그룹이 이미 존재 하 고 Key Vault와 동일한 지역에 있어야 합니다.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

마지막으로 다음 명령을 실행 하 여 초기 테스트 클러스터를 배포 합니다.

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>새 클러스터에 연결하고 성능 상태 확인

클러스터에 연결 하 고 5 개의 노드가 정상 상태 인지 확인 합니다 ( `clusterName` 및 `thumb` 변수를 고유한 값으로 대체).

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

그러면 업그레이드 절차를 시작할 준비가 된 것입니다.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>업그레이드 된 확장 집합을 사용 하 여 새 주 노드 유형 배포

노드 유형을 업그레이드 (수직 확장) 하기 위해 먼저 새 확장 집합 및 지원 리소스에 의해 지원 되는 새 노드 유형을 배포 해야 합니다. 새 확장 집합은 원래 확장 집합과 마찬가지로 기본 ()으로 표시 됩니다 `isPrimary: true` (주 노드 형식이 아닌 업그레이드를 수행 하지 않는 경우). 다음 섹션에서 만든 리소스는 궁극적으로 클러스터의 새 주 노드 유형이 되며 원래 주 노드 유형 리소스가 삭제 됩니다.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>업그레이드 된 확장 집합으로 클러스터 템플릿 업데이트

새 주 노드 유형 및 지원 리소스를 추가 하기 위한 원본 클러스터 배포 템플릿의 섹션 별 수정 사항은 다음과 같습니다.

이 단계에 필요한 변경 내용은 템플릿 파일 [*의Step1-AddPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) 에 이미 적용 되었으며, 다음은 이러한 변경 내용을 자세히 설명 합니다. 원하는 경우 설명을 건너뛰고 계속 해 서 [Key Vault 참조를 가져오고](#obtain-your-key-vault-references) 새 주 노드 형식을 클러스터에 추가 하는 [업데이트 된 템플릿을 배포할](#deploy-the-updated-template) 수 있습니다.

> [!Note]
> 원래 주 노드 형식의 원래 노드 유형, 확장 집합, 부하 분산 장치, 공용 IP 및 서브넷에서 고유한 이름을 사용 해야 합니다. 이러한 리소스는 프로세스의 이후 단계에서 삭제 됩니다.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>기존 가상 네트워크에서 새 서브넷 만들기

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>고유한 domainNameLabel를 사용 하 여 새 공용 IP 만들기

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>공용 IP에 대 한 새 부하 분산 장치 만들기

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>새 가상 머신 확장 집합 만들기 (업그레이드 된 VM 및 OS Sku 사용)

노드 유형 참조

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM SKU

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

또한 워크 로드에 필요한 추가 확장을 포함 해야 합니다.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>클러스터에 새 주 노드 형식 추가

이제 새 노드 형식 (vmNodeType1Name)에 고유한 이름, 서브넷, IP, 부하 분산 장치 및 확장 집합이 있으므로 원래 노드 형식 (예:, 및)의 다른 모든 변수를 재사용할 수 있습니다 `nt0applicationEndPort` `nt0applicationStartPort` `nt0fabricTcpGatewayPort` .

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

템플릿 및 매개 변수 파일의 모든 변경 내용을 구현한 후 다음 섹션으로 이동 하 여 Key Vault 참조를 가져오고 클러스터에 업데이트를 배포 합니다.

### <a name="obtain-your-key-vault-references"></a>Key Vault 참조 가져오기

업데이트 된 구성을 배포 하려면 Key Vault에 저장 된 클러스터 인증서에 대 한 몇 가지 참조가 필요 합니다. 이러한 값을 찾는 가장 쉬운 방법은 Azure Portal을 통하는 것입니다. 다음 도구가 필요합니다.

* **클러스터 인증서의 Key Vault URL입니다.** Azure Portal의 Key Vault에서   >  *원하는 인증서*  >  **비밀 식별자** 를 선택 합니다.

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **클러스터 인증서의 지문입니다.** [초기 클러스터에 연결](#connect-to-the-new-cluster-and-check-health-status) 하 여 상태를 확인할 수 있습니다. Azure Portal의 동일한 인증서 블레이드 (  >  *원하는 인증서* 의 인증서)에서 **x.509 sha-1 지문 (16 진수)** 을 복사 합니다.

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault의 리소스 ID입니다.** Azure Portal의 Key Vault에서 **속성**  >  **리소스 ID** 를 선택 합니다.

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포

필요에 따라를 조정 `templateFilePath` 하 고 다음 명령을 실행 합니다.

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

배포가 완료 되 면 클러스터 상태를 다시 확인 하 고 두 노드 유형의 모든 노드가 정상 상태 인지 확인 합니다.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>새 노드 형식으로 시드 노드 마이그레이션

이제 원래 노드 형식을 비기본로 업데이트 하 고 노드 비활성화를 시작할 준비가 되었습니다. 노드가 사용 하지 않도록 설정 되 면 클러스터의 시스템 서비스 및 시드 노드가 새 확장 집합으로 마이그레이션됩니다.

### <a name="unmark-the-original-node-type-as-primary"></a>원래 노드 유형을 기본으로 표시 해제

먼저 `isPrimary` 원래 노드 형식에서 템플릿의 지정을 제거 합니다.

```json
{
    "isPrimary": false,
}
```

그런 다음 업데이트를 사용 하 여 템플릿을 배포 합니다. 이렇게 하면 시드 노드를 새 확장 집합으로 마이그레이션하기 시작 합니다.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> 새 확장 집합으로 시드 노드 마이그레이션을 완료 하는 데 다소 시간이 걸립니다. 데이터 일관성을 보장하기 위해 한 번에 하나의 시드 노드만 변경할 수 있습니다. 각 시드 노드 변경에는 클러스터 업데이트가 필요합니다. 따라서 시드 노드를 교체하려면 두 개의 클러스터 업그레이드가 필요합니다 (노드 추가 및 제거 시 각각 하나씩). 이 샘플 시나리오에서 5개의 시드 노드를 업그레이드하면 10개의 클러스터 업그레이드가 발생합니다.

Service Fabric Explorer를 사용 하 여 seed 노드의 마이그레이션을 새 확장 집합으로 모니터링할 수 있습니다. 원본 노드 유형 (nt0vm)의 노드는 **Is 초기값 노드** 열에서 모두 *false* 가 되 고 새 노드 유형 (nt1vm)의 노드는 *true* 가 됩니다.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>원래 노드 형식 확장 집합에서 노드를 사용 하지 않도록 설정

모든 초기값 노드가 새 확장 집합으로 마이그레이션되면 원래 확장 집합의 노드를 사용 하지 않도록 설정할 수 있습니다.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer를 사용 하 여 원래 확장 집합의 노드를 사용 안 함 *상태에서 사용 안 함 상태로* 의 *진행 상태를* 모니터링할 수 있습니다.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="비활성 노드의 상태를 보여 주는 Service Fabric Explorer":::

실버 및 골드 내구성의 경우 일부 노드는 비활성화 상태로 전환 되 고 다른 노드는 *비활성화* 상태로 남아 있을 수 있습니다. Service Fabric Explorer에서 상태 비활성화 노드의 **세부 정보** 탭을 선택 합니다. Kind *EnsurePartitionQuorem* (인프라 서비스 파티션에 대 한 쿼럼 보장)의 *보안을 보류 중인* 것으로 표시 되는 경우 계속 하는 것이 안전 합니다.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="' EnsurePartitionQuorum ' 유형의 보류 중인 안전 검사를 표시 하는 경우 데이터를 중지 하 고 ' 비활성화 ' 상태에서 중단 된 노드 제거를 계속할 수 있습니다.":::

클러스터가 청동 내구성 인 경우 모든 노드가 *비활성화* 된 상태에 도달할 때까지 기다립니다.

### <a name="stop-data-on-the-disabled-nodes"></a>사용 하지 않도록 설정 된 노드에서 데이터 중지

이제 사용 하지 않도록 설정 된 노드에서 데이터를 중지할 수 있습니다.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>원래 노드 형식을 제거 하 고 해당 리소스를 정리 합니다.

수직 확장 프로시저를 종료 하기 위해 원래 노드 유형과 연결 된 리소스를 제거할 준비가 되었습니다.

### <a name="remove-the-original-scale-set"></a>원래 확장 집합 제거

먼저 노드 형식의 지원 확장 집합을 제거 합니다.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>원래 IP 및 부하 분산 장치 리소스를 삭제 합니다.

이제 원래 IP 및 부하 분산 장치 리소스를 삭제할 수 있습니다. 이 단계에서는 DNS 이름도 업데이트 합니다.

> [!Note]
> *표준* SKU 공용 IP 및 부하 분산 장치를 이미 사용 하 고 있는 경우이 단계는 선택 사항입니다. 이 경우 동일한 부하 분산 장치에 여러 확장 집합/노드 형식을 포함할 수 있습니다.

필요에 따라 값을 수정 하 여 다음 명령을 실행 합니다 `$lbname` .

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>원래 노드 형식에서 노드 상태 제거

이제 원래 노드 형식 노드에는 해당 **상태** 에 대 한 *오류가* 표시 됩니다. 클러스터에서 해당 노드 상태를 제거 합니다.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer은 이제 새 노드 형식 (nt1vm)의 5 개 노드만 반영 하 고 모든 상태 값은 *OK* 입니다. 클러스터 성능 상태에 계속 *오류가* 표시 됩니다. 최신 변경 내용을 반영 하 고 다시 배포 하는 템플릿을 업데이트 하 여 다음을 수정 합니다.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>새로 확장 된 주 노드 유형을 반영 하도록 배포 템플릿 업데이트

이 단계에 필요한 변경 내용은 템플릿 파일 [*의Step3-CleanupOriginalPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) 에 이미 적용 되었으며, 다음 섹션에서는 이러한 템플릿 변경 내용에 대해 자세히 설명 합니다. 원하는 경우 설명을 건너뛰고 [업데이트 된 템플릿을 계속 배포](#deploy-the-finalized-template) 하 고 자습서를 완료할 수 있습니다.

#### <a name="update-the-cluster-management-endpoint"></a>클러스터 관리 끝점 업데이트

`managementEndpoint`새 IP를 참조 하도록 배포 템플릿의 클러스터를 업데이트 합니다 ( *VmNodeType1Name* 로 *vmNodeType0Name* 를 업데이트 하 여).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>원래 노드 형식 참조를 제거 합니다.

배포 템플릿의 Service Fabric 리소스에서 원래 노드 형식 참조를 제거 합니다.

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>기존 오류를 무시 하도록 상태 정책 구성

실버 이상 내구성이 있는 클러스터의 경우에만 템플릿에서 클러스터 리소스를 업데이트 하 고 `fabric:/System` 아래 지정 된 대로 클러스터 리소스 속성 아래에 *applicationDeltaHealthPolicies* 를 추가 하 여 응용 프로그램 상태를 무시 하도록 상태 정책을 구성 합니다. 아래 정책은 기존 오류를 무시 하지만 새 상태 오류는 허용 하지 않습니다.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>원본 노드 형식에 대 한 지원 리소스 제거

ARM 템플릿 및 매개 변수 파일에서 원래 노드 유형과 관련 된 다른 모든 리소스를 제거 합니다. 다음 내용을 삭제합니다.

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>완성 된 템플릿 배포

마지막으로 수정 된 Azure Resource Manager 템플릿을 배포 합니다.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> 이 단계는 시간이 오래 걸릴 수 있으며, 일반적으로 최대 2 시간까지 소요 됩니다.

업그레이드 하면 설정이 *InfrastructureService* 로 변경 됩니다. 따라서 노드를 다시 시작 해야 합니다. 이 경우 *forceRestart* 은 무시 됩니다. 매개 변수는 `upgradeReplicaSetCheckTimeout` 아직 안전 상태가 아닌 경우 파티션이 안전한 상태가 될 때까지 Service Fabric 대기 하는 최대 시간을 지정 합니다. 안전 검사가 노드의 모든 파티션에 대해 통과 하면 Service Fabric는 해당 노드에서의 업그레이드를 진행 합니다. 매개 변수의 값은 `upgradeTimeout` 6 시간으로 줄일 수 있지만 최대 보안을 12 시간으로 사용 해야 합니다.

배포가 완료 되 면 Service Fabric 리소스 상태가 *준비* Azure Portal를 확인 합니다. 새 Service Fabric Explorer 끝점에 연결할 수 있는지 확인 하 고, **클러스터 상태가** *양호 인지 확인* 하 고, 배포 된 응용 프로그램이 제대로 작동 하는지 확인 합니다.

이를 통해 클러스터 주 노드 유형을 수직으로 확장 했습니다.

## <a name="next-steps"></a>다음 단계

* [클러스터에 노드 형식을 추가](virtual-machine-scale-set-scale-node-type-scale-out.md)하는 방법을 알아봅니다.
* [애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md).
