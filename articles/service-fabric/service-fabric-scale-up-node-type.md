---
title: Azure Service Fabric 노드 형식 스케일 업
description: Virtual Machine Scale Set을 추가하여 Service Fabric 클러스터를 확장하는 방법을 알아봅니다.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 2d700367049e0bf9bf710aad110c850a78c26220
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610696"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric 클러스터 주 노드 형식 강화
이 문서에서는 가상 머신 리소스를 증가시켜서 Service Fabric 클러스터 주 노드 형식을 강화하는 방법을 설명합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 컴퓨팅 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. Service Fabric 클러스터가 만들어지면 클러스터 노드 유형을 수직으로 확장하거나(노드의 리소스 변경) 노드 유형 VM의 운영 체제를 업그레이드할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

> [!WARNING]
> 클러스터 상태가 비정상 인 경우 주 노드 유형 확장 프로시저를 시도 하지 마십시오. 이렇게 하면 클러스터를 추가로 불안정 하 게 됩니다.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>기본 노드 유형 VM의 크기 및 운영 체제를 업그레이드하는 프로세스
주 노드 유형 VM의 VM 크기 및 운영 체제를 업데이트하는 프로세스는 다음과 같습니다.  업그레이드 후에 주 노드 유형 VM은 표준 D4_V2 크기이고 컨테이너가 있는 Windows Server 2016 Datacenter를 실행합니다.

> [!WARNING]
> 프로덕션 클러스터에서 이 절차를 시도하기 전에 먼저 샘플 템플릿을 알아보고 테스트 클러스터에 대한 프로세스를 확인하는 것이 좋습니다. 또한 클러스터도 잠시 사용할 수 없습니다. 병렬로 동일한 NodeType으로 선언된 여러 VMSS는 변경할 수 없습니다. 각 NodeType VMSS에 변경 내용을 개별적으로 적용하려면 별도의 배포 작업을 수행해야 합니다.

1. 이러한 [템플릿](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) 및 [매개 변수](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) 샘플 파일을 사용하여 노드 유형과 확장 집합(노드 유형당 하나의 확장 집합)이 각각 두 개씩 있는 초기 클러스터를 배포합니다.  두 개의 확장 집합은 모두 표준 D2_V2 크기이며 Windows Server 2012 R2 Datacenter를 실행합니다.  클러스터에서 기준 업그레이드를 완료할 때까지 기다리세요.   
2. 선택 사항 - 클러스터에 상태 저장 샘플을 배포합니다.
3. 주 노드 유형 VM을 업그레이드하도록 결정한 후 주 노드 유형에 두 개의 확장 집합이 있으므로 이러한 [템플릿](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) 및 [매개 변수](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) 샘플 파일을 사용하여 주 노드 유형에 새 확장 집합을 추가합니다.  시스템 서비스 및 사용자 애플리케이션은 두 개의 서로 다른 확장 집합의 VM 간에 마이그레이션할 수 있습니다.  새 확장 집합 VM은 표준 D4_V2 크기이며 Windows Server 2016 Datacenter with Containers를 실행합니다.  새 부하 분산 장치와 공용 IP 주소도 새 확장 집합과 함께 추가됩니다.  
    템플릿에서 새 확장 집합을 찾으려면 *vmNodeType2Name* 매개 변수로 명명된 "Microsoft.Compute/virtualMachineScaleSets" 리소스를 검색합니다.  새 확장 집합은 속성 -> virtualMachineProfile -> extensionProfile -> 확장 -> 속성 -> 설정 -> nodeTypeRef 설정을 차례로 사용하여 주 노드 유형에 추가됩니다.
