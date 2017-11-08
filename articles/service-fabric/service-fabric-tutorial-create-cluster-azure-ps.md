---
title: "Azure에서 Service Fabric 클러스터 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure에서 Windows 또는 Linux Service Fabric 클러스터를 만드는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 8b744f6b33a04a707c56e6fc8a4638118170b668
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-secure-cluster-in-azure-by-using-powershell"></a>PowerShell을 사용하여 Azure에서 보안 클러스터 만들기
이 문서는 Azure Service Fabric 클러스터 및 컨테이너를 사용하여 .NET 응용 프로그램을 클라우드로 이동하는 방법을 보여주는 자습서 시리즈의 첫 번째 단계입니다. 다음 단계에서는 Azure에서 실행되는 Service Fabric 클러스터(Windows 또는 Linux)를 만드는 방법을 알아봅니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 보안 클러스터가 생깁니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Service Fabric SDK](service-fabric-get-started.md)를 설치합니다.
- [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치합니다. (필요한 경우 [Azure PowerShell을 설치](/powershell/azure/overview)하거나 [최신 버전으로 업데이트](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps)합니다.)


## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기

이 스크립트는 단일 노드 미리 보기 Service Fabric 클러스터를 만듭니다. 자체 서명된 인증서는 클러스터를 보호합니다. 스크립트는 클러스터와 함께 인증서를 만들고 키 자격 증명 모음에 인증서를 배치합니다. 단일 노드 클러스터를 하나의 가상 컴퓨터보다 확장할 수 없으며 미리 보기 클러스터를 새 버전으로 업그레이드할 수 없습니다.

Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.
Service Fabric 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인
PowerShell 콘솔을 열고 Azure에 로그인한 다음 클러스터를 배포할 구독을 선택합니다.

   ```PowerShell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>클러스터 매개 변수

   이 스크립트는 다음 매개 변수 및 개념을 사용합니다. 매개 변수를 요구 사항에 맞게 사용자 지정합니다.

   | 매개 변수       | 설명 | 제안 값 |
   | --------------- | ----------- | --------------- |
   | 위치 | 클러스터를 배포한 Azure 지역입니다. | 예: *westeurope*, *eastasia* 또는 *eastus* |
   | 이름     | 만들려는 클러스터의 이름입니다. 이름은 4~23자여야 하고 소문자, 숫자 및 하이픈만 포함할 수 있습니다. | 예: *bobs-sfpreviewcluster* |
   | ResourceGroupName   | 클러스터를 만들 리소스 그룹의 이름입니다. | 예: *myresourcegroup* |
   | VmSku  | 노드에 사용할 가상 컴퓨터 SKU입니다. | 유효한 가상 컴퓨터 SKU |
   | OS  | 노드에 사용할 가상 컴퓨터 OS입니다. | 유효한 가상 컴퓨터 OS |
   | KeyVaultName | 클러스터와 연결할 새 키 자격 증명 모음의 이름입니다. | 예: *mykeyvault* |
   | ClusterSize | 클러스터의 가상 컴퓨터 수(*1* 또는 *3~99*일 수 있음)입니다.| 미리 보기 클러스터에 하나의 가상 컴퓨터만 지정합니다. |
   | CertificateSubjectName | 만들 인증서의 고유 이름입니다. | *<name>*.*<location>*.cloudapp.azure.com 형식을 따릅니다. |

### <a name="default-parameter-values"></a>기본 매개 변수 값
**Virtual Machine**: 옵션 설정입니다. 지정하지 않으면 관리자 사용자 이름은 기본적으로 *vmadmin*이 되며 클러스터를 만들기 전에 PowerShell에 가상 컴퓨터 암호를 묻는 메시지가 표시됩니다.

**포트**: 기본값은 포트 80 및 8081입니다. [Service Fabric 클러스터에서 포트](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule)에 대한 지침에 따라 추가 포트를 지정할 수 있습니다.

**진단**: 기본적으로 사용하도록 설정됩니다.

**DNS 서비스**: 기본적으로 사용하도록 설정되어 있지 않습니다.

**역방향 프록시**: 기본적으로 사용하도록 설정되어 있지 않습니다.

## <a name="create-the-cluster-with-your-parameters"></a>매개 변수를 사용하여 클러스터 만들기

요구 사항에 맞는 매개 변수를 결정한 후에 다음 명령을 실행하여 보안 Service Fabric 클러스터 및 해당 인증서를 생성합니다.

추가 매개 변수를 포함하도록 이 스크립트를 수정할 수 있습니다. 클러스터 생성을 위한 매개 변수에 대한 자세한 내용은 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) cmdlet을 참조하세요.

>[!NOTE]
>이 명령을 실행하기 전에 클러스터 인증서를 저장할 폴더를 만들어야 합니다.

```PowerShell

# Set the certificate variables. This creates and encrypts a password that Service Fabric will use.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

# You must create the folder where you want to store the certificate on your machine before you start this step.
$certfolder="c:\mycertificates\"

# Set the variables for common values. Change the values to fit your needs.
$clusterloc="WestUS"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. The possible values are 1 and 3-99.
$clustersize=1

# Create the Service Fabric cluster and its self-signed certificate. The OS you specify here lets you use this cluster with any applications that are also using containers.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

이러한 생성 프로세스는 몇 분 정도 걸릴 수 있습니다. 구성이 완료된 후에 Azure에서 만든 클러스터에 대한 정보를 출력합니다. 또한 클러스터 인증서를 이 매개 변수에 지정한 경로의 CertificateOutputFolder 디렉터리에 복사합니다.

다음 URL과 같은 클러스터의 **ManagementEndpoint** URL을 기록해 둡니다. https://mycluster.westeurope.cloudapp.azure.com:19080

## <a name="import-the-certificate"></a>인증서 가져오기

클러스터가 만들어지면 다음 명령을 실행하여 자체 서명된 인증서를 사용할 수 있는지 확인합니다.

```PowerShell

# To connect to the cluster, install the certificate into the Personal (My) store of the current user on your computer.
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
-FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
-Password $certpwd
```

이 명령은 컴퓨터의 현재 사용자에 대해 인증서를 설치합니다. Service Fabric Explorer에 액세스하고 클러스터의 상태를 확인하려면 이 인증서가 필요합니다.


## <a name="view-your-cluster-optional"></a>클러스터 보기(선택 사항)

클러스터와 가져온 인증서가 있는 경우 클러스터에 연결하고 해당 상태를 볼 수 있습니다. Service Fabric Explorer 또는 PowerShell을 통해 다양한 방식으로 연결할 수 있습니다.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Service Fabric Explorer를 통해 클러스터의 상태를 볼 수 있습니다. 이렇게 하려면 클러스터에 대한 **ManagementEndpoint** URL로 이동하고 컴퓨터에 저장된 인증서를 선택합니다.

>[!NOTE]
>Service Fabric Explorer를 열면 인증서 오류가 표시되는데 이는 자체 서명된 인증서를 사용하고 있기 때문입니다. Edge에서는 **세부 정보**를 클릭한 다음 **웹 페이지로 이동** 링크를 클릭합니다. Chrome에서는 **고급**을 클릭하고 **진행** 링크를 클릭합니다.

### <a name="powershell"></a>PowerShell

Service Fabric PowerShell 모듈은 Service Fabric 클러스터, 응용 프로그램 및 서비스를 관리하기 위해 많은 cmdlet을 제공합니다. [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet을 사용하여 보안 클러스터에 연결합니다. 인증서 지문 및 연결 끝점 세부 정보는 이전 단계의 출력에 있을 수 있습니다.

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
-KeepAliveIntervalInSec 10 `
-X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet을 사용하여 사용자가 연결되어 있고 클러스터가 정상 상태인지 확인할 수도 있습니다.

```PowerShell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 PowerShell을 사용하여 Azure에서 안전한 Service Fabric 클러스터를 만드는 방법을 알아보았습니다.

이제 다음 자습서로 이동하여 기존 응용 프로그램을 배포하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Docker Compose를 사용하여 기존 .NET 응용 프로그램 배포](service-fabric-host-app-in-a-container.md)

 
