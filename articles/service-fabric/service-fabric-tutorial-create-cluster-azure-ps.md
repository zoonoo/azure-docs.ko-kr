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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 721d1bf5b03d667baa380f0b825f266d2326ecae
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>PowerShell을 사용하여 Azure에서 보안 클러스터 만들기
이 자습서에서는 Azure에서 실행되는 Service Fabric 클러스터(Windows 또는 Linux)를 만드는 방법을 보여 줍니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.
- [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치합니다. 

다음 절차에서는 키 자격 증명 모음에 있는 자체 서명된 인증서로 보호되는 단일 노드(단일 가상 컴퓨터) 미리 보기 Service Fabric 클러스터를 만듭니다. 단일 노드 클러스터는 여러 가상 컴퓨터로 확장할 수 없으며 미리 보기 클러스터는 새 버전으로 업그레이드할 수 없습니다. 

Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.
Service Fabric 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.

## <a name="create-the-cluster-using-azure-powershell"></a>Azure PowerShell을 사용하여 클러스터 만들기
1. [Service Fabric용 Azure Resource Manager 템플릿](https://aka.ms/securepreviewonelineclustertemplate) GitHub 리포지토리에서 Azure Resource Manager 템플릿 및 매개 변수 파일의 로컬 복사본을 다운로드합니다.  *azuredeploy.json*은 Service Fabric 클러스터를 정의하는 Azure Resource Manager 템플릿입니다. *azuredeploy.parameters.json*은 클러스터 배포를 사용자 지정하는 데 사용할 매개 변수 파일입니다.

2. *azuredeploy.parameters.json* 매개 변수 파일에서 다음 매개 변수를 사용자 지정합니다.
  
   | 매개 변수       | 설명 | 제안 값 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | 클러스터를 배포할 Azure 지역입니다. | *예: westeurope, eastasia, eastus* |
   | clusterName     | 클러스터의 이름입니다. | *예: bobs-sfpreviewcluster* |
   | adminUserName   | 클러스터 가상 컴퓨터의 로컬 관리자 계정입니다. | *모든 유효한 Windows Server 사용자 이름* |
   | adminPassword   | 클러스터 가상 컴퓨터의 로컬 관리자 계정 암호입니다. | *모든 유효한 Windows Server 암호* |
   | clusterCodeVersion | 실행할 Service Fabric 버전입니다. (미리 보기 버전은 255.255.X.255) | **255.255.5718.255** |
   | vmInstanceCount | 클러스터의 가상 컴퓨터 수(1 또는 3-99일 수 있음)입니다. | **1** |

3. PowerShell 콘솔을 열고 Azure에 로그인한 다음 클러스터를 배포할 구독을 선택합니다.

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Service Fabric에서 사용되는 인증서의 암호를 만들고 암호화합니다.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. 다음 명령을 실행하여 클러스터를 만듭니다.

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 매개 변수는 매개 변수 파일에 지정된 clusterName 매개 변수와 일치해야 하며, 도메인은 선택한 Azure 지역(예: `clustername.eastus.cloudapp.azure.com`)에 연결되어야 합니다.
   
    구성이 완료되면 Azure에서 생성된 클러스터에 대한 정보가 출력되고 인증서가 -CertificateOutputFolder 디렉터리에 복사됩니다.

6. 인증서를 두 번 클릭하여 인증서 가져오기 마법사를 엽니다.

7. 기본 설정을 사용하지만 **이 키를 내보낼 수 있도록 표시** 확인란을 **개인 키 보호** 단계에서 선택해야 합니다. 이 자습서의 뒷부분에 나오는 Azure Container Registry를 Service Fabric 클러스터 인증으로 구성할 때 Visual Studio에서 인증서를 내보내야 합니다.

8. 이제 브라우저에서 Service Fabric Explorer를 열 수 있습니다. URL은 PowerShell CmdLet의 출력에 있는 **ManagementEndpoint**(예: *https://mycluster.westeurope.cloudapp.azure.com:19080*)입니다. 

>[!NOTE]
>Service Fabric Explorer를 열면 인증서 오류가 표시되는데 이는 자체 서명된 인증서를 사용하고 있기 때문입니다. Edge에서는 *세부 정보*를 클릭한 다음 *웹 페이지로 이동* 링크를 클릭합니다. Chrome에서는 *고급* 및 *진행* 링크를 클릭합니다.

>[!NOTE]
>클러스터 작성이 실패한 경우 이미 배포된 리소스를 업데이트하는 명령을 언제든지 다시 실행할 수 있습니다. 실패한 배포의 일부로 인증서가 만들어진 경우 새 인증서가 생성됩니다. 클러스터 만들기 문제를 해결하려면 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결 
Service Fabric SDK가 설치된 Service Fabric PowerShell 모듈을 사용하여 클러스터에 연결합니다.  먼저 사용자 컴퓨터에서 현재 사용자의 개인(내) 저장소에 인증서를 설치합니다.  다음 PowerShell 명령을 실행합니다.

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

이제 보안 클러스터에 연결할 준비가 되었습니다.

**Service Fabric** PowerShell 모듈은 Service Fabric 클러스터, 응용 프로그램 및 서비스를 관리하기 위한 많은 cmdlet을 제공합니다.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet을 사용하여 보안 클러스터에 연결합니다. 인증서 지문 및 연결 끝점 세부 정보는 이전 단계의 출력에 있습니다. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
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

클러스터는 클러스터 리소스 외에도 다른 Azure 리소스로 이루어져 있습니다. 클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다. 

Azure에 로그인하고 클러스터를 제거할 구독 ID를 선택합니다.  [Azure Portal](http://portal.azure.com)에 로그인하여 구독 ID를 찾을 수 있습니다. [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이제 다음 자습서로 이동하여 기존 응용 프로그램을 배포하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Docker Compose를 사용하여 기존 .NET 응용 프로그램 배포](service-fabric-host-app-in-a-container.md)