4. 클러스터 상태를 확인하고 모든 노드가 정상인지 확인합니다.
5. 노드를 제거하려는 의도로 주 노드 유형의 이전 확장 집합에 있는 노드를 사용하지 않도록 설정합니다. 한 번에 모두 사용하지 않도록 설정할 수 있으며 작업이 큐에서 대기합니다. 약간의 시간이 걸릴 수도 있지만 모든 노드가 사용되지 않도록 설정될 때까지 기다리세요.  노드 유형의 이전 노드가 사용되지 않도록 설정되면 시스템 서비스 및 시드 노드에서 주 노드 유형에 설정된 새 확장 집합의 VM으로 마이그레이션합니다.
6. 주 노드 유형에서 이전 확장 집합을 제거합니다. (5단계에서와 같이 노드를 사용하지 않도록 설정한 후 Azure Portal의 가상 머신 확장 집합 블레이드에서 이전 노드 형식의 노드를 하나씩 할당 취소합니다.)
7. 이전의 확장 집합과 연결된 부하 분산 장치를 제거합니다. 새 확장 집합에 대한 새 공용 IP 주소 및 부하 분산 장치가 구성되는 동안에는 클러스터를 사용할 수 없습니다.  
8. 변수에 설정된 이전의 주 노드 유형 확장 집합과 연결된 공용 IP 주소의 DNS 설정을 저장하고 해당 공용 IP 주소를 제거합니다.
9. 새 주 노드 유형 확장 집합과 연결된 공용 IP 주소의 DNS 설정을 삭제된 공용 IP 주소의 DNS 설정으로 바꿉니다.  이제 클러스터에 다시 연결할 수 있습니다.
10. 클러스터에서 노드에 대한 노드 상태를 제거합니다.  이전의 확장 집합에 대한 내구성 수준이 은색 또는 금색으로 표시되면 시스템에서 이 단계를 자동으로 수행합니다.
11. 이전 단계에서 상태 저장 애플리케이션을 배포한 경우 애플리케이션이 작동하는지 확인합니다.

## <a name="set-up-the-test-cluster"></a>테스트 클러스터 설정

먼저 이 자습서에 필요한 두 가지 파일 세트(이전 [템플릿](), [매개 변수]() 및 이후 [템플릿](), [매개 변수]())를 다운로드합니다.

다음으로, Azure 계정에 로그인합니다.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

이 자습서에서는 자체 서명된 인증서를 만드는 시나리오를 안내합니다. Azure Key Vault에서 기존 인증서를 사용하려면 아래 단계를 건너뛰고 대신 [기존 인증서를 사용하여 클러스터 배포](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster)의 단계를 미러링합니다.

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>자체 서명된 인증서 생성 및 클러스터 배포

먼저 Service Fabric 클러스터 배포에 필요한 변수를 할당합니다. 특정 계정 및 환경에 대한 `resourceGroupName`, `certSubjectName`, `parameterFilePath` 및 `templateFilePath`의 값을 조정합니다.

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> 새 Service Fabric 클러스터를 배포하는 명령을 실행하기 전에 로컬 머신에 `certOutputFolder` 위치가 있는지 확인합니다.

다음으로, *Deploy-2NodeTypes-2ScaleSets.parameters.json* 파일을 열고 PowerShell에서 설정한 동적 값에 해당하는 `clusterName` 및 `dnsName` 값을 조정하고 변경 내용을 저장합니다.

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

이 작업은 새 클러스터에 연결하고 상태를 확인하는 데 사용하는 인증서 지문을 반환합니다.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>새 클러스터에 연결하고 성능 상태 확인

클러스터에 연결하고 모든 노드가 정상인지 확인합니다(클러스터의 `clusterName` 및 `thumb` 변수 대체).

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

업그레이드 절차를 시작할 준비가 되었습니다.

## <a name="upgrade-the-primary-node-type-vms"></a>기본 노드 형식 VM 업그레이드

기본 노드 유형 VM을 업그레이드하기로 결정한 후 기본 노드 유형에 두 개의 확장 집합이 포함되도록 새 확장 집합을 기본 노드 유형에 추가합니다. 필요한 변경 내용을 표시하기 위해 샘플 [템플릿](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) 및 [매개 변수](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) 파일이 제공되었습니다. 새 확장 집합의 VM은 표준 D4_V2 크기이며 Windows Server 2016 Datacenter with Containers를 실행합니다. 새 부하 분산 장치와 공용 IP 주소도 새 확장 집합과 함께 추가됩니다. 

