---
title: Azure에서 Windows를 실행하는 Service Fabric 클러스터 만들기 | Microsoft Docs
description: 이 자습서에서는 PowerShell을 사용하여 Windows Service Fabric 클러스터를 Azure 가상 네트워크 및 네트워크 보안 그룹에 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: dabbefa8ca2073e30948f1c70782f730bceae030
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158104"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>자습서: Azure 가상 네트워크에 Windows를 실행하는 Service Fabric 클러스터 배포

이 자습서는 시리즈의 1부입니다. PowerShell 및 템플릿을 사용하여 Windows를 실행하는 Azure Service Fabric 클러스터를 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md) 및 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)에 배포하는 방법에 대해 알아봅니다. 작업이 완료되면 애플리케이션을 배포할 수 있는 클라우드에서 클러스터가 실행됩니다. Azure CLI를 사용하는 Linux 클러스터를 만들려면 [Azure에서 보안 Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

이 자습서는 프로덕션 시나리오를 설명합니다. 테스트를 위해 더 작은 클러스터를 만들려면 [테스트 클러스터 만들기](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * PowerShell을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * Azure Active Directory 인증 설정
> * 진단 컬렉션 구성
> * EventStore 서비스 설정
> * Azure Monitor 로그 설정
> * Azure PowerShell에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 보안 클러스터 만들기
> * [클러스터 모니터링](service-fabric-tutorial-monitor-cluster.md)
> * [클러스터 규모 확장 또는 규모 감축](service-fabric-tutorial-scale-cluster.md)
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * [클러스터 삭제](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.
* [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps)을 설치합니다.
* [Azure 클러스터](service-fabric-azure-clusters-overview.md)에 대한 주요 개념을 검토합니다.
* 프로덕션 클러스터 배포를 [계획 및 준비](service-fabric-cluster-azure-deployment-preparation.md)합니다.

다음 절차에서는 7개 노드 Service Fabric 클러스터를 만듭니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 Azure에서 Service Fabric 클러스터를 실행함으로써 발생하는 비용을 계산합니다.

## <a name="download-and-explore-the-template"></a>템플릿 다운로드 및 탐색

다음 Azure Resource Manager 템플릿 파일을 다운로드합니다.

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

이 템플릿은 7개 가상 머신 및 3개 노드 유형의 보안 클러스터를 가상 네트워크 및 네트워크 보안 그룹에 배포합니다.  다른 예제 템플릿은 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates)에 있을 수 있습니다. [azuredeploy.json][template]은 다음을 포함하여 여러 리소스를 배포합니다.

### <a name="service-fabric-cluster"></a>Service Fabric 클러스터

**Microsoft.ServiceFabric/clusters** 리소스에서 Windows 클러스터가 다음과 같은 특성으로 구성됩니다.

* 3개 노드 유형
* 주 노드 유형의 5개 노드(템플릿 매개 변수에서 구성 가능) 및 다른 두 노드 유형 각각의 1개 노드
* OS: 컨테이너가 있는 Windows Server 2016 Datacenter(템플릿 매개 변수에서 구성 가능)
* 인증서 보안(템플릿 매개 변수에서 구성 가능)
* [역방향 프록시](service-fabric-reverseproxy.md) 사용
* [DNS 서비스](service-fabric-dnsservice.md) 사용
* Bronze의 [내구성 수준](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
* Silver의 [안정성 수준](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
* 클라이언트 연결 엔드포인트: 19000(템플릿 매개 변수에서 구성 가능)
* HTTP 게이트웨이 엔드포인트: 19080(템플릿 매개 변수에서 구성 가능)

### <a name="azure-load-balancer"></a>Azure Load Balancer

부하 분산 장치는 **Microsoft.Network/loadBalancers** 리소스에서 구성됩니다. 프로브 및 규칙은 다음 포트에 대해 설정됩니다.

* 클라이언트 연결 엔드포인트: 19000
* HTTP 게이트웨이 엔드포인트: 19080
* 애플리케이션 포트: 80
* 애플리케이션 포트: 443
* Service Fabric 역방향 프록시: 19081

다른 애플리케이션 포트가 필요한 경우 트래픽을 허용하도록 **Microsoft.Network/loadBalancers** 리소스 및 **Microsoft.Network/networkSecurityGroups** 리소스를 조정해야 합니다.

### <a name="virtual-network-subnet-and-network-security-group"></a>가상 네트워크, 서브넷 및 네트워크 보안 그룹

가상 네트워크, 서브넷 및 네트워크 보안 그룹의 이름은 템플릿 매개 변수에서 선언됩니다. 가상 네트워크 및 서브넷의 주소 공간도 템플릿 매개 변수로 선언되고 **Microsoft.Network/virtualNetworks** 리소스에 구성됩니다.

* 가상 네트워크 주소 공간: 172.16.0.0/20
* Service Fabric 서브넷 주소 공간: 172.16.2.0/23

**Microsoft.Network/networkSecurityGroups** 리소스에서 다음 인바운드 트래픽 규칙을 사용하도록 설정할 수 있습니다. 템플릿 변수를 변경하여 포트 값을 변경할 수 있습니다.

* ClientConnectionEndpoint(TCP): 19000
* HttpGatewayEndpoint(HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* 사용 후 삭제 포트 범위: 49152 ~ 65534(최소 256개 포트 필요)
* 애플리케이션 사용에 대한 포트: 80 및 443
* 애플리케이션 포트 범위: 49152 ~ 65534(서비스 간 통신에 사용됨, 다른 포트는 부하 분산 장치에서 열리지 않음)
* 다른 모든 포트 차단

다른 애플리케이션 포트가 필요한 경우 트래픽을 허용하도록 **Microsoft.Network/loadBalancers** 리소스 및 **Microsoft.Network/networkSecurityGroups** 리소스를 조정해야 합니다.

### <a name="windows-defender"></a>Windows Defender
[Windows Defender 바이러스 백신 프로그램](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)은 기본적으로 Windows Server 2016에 설치되어 작동합니다. 사용자 인터페이스는 기본적으로 일부 SKU에 설치되지만 필요하지는 않습니다. 템플릿에 선언된 각 노드 유형/VM 확장 집합에 대해 [Azure VM 맬웨어 방지 확장](/azure/virtual-machines/extensions/iaas-antimalware-windows)을 사용하여 Service Fabric 디렉터리 및 프로세스를 제외합니다.

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

[azuredeploy.parameters.json][parameters] 매개 변수 파일은 클러스터 및 연결된 리소스를 배포하는 데 사용되는 많은 값을 선언합니다. 배포에 맞게 수정할 매개 변수는 다음과 같습니다.

**매개 변수** | **예제 값** | **참고 사항** 
|---|---|---|
|adminUserName|vmadmin| 클러스터 VM에 대한 관리자 사용자 이름입니다. [VM에 대한 사용자 이름 요구 사항](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| 클러스터 VM에 대한 관리자 암호입니다. [VM에 대한 암호 요구 사항](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| 클러스터의 이름입니다. 문자와 숫자만 포함할 수 있습니다. 길이는 3자에서 23자 사이일 수 있습니다.|
|location|southcentralus| 클러스터의 위치입니다. |
|certificateThumbprint|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 SHA1 지문 값을 입력합니다. 예를 들면 "6190390162C988701DB5676EB81083EA608DCCF3"과 같습니다.</p> |
|certificateUrlValue|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다. </p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 URL을 입력합니다. 예: "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 원본 자격 증명 모음 값을 입력합니다. 예를 들면 “/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”와 같습니다.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory 클라이언트 인증 설정
Azure에 호스트된 공용 네트워크에 배포된 Service Fabric 클러스터의 경우 클라이언트-노드 상호 인증에 추천되는 사항은 다음과 같습니다.
* 클라이언트 ID에 Azure Active Directory를 사용합니다.
* HTTP 통신의 서버 ID 및 SSL 암호화에 인증서를 사용합니다.

[클러스터를 만들기](#createvaultandcert) 전에 Service Fabric 클러스터에 대한 클라이언트를 인증하도록 Azure AD(Azure Active Directory)를 설정해야 합니다. 조직(테넌트)에서는 Azure AD를 사용하여 애플리케이션에 대한 사용자 액세스를 관리할 수 있습니다. 

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 및 [Visual Studio](service-fabric-manage-application-in-visual-studio.md)를 포함하여 관리 기능에 대한 몇 가지 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 애플리케이션(웹 애플리케이션과 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다.  애플리케이션을 만든 후 읽기 전용 및 관리자 역할에 사용자를 할당합니다.

> [!NOTE]
> 클러스터를 만들기 전에 다음 단계를 완료해야 합니다. 스크립트는 클러스터 이름과 끈점을 예상하므로 이 값을 계획해야 하며, 이 값은 이미 만든 값이 아닙니다.

이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그렇지 않은 경우 [Azure Active Directory 테넌트를 가져오는 방법](../active-directory/develop/quickstart-create-new-tenant.md)을 참조하세요.

Azure AD를 Service Fabric 클러스터로 구성하는 데 관련된 단계를 간소화하기 위해 Windows PowerShell 스크립트 세트를 만들었습니다. 컴퓨터에 [스크립트를 다운로드](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)합니다.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD 애플리케이션 만들기 및 역할에 사용자 할당
두 개의 Azure AD 애플리케이션(웹 애플리케이션 및 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다. 클러스터를 나타내는 애플리케이션이 만들어지면 사용자를 [Service Fabric에서 지원하는 역할](service-fabric-cluster-security-roles.md)(읽기 전용 및 관리자)에 할당합니다.

`SetupApplications.ps1`을 실행하고 테넌트 ID, 클러스터 이름 및 웹 애플리케이션 회신 URL을 매개 변수로 제공합니다. 사용자에 대한 사용자 이름과 암호를 지정합니다. 예:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 국가별 클라우드(예: Azure Government, Azure 중국, Azure 독일)의 경우 `-Location` 매개 변수를 지정합니다.

*TenantId* 또는 디렉터리 ID는 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다. **Azure Active Directory** > **속성**을 차례로 선택하고, **디렉터리 ID** 값을 복사합니다.

*ClusterName*은 스크립트로 만든 Azure AD 애플리케이션의 접두사로 사용됩니다. 실제 클러스터 이름과 정확히 일치할 필요는 없습니다. Azure AD 아티팩트를 사용 중인 Service Fabric 클러스터에 쉽게 매핑할 수 있습니다.

*WebApplicationReplyUrl*은 Azure AD에서 로그인을 마친 후에 사용자에게 반환하는 기본 엔드포인트입니다. 이 엔드포인트를 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 엔드포인트로 설정합니다.

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD 테넌트에 대한 관리자 권한이 있는 계정에 로그인하라는 메시지가 표시됩니다. 로그인한 후에는 스크립트가 Service Fabric 클러스터를 나타내는 웹 및 네이티브 애플리케이션을 만듭니다. [Azure Portal](https://portal.azure.com)에 있는 테넌트의 애플리케이션에는 다음 두 가지의 새 항목이 표시됩니다.

   * *ClusterName*\_클러스터
   * *ClusterName*\_클라이언트

이 스크립트는 클러스터를 만들 때 Resource Manager 템플릿에 필요한 JSON을 출력하므로 PowerShell 창이 열린 상태로 유지하는 것이 좋습니다.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD 구성을 추가하여 클라이언트 액세스를 위해 Azure AD 사용
[azuredeploy.json][template]의 **Microsoft.ServiceFabric/clusters** 섹션에서 Azure AD를 구성합니다. 테넌트 ID, 클러스터 애플리케이션 ID 및 클라이언트 애플리케이션 ID에 대한 매개 변수를 추가합니다.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
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

매개 변수 값을 [azuredeploy.parameters.json][parameters] 매개 변수 파일에 추가합니다. 예:

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
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>클러스터에서 진단 컬렉션 구성
Service Fabric 클러스터를 실행하는 경우 중앙 위치에서 모든 노드의 로그를 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 클러스터나 해당 클러스터에서 실행 중인 애플리케이션 및 서비스의 문제를 분석하고 해결하는 데 도움이 됩니다.

로그를 업로드하고 수집하는 한 가지 방법은 로그를 Azure Storage에 업로드하고 이를 Azure Application Insights 또는 Event Hubs에 보낼 수 있는 Azure Diagnostics(WAD) 확장을 사용하는 것입니다. 또한 외부 프로세스를 사용하여 스토리지에서 이벤트를 읽고 분석 플랫폼 제품(예: Azure Monitor 로그 또는 기타 로그 구문 분석 솔루션)에 배치할 수 있습니다.

이 자습서를 수행하는 경우 진단 수집이 [템플릿][template]에 이미 구성되어 있습니다.

진단이 배포되지 않은 기존 클러스터가 있는 경우 클러스터 템플릿을 통해 기존 클러스터를 추가하거나 업데이트할 수 있습니다. 기존 클러스터를 만들거나 포털에서 템플릿을 다운로드하는 데 사용되는 Resource Manager 템플릿을 수정합니다. 다음 작업을 수행하여 template.json 파일을 수정합니다.

새 스토리지 리소스를 템플릿의 리소스 섹션에 추가합니다.
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

다음으로, 스토리지 계정 이름 및 형식에 대한 매개 변수를 템플릿의 매개 변수 섹션에 추가합니다. storage account name goes here(여기에 스토리지 계정 이름을 입력합니다) 자리 표시자 텍스트를 원하는 스토리지 계정의 이름으로 바꿉니다.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

다음으로, 클러스터에 있는 각 **Microsoft.Compute/virtualMachineScaleSets** 리소스의 **VirtualMachineProfile** 속성 확장 배열에 **IaaSDiagnostics** 확장을 추가합니다.  [샘플 템플릿][template]을 사용하는 경우 세 개의 가상 머신 확장 집합(클러스터의 노드 유형별로 하나씩)이 있습니다.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>EventStore 서비스 구성
EventStore 서비스는 Service Fabric의 모니터링 옵션입니다. EventStore는 일정한 시기에 클러스터 또는 워크로드의 상태를 파악할 수 있는 방법을 제공합니다. EventStore는 클러스터에서 이벤트를 유지 관리하는 상태 저장 Service Fabric 서비스입니다. 이벤트는 Service Fabric Explorer, REST 및 API를 통해 노출됩니다. EventStore는 클러스터를 직접 쿼리하여 클러스터의 모든 엔터티에 대한 진단 데이터를 획득하고 다음 용도로 사용할 수 있습니다.

* 개발 또는 테스트의 문제 또는 모니터링 파이프라인을 사용할 위치의 문제 진단
* 클러스터에 대해 수행하는 관리 작업이 제대로 처리되고 있는지 확인
* Service Fabric이 특정 엔터티와 상호 작용하는 방법의 "스냅샷" 가져오기



클러스터에서 EventStore 서비스를 사용하도록 설정하려면 **Microsoft.ServiceFabric/clusters** 리소스의 **fabricSettings** 속성에 다음을 추가합니다.

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>클러스터에 대한 Azure Monitor 로그 설정

Azure Monitor 로그는 클러스터 수준 이벤트를 모니터링하는 데 추천됩니다. 클러스터를 모니터링하도록 Azure Monitor 로그를 설정하려면 [클러스터 수준 이벤트를 볼 수 있도록 설정된 진단](#configure-diagnostics-collection-on-the-cluster)을 사용해야 합니다.  

클러스터에서 가져오는 진단 데이터에 작업 영역을 연결해야 합니다.  이 로그 데이터는 *applicationDiagnosticsStorageAccountName* 스토리지 계정의 WADServiceFabric*EventTable, WADWindowsEventLogsTable 및 WADETWEventTable 테이블에 저장됩니다.

Azure Log Analytics 작업 영역을 추가하고, 다음 작업 영역에 솔루션을 추가합니다.

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

다음으로, 매개 변수를 추가합니다.
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

다음으로, 변수를 추가합니다.
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Log Analytics 에이전트 확장을 클러스터에 설정된 각 가상 머신 확장 집합에 추가하고, 에이전트를 Log Analytics 작업 영역에 연결합니다. 이를 통해 컨테이너, 애플리케이션 및 성능 모니터링에 대한 진단 데이터를 수집할 수 있습니다. 이것을 가상 머신 확장 집합 리소스에 확장으로 추가하면 Azure Resource Manager는 클러스터 크기 조정 시에도 해당 항목이 모든 노드에 설치되도록 보장합니다.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>가상 네트워크 및 클러스터 배포

다음으로 네트워크 토폴로지를 설정하고 Service Fabric 클러스터를 배포합니다. [azuredeploy.json][template] Resource Manager 템플릿은 Service Fabric에 대한 가상 네트워크, 서브넷 및 네트워크 보안 그룹을 만듭니다. 템플릿은 활성화된 인증서 보안으로 클러스터도 배포합니다. 프로덕션 클러스터의 경우 인증 기관의 인증서를 클러스터 인증서로 사용합니다. 테스트 클러스터를 보호하는 데 자체 서명된 인증서를 사용할 수 있습니다.

이 문서의 템플릿은 인증서 지문을 사용하여 클러스터 인증서를 식별하는 클러스터를 배포합니다. 두 인증서에 동일한 지문을 사용할 수 없으므로 인증서 관리가 더 어려워집니다. 배포된 클러스터를 인증서 지문에서 인증서 일반 이름으로 전환하면 인증서 관리가 간소화됩니다. 인증서 관리에 인증서 일반 이름을 사용하도록 클러스터를 업데이트하는 방법에 대한 자세한 내용은 [클러스터를 인증서 일반 이름 관리로 변경](service-fabric-cluster-change-cert-thumbprint-to-cn.md)을 참조하세요.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>기존 인증서를 사용하여 클러스터 만들기

다음 스크립트는 [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) cmdlet 및 템플릿을 사용하여 Azure에서 새 클러스터를 배포합니다. 이 cmdlet은 Azure에서 새 키 자격 증명 모음을 만들고 인증서를 업로드합니다.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>자체 서명된 새 인증서를 사용하여 클러스터 만들기

다음 스크립트는 [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) cmdlet 및 템플릿을 사용하여 Azure에서 새 클러스터를 배포합니다. 이 cmdlet은 Azure에서 새 키 자격 증명 모음을 만들고, 자체 서명된 인증서를 키 자격 증명 모음에 추가하고, 인증서 파일을 로컬로 다운로드합니다.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결

Service Fabric SDK와 함께 설치된 Service Fabric PowerShell 모듈을 사용하여 클러스터에 연결합니다.  먼저 사용자 컴퓨터에서 현재 사용자의 개인(내) 저장소에 인증서를 설치합니다. 다음 PowerShell 명령을 실행합니다.

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

이제 보안 클러스터에 연결할 준비가 되었습니다.

**Service Fabric** PowerShell 모듈은 Service Fabric 클러스터, 응용 프로그램 및 서비스를 관리하기 위한 많은 cmdlet을 제공합니다. [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet을 사용하여 보안 클러스터에 연결합니다. 인증서 SHA1 지문 및 연결 엔드포인트 세부 정보는 이전 단계의 출력에 있습니다.

이전에 Azure AD 클라이언트 인증을 설정한 경우 다음 명령을 실행합니다. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Azure AD 클라이언트 인증을 설정하지 않은 경우 다음 명령을 실행합니다.
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

방금 만든 클러스터는 이 자습서 시리즈의 다른 문서에서 사용합니다. 다음 문서로 바로 이동하지 않는 경우 요금이 발생하지 않도록 [클러스터를 삭제](service-fabric-cluster-delete.md)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

클러스터 크기를 조정하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="checklist"]
> * PowerShell을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * Azure Active Directory 인증 설정
> * 진단 컬렉션 구성
> * EventStore 서비스 설정
> * Azure Monitor 로그 설정
> * Azure PowerShell에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

다음으로, 클러스터를 모니터링하는 방법을 알아보려면 다음 자습서로 진행하세요.
> [!div class="nextstepaction"]
> [클러스터 모니터링](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
