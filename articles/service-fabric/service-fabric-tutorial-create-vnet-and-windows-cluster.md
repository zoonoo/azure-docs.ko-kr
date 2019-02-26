---
title: Azure에서 Windows Service Fabric 클러스터 만들기 | Microsoft Docs
description: 이 자습서에서는 PowerShell을 사용하여 Azure 가상 네트워크 및 네트워크 보안 그룹에 Windows Service Fabric 클러스터를 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 590e1e5853ccf4a525477f194c78f1fd8ce679ed
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453072"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>자습서: Azure 가상 네트워크에 Service Fabric Windows 클러스터 배포

이 자습서는 시리즈의 1부입니다. PowerShell 및 템플릿을 사용하여 [Azure VNET(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 및 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)에 Windows를 실행 중인 Service Fabric 클러스터를 배포하는 방법을 알아봅니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다.  Azure CLI를 사용하여 Linux 클러스터를 만들려면 [Azure에서 보안 Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

이 자습서는 프로덕션 시나리오를 설명합니다.  테스트 목적으로 더 작은 클러스터를 빠르게 만들려면 [테스트 클러스터 만들기](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * PowerShell을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * Azure Active Directory 인증 설정
> * Azure PowerShell에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 보안 클러스터 만들기
> * [클러스터 규모 확장 또는 규모 감축](service-fabric-tutorial-scale-cluster.md)
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * [클러스터 삭제](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.
* [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)을 설치합니다.
* [Azure 클러스터](service-fabric-azure-clusters-overview.md)에 대한 주요 개념을 검토합니다.

다음 절차에서는 7개 노드 Service Fabric 클러스터를 만듭니다. Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="download-and-explore-the-template"></a>템플릿 다운로드 및 탐색

다음 Resource Manager 템플릿 파일을 다운로드합니다.

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

이 템플릿은 7개 가상 머신 및 3개 노드 유형의 보안 클러스터를 가상 네트워크 및 네트워크 보안 그룹에 배포합니다.  다른 예제 템플릿은 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates)에 있을 수 있습니다.  [azuredeploy.json][template]은 다음을 포함한 숫자 리소스를 배포합니다.

### <a name="service-fabric-cluster"></a>Service Fabric 클러스터

**Microsoft.ServiceFabric/clusters** 리소스에서 Windows 클러스터가 다음과 같은 특성으로 구성됩니다.

* 3개 노드 유형
* 기본 노드 유형의 5개 노드(템플릿 매개 변수에서 구성 가능), 다른 두 노드 유형 각각의 1개 노드
* OS: 컨테이너가 있는 Windows Server 2016 Datacenter(템플릿 매개 변수에서 구성 가능)
* 보안된 인증서(템플릿 매개 변수에서 구성 가능)
* [역방향 프록시](service-fabric-reverseproxy.md) 사용
* [DNS 서비스](service-fabric-dnsservice.md) 사용
* 브론즈의 [내구성 수준](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
* 실버의 [안정성 수준](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
* 클라이언트 연결 엔드포인트: 19000(템플릿 매개 변수에서 구성 가능)
* HTTP 게이트웨이 엔드포인트: 19080(템플릿 매개 변수에서 구성 가능)

### <a name="azure-load-balancer"></a>Azure Load Balancer

**Microsoft.Network/loadBalancers** 리소스에서 부하 분산 장치가 구성되고 다음 포트에 대한 프로브 및 규칙이 설정됩니다.

* 클라이언트 연결 엔드포인트: 19000
* HTTP 게이트웨이 엔드포인트: 19080
* 애플리케이션 포트: 80
* 애플리케이션 포트: 443
* Service Fabric 역방향 프록시: 19081

다른 애플리케이션 포트가 필요한 경우 트래픽을 허용하도록 **Microsoft.Network/loadBalancers** 리소스 및 **Microsoft.Network/networkSecurityGroups** 리소스를 조정해야 합니다.

### <a name="virtual-network-subnet-and-network-security-group"></a>가상 네트워크, 서브넷 및 네트워크 보안 그룹

가상 네트워크, 서브넷 및 네트워크 보안 그룹의 이름은 템플릿 매개 변수에서 선언됩니다.  가상 네트워크 및 서브넷의 주소 공간도 템플릿 매개 변수로 선언되고 **Microsoft.Network/virtualNetworks** 리소스에 구성됩니다.

* 가상 네트워크 주소 공간: 172.16.0.0/20
* Service Fabric 서브넷 주소 공간: 172.16.2.0/23

**Microsoft.Network/networkSecurityGroups** 리소스에서 다음 인바운드 트래픽 규칙을 사용하도록 설정할 수 있습니다. 템플릿 변수를 변경하여 포트 값을 변경할 수 있습니다.

* ClientConnectionEndpoint(TCP): 19000
* HttpGatewayEndpoint(HTTP/TCP): 19080
* SMB: 445
* Internodecommunication - 1025, 1026, 1027
* 임시 포트 범위 – 49152~65534(최소 256 포트 필요)
* 애플리케이션 사용에 대한 포트: 80 및 443
* 애플리케이션 포트 범위 – 49152~65534(서비스 간 통신에 사용되며 부하 분산 장치에서 열리지 않음)
* 다른 모든 포트 차단

다른 애플리케이션 포트가 필요한 경우 트래픽을 허용하도록 **Microsoft.Network/loadBalancers** 리소스 및 **Microsoft.Network/networkSecurityGroups** 리소스를 조정해야 합니다.

### <a name="windows-defender"></a>Windows Defender
[Windows Defender 바이러스 백신](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)은 기본적으로 Windows Server 2016에 설치되어 작동합니다. 사용자 인터페이스가 기본적으로 일부 SKU에 설치되지만 필요하지는 않습니다.  템플릿에 선언된 각 노드 유형/VM 확장 집합에 대해 [Azure VM 맬웨어 방지 확장](/azure/virtual-machines/extensions/iaas-antimalware-windows)을 사용하여 Service Fabric 디렉터리 및 프로세스를 제외합니다.

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>템플릿 매개 변수 설정

[azuredeploy.parameters.json][parameters] 매개 변수 파일은 클러스터 및 연결된 리소스를 배포하는 데 사용되는 많은 값을 선언합니다. 배포에 대해 수정이 필요할 수도 있는 매개 변수 중 일부는 다음과 같습니다.

|매개 변수|예제 값|메모|
|---|---||
|adminUserName|vmadmin| 클러스터 VM에 대한 관리자 사용자 이름입니다.[VM의 사용자 이름 요구 사항](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| 클러스터 VM에 대한 관리자 암호입니다. [VM의 암호 요구 사항](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| 클러스터의 이름입니다. 문자와 숫자만 포함할 수 있습니다. 길이는 3자에서 23자 사이일 수 있습니다.|
|location|southcentralus| 클러스터의 위치입니다. |
|certificateThumbprint|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 SHA1 지문 값을 입력합니다. 예를 들면 “6190390162C988701DB5676EB81083EA608DCCF3”과 같습니다.</p>. |
|certificateUrlValue|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다. </p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 URL을 입력합니다. 예: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 원본 자격 증명 모음 값을 입력합니다. 예를 들면 “/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”와 같습니다.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory 클라이언트 인증 설정
Azure에서 호스팅되는 공용 네트워크에 배포된 Service Fabric 클러스터의 경우 클라이언트-노드 상호 인증에 추천되는 사항은 다음과 같습니다.
* 클라이언트 ID에 Azure Active Directory 사용
* http 통신의 서버 ID 및 SSL 암호화에 대한 인증서

[클러스터를 만들기](#createvaultandcert) 전에 Service Fabric 클러스터에 대한 클라이언트를 인증하도록 Azure AD를 설정해야 합니다.  조직(테넌트)에서는 Azure AD를 사용하여 애플리케이션에 대한 사용자 액세스를 관리할 수 있습니다. 

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 및 [Visual Studio](service-fabric-manage-application-in-visual-studio.md)를 포함하여 관리 기능에 대한 몇 가지 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 애플리케이션(웹 애플리케이션과 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다.  애플리케이션을 만든 후 읽기 전용 및 관리자 역할에 사용자를 할당합니다.

> [!NOTE]
> 클러스터를 만들기 전에 다음 단계를 완료해야 합니다. 스크립트는 클러스터 이름과 끈점을 예상하므로 이 값을 계획해야 하며, 이 값은 이미 만든 값이 아닙니다.

이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그렇지 않은 경우 [Azure Active Directory 테넌트를 가져오는 방법](../active-directory/develop/quickstart-create-new-tenant.md)을 참조하세요.

Service Fabric 클러스터로 Azure AD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다. 컴퓨터에 [스크립트를 다운로드](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)합니다.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD 애플리케이션 만들기 및 역할에 사용자 할당
두 개의 Azure AD 애플리케이션(웹 애플리케이션 및 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다. 클러스터를 나타내는 애플리케이션이 만들어지면 사용자를 [Service Fabric에서 지원하는 역할](service-fabric-cluster-security-roles.md)(읽기 전용 및 관리자)에 할당합니다.

`SetupApplications.ps1`을 실행하고 테넌트 ID, 클러스터 이름 및 웹 애플리케이션 회신 URL을 매개 변수로 제공합니다.  또한 사용자에 대한 사용자 이름과 암호를 지정합니다.  예: 

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 국가별 클라우드(예: Azure Government, Azure 중국, Azure 독일)의 경우 `-Location` 매개 변수도 지정해야 합니다.

*TenantId* 또는 디렉터리 ID는 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다. **Azure Active Directory -> 속성**을 차례로 선택하고, **디렉터리 ID** 값을 복사합니다.

*ClusterName*은 스크립트로 만든 Azure AD 애플리케이션의 접두사로 사용됩니다. 실제 클러스터 이름과 정확히 일치할 필요는 없습니다. 단순히 Azure AD 아티팩트를, 함께 사용할 Service Fabric 패브릭 클러스터에 쉽게 매핑하기 위한 것입니다.

*WebApplicationReplyUrl*은 Azure AD에서 로그인을 마친 후에 사용자에게 반환하는 기본 엔드포인트입니다. 이 엔드포인트를 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 엔드포인트로 설정합니다.

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 로그인한 후에는 스크립트가 Service Fabric 클러스터를 나타내는 웹 및 네이티브 애플리케이션을 만듭니다. [Azure Portal](https://portal.azure.com)에서 테넌트의 애플리케이션을 살펴보면 다음 두 개의 새 항목이 표시되어 있습니다.

   * *ClusterName*\_클러스터
   * *ClusterName*\_클라이언트

이 스크립트는 클러스터를 만들 때 Azure Resource Manager 템플릿에 필요한 JSON을 출력하므로 PowerShell 창을 계속 열어 두는 것이 좋습니다.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD 구성을 추가하여 클라이언트 액세스를 위해 Azure AD 사용
[azuredeploy.json][template]의 **Microsoft.ServiceFabric/clusters** 섹션에서 Azure AD를 구성합니다.  테넌트 ID, 클러스터 애플리케이션 ID 및 클라이언트 애플리케이션 ID에 대한 매개 변수를 추가합니다.  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

매개 변수 값을 [azuredeploy.parameters.json][parameters] 매개 변수 파일에 추가합니다.  예: 

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>가상 네트워크 및 클러스터 배포

다음으로 네트워크 토폴로지를 설정하고 Service Fabric 클러스터를 배포합니다. [azuredeploy.json][template] Resource Manager 템플릿은 VNET(가상 네트워크), Service Fabric에 대한 서브넷 및 NSG(네트워크 보안 그룹)를 만듭니다. 템플릿은 활성화된 인증서 보안으로 클러스터도 배포합니다.  프로덕션 클러스터의 경우 클러스터 인증서로 CA(인증 기관)의 인증서를 사용합니다. 테스트 클러스터를 보호하는 데 자체 서명된 인증서를 사용할 수 있습니다.

이 문서의 템플릿은 인증서 지문을 사용하여 클러스터 인증서를 식별하는 클러스터를 배포합니다.  두 인증서에 동일한 지문을 사용할 수 없으므로 인증서 관리가 더 어려워집니다. 배포된 클러스터를 인증서 지문에서 인증서 일반 이름으로 전환하면 인증서 관리 방법이 훨씬 간단해집니다.  인증서 관리에 인증서 일반 이름을 사용하도록 클러스터를 업데이트하는 방법에 대해 알아보려면 [클러스터를 인증서 일반 이름 관리로 변경](service-fabric-cluster-change-cert-thumbprint-to-cn.md)을 참조하세요.

### <a name="create-a-cluster-using-an-existing-certificate"></a>기존 인증서를 사용하여 클러스터 만들기

다음 스크립트는 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet 및 템플릿을 사용하여 Azure에서 새 클러스터를 배포합니다. 또한 cmdlet은 Azure에서 새 키 자격 증명 모음을 만들고 인증서를 업로드합니다.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>자체 서명된 새로운 인증서를 사용하여 클러스터 만들기

다음 스크립트는 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet 및 템플릿을 사용하여 Azure에서 새 클러스터를 배포합니다. 또한 이 cmdlet은 Azure에 새로운 키 자격 증명 모음을 만들고, 키 자격 증명 모음에 자체 서명된 인증서를 추가하고, 인증서 파일을 로컬로 다운로드합니다.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결

Service Fabric SDK가 설치된 Service Fabric PowerShell 모듈을 사용하여 클러스터에 연결합니다.  먼저 사용자 컴퓨터에서 현재 사용자의 개인(내) 저장소에 인증서를 설치합니다.  다음 PowerShell 명령을 실행합니다.

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

이제 보안 클러스터에 연결할 준비가 되었습니다.

**Service Fabric** PowerShell 모듈은 Service Fabric 클러스터, 응용 프로그램 및 서비스를 관리하기 위한 많은 cmdlet을 제공합니다.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet을 사용하여 보안 클러스터에 연결합니다. 인증서 SHA1 지문 및 연결 엔드포인트 세부 정보는 이전 단계의 출력에 있습니다.

이전에 AAD 클라이언트 인증을 설정한 경우 다음을 실행합니다. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

AAD 클라이언트 인증을 설정하지 않은 경우 다음을 실행합니다.
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet을 사용하여 사용자가 연결되어 있고 클러스터가 정상 상태인지 확인합니다.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서 시리즈의 다른 문서에서는 방금 만든 클러스터를 사용합니다. 다음 문서로 바로 이동하지 않는 경우 요금이 발생하지 않도록 [클러스터를 삭제](service-fabric-cluster-delete.md)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * PowerShell을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * Azure Active Directory 인증 설정
> * PowerShell을 사용하여 Azure에서 안전한 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이제 다음 자습서로 넘어가서 클러스터 규모를 조정하는 방법을 알아보겠습니다.
> [!div class="nextstepaction"]
> [클러스터 규모 조정](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