템플릿에서 새 확장 집합을 찾으려면 vmNodeType2Name 매개 변수로 명명된 "Microsoft.Compute/virtualMachineScaleSets" 리소스를 검색합니다. 새 확장 집합은 속성 -> virtualMachineProfile -> extensionProfile -> 확장 -> 속성 -> 설정 -> nodeTypeRef 설정을 차례로 사용하여 주 노드 유형에 추가됩니다.

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포

필요에 따라 `parameterFilePath` 및 `templateFilePath`를 조정한 후, 다음 명령을 실행합니다.

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

배포가 완료되면 클러스터 상태를 다시 확인하고 원본 및 새 확장 집합의 모든 노드가 정상인지 확인합니다.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>노드를 새 확장 집합으로 마이그레이션

이제 원래 크기 집합의 노드 비활성화를 시작할 준비가 되었습니다. 이러한 노드가 비활성화되면 시스템 서비스 및 시드 노드가 기본 노드 유형으로 표시되기 때문에 새 확장 집합의 VM으로 마이그레이션됩니다.

주 노드가 아닌 노드 유형을 확장 하는 경우이 단계에서는 새 가상 머신 확장 집합/노드 유형을 포함 하도록 서비스 배치 제약 조건을 수정한 후 이전 가상 머신 확장 집합 인스턴스 수를 한 번에 한 번에 한 노드에서만 축소 하 여 노드 제거가 클러스터 안정성에 영향을 주지 않도록 합니다.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Service Fabric Explorer를 사용하여 시드 노드를 새 확장 집합으로 마이그레이션하고 원래 확장 집합의 노드 진행을 *사용하지 않도록 설정 중*에서 *사용 안 함* 상태로 모니터링합니다.

> [!NOTE]
> 원래 확장 집합의 모든 노드에서 비활성화 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다. 데이터 일관성을 보장하기 위해 한 번에 하나의 시드 노드만 변경할 수 있습니다. 각 시드 노드 변경에는 클러스터 업데이트가 필요합니다. 따라서 시드 노드를 교체하려면 두 개의 클러스터 업그레이드가 필요합니다 (노드 추가 및 제거 시 각각 하나씩). 이 샘플 시나리오에서 5개의 시드 노드를 업그레이드하면 10개의 클러스터 업그레이드가 발생합니다.

## <a name="remove-the-original-scale-set"></a>원래 확장 집합 제거

비활성화 작업이 완료되면 확장 집합을 제거합니다.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Service Fabric Explorer에서 제거된 노드(따라서 *Cluster Health State*)가 이제 *오류* 상태로 표시됩니다.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>이전 부하 분산 장치를 제거하고 DNS 설정을 업데이트합니다.

이제 부하 분산 장치 및 이전 공용 IP로 시작하여 이전 기본 노드 유형과 관련된 리소스를 제거할 수 있습니다. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

다음으로, 새 공용 IP의 DNS 설정을 업데이트하여 이전 기본 노드 형식의 공용 IP에서 설정을 미러링합니다.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

다시 한 번 클러스터 상태를 확인합니다.

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

마지막으로 관련된 각 노드에 대한 노드 상태를 제거합니다. 이전의 확장 집합에 대한 내구성 수준이 은색 또는 금색이면 자동으로 수행됩니다.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

이제 클러스터의 기본 노드 형식이 업그레이드되었습니다. 배포된 애플리케이션이 제대로 작동하고 클러스터 상태가 정상인지 확인합니다.

## <a name="next-steps"></a>다음 단계
* [클러스터에 노드 형식을 추가](virtual-machine-scale-set-scale-node-type-scale-out.md)하는 방법을 알아봅니다.
* [애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md).

