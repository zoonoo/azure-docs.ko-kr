<properties
   pageTitle="Azure에서 서비스 패브릭 클러스터에 사용되는 인증서 추가, 롤오버 및 제거 | Microsoft Azure"
   description="보조 클러스터 인증서를 업로드한 다음 이전 기본 인증서를 업로드하는 방법에 대해 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# Azure에서 서비스 패브릭 클러스터에 대한 인증서 추가 또는 제거

서비스 패브릭이 X.509 인증서를 사용하는 방법에 익숙해지고 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 읽어보는 것이 좋습니다. 다음 과정으로 진행하기 전에 클러스터 인증서가 무엇이며 어떤 용도로 사용되는지를 이해해야 합니다.

클러스터를 만드는 동안 인증서 보안을 구성할 때 서비스 패브릭을 사용하여 기본 인증서와 보조 인증서의 두 클러스터 인증서를 지정할 수 있습니다. 자세한 내용은 [포털을 통해 Azure 클러스터 만들기](service-fabric-cluster-creation-via-portal.md) 또는 [Azure Resource Manager를 통해 Azure 클러스터 만들기](service-fabric-cluster-creation-via-Resource Manager.md)를 참조하세요. Resource Manager를 통해 배포하며 클러스터 인증서를 하나만 지정하는 경우 해당 인증서가 기본 인증서로 사용됩니다. 클러스터를 만든 후 새 인증서를 보조 인증서로 추가할 수 있습니다.

>[AZURE.NOTE] 보안 클러스터의 경우 항상 적어도 하나의 유효한(취소되지 않거나 만료되지 않은) 기본 또는 보조 인증서를 배포해야 하며 그러지 않으면 클러스터가 작동을 중지합니다. 모든 유효한 인증서가 만료되기 90일 전에 시스템은 해당 노드에 대해 경고 추적 및 경고 상태 이벤트를 생성합니다. 현재, 서비스 패브릭이 이 항목에 대해 전송하는 전자 메일 또는 기타 알림은 없습니다.


## 포털을 사용하여 보조 인증서 추가
다른 인증서를 보조 인증서로 추가하려면 해당 인증서를 Azure 주요 자격 증명 모음에 업로드하고 클러스터의 VM에 배포해야 합니다. 자세한 내용은 [고객 관리 주요 자격 증명 모음에서 VM에 인증서 배포](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)를 참조하세요.

1. 방법에 대해서는 [주요 자격 증명 모음에 X.509 인증서 업로드](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)를 참조하세요.

2. [Azure 포털](https://portal.azure.com/)에 로그인하고 이 인증서를 추가하려는 클러스터 리소스로 이동합니다.
3. **설정** 아래에서 **보안**을 클릭하여 클러스터 보안 블레이드를 표시합니다.
4. 블레이드 위쪽의 **"+인증서"** 단추를 클릭하여 **"인증서 추가"** 블레이드로 이동합니다.
5. 드롭다운 목록에서 "보조 인증서 지문"을 선택하고 보조 주요 자격 증명 모음에 업로드한 보조 인증서의 인증서 지문을 입력합니다.

>[AZURE.NOTE]
이 블레이드로 이동할 때쯤에는 VM에 인증서를 이미 배포했고 VMSS 인스턴스의 로컬 인증서 저장소에서 이미 해당 인증서를 사용할 수 있으므로 클러스터 만들기 워크플로와는 달리 여기서는 주요 자격 증명 모음 정보를 자세히 제공하지 않습니다.

**인증서**를 클릭합니다. 배포가 시작되고, 클러스터 보안 블레이드에 파란색 상태 표시줄이 표시됩니다.

![포털의 인증서 지문 스크린 샷][SecurityConfigurations\_02]

해당 배포가 완료되면 기본 또는 보조 인증서를 사용하여 클러스터에서 관리 작업을 수행할 수 있습니다.

![진행 중인 인증서 배포 스크린샷][SecurityConfigurations\_03]

배포가 완료될 때의 보안 블레이드 모습에 대한 스크린샷은 다음과 같습니다.

![배포 후의 인증서 지문 스크린샷][SecurityConfigurations\_08]


이제 방금 추가한 새 인증서를 사용하여 연결하고 클러스터에서 작업을 수행할 수 있습니다.

>[AZURE.NOTE]
현재, 포털에서 기본 및 보조 인증서를 교체할 방법은 없으며 해당 기능은 준비 중입니다. 유효한 클러스터 인증서가 있으면 클러스터는 문제없이 잘 작동합니다.

## 보조 인증서를 추가한 후 Resource Manager Powershell을 사용하여 기본 인증서로 교체

이러한 단계에서는 Resource Manager의 작동 원리에 익숙하며, Resource Manager 템플릿을 사용하여 하나 이상의 서비스 패브릭 클러스터를 배포했고, 클러스터를 설정하는 데 사용한 템플릿이 있다고 가정합니다. 또한 JSON을 사용하여 편리하게 작업할 수 있다고 간주합니다.

>[AZURE.NOTE]
시작 지점으로 사용하거나 필요할 때 사용할 수 있는 샘플 템플릿 및 매개 변수를 찾으려는 경우 이 [git repo]에서 다운로드합니다. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

#### Resource Manager 템플릿 편집 

[git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample)의 샘플을 사용하는 경우 샘플 5-VM-1-NodeTypes-Secure\_Step2.JSON에서 다음과 같은 변경 내용을 확인할 수 있습니다. 5-VM-1-NodeTypes-Secure\_Step1.JSON을 사용하여 보안 클러스터 배포

1. 클러스터를 배포하는 데 사용한 Resource Manager 템플릿을 엽니다.
2. "string" 형식의 새 매개 변수 "secCertificateThumbprint"를 추가합니다. 생성 시에 포털에서 또는 빠른 시작 템플릿에서 다운로드한 Resource Manager 템플릿을 사용하고 해당 매개 변수를 검색한 경우 이미 이러한 매개 변수가 정의되어 있을 수 있습니다.
3. "Microsoft.ServiceFabric/clusters" 리소스 정의를 찾습니다. 속성에서 "Certificate" JSON 태그를 찾을 수 있습니다. 이 태그는 다음 JSON 조각과 비슷합니다.
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
``` 

4. 새 태그 "ThumbprintSecondary"를 추가하고 "[parameters('secCertificateThumbprint')]" 값을 지정합니다.

이제 리소스 정의는 다음과 같습니다(템플릿 원본에 따라, 아래 조각과 정확히 같지는 않을 수 있음). 아래에서 볼 수 있듯이 여기에서 수행하는 작업은 새 인증서를 기본 인증서로 지정한 후 현재 기본 인증서를 보조 인증서로 전환하는 것입니다. 이를 통해 하나의 배포 단계에서 현재 인증서가 새 인증서로 롤오버됩니다.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### 위에서 추가한 새 매개 변수를 반영하도록 템플릿 파일 편집

[git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample)의 샘플을 사용하는 경우 샘플 5-VM-1-NodeTypes-Secure.paramters\_Step2.JSON에서 변경을 시작할 수 있습니다.


Resource Manager 템플릿 매개 변수 파일을 편집하고, secCertificate에 대한 새 매개 변수를 추가하고, 기존의 기본 인증서 세부 정보를 보조 인증서로 교체하고, 기본 인증서 세부 정보를 새 인증서 세부 정보로 바꿉니다.

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### Azure에 템플릿 배포

1. 이제 Azure에 템플릿을 배포할 준비가 되었습니다. Azure PS 버전 1+ 명령 프롬프트를 엽니다.
2. Azure 계정에 로그인하고 특정 Azure 구독을 선택합니다. 이 단게는 둘 이상의 Azure 구독에 대해 액세스 권한이 있는 사용자들을 위한 중요한 단계입니다.


```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

템플릿을 배포하기 전에 테스트합니다. 현재 클러스터가 배포된 동일한 리소스 그룹을 사용합니다.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

리소스 그룹에 템플릿을 배포합니다. 현재 클러스터가 배포된 동일한 리소스 그룹을 사용합니다. New-AzureRmResourceGroupDeployment 명령을 실행합니다. 기본값은 **incremental**이므로 모드는 지정할 필요가 없습니다.

>[AZURE.NOTE]
Mode를 Complete로 설정하면 템플릿에 없는 리소스를 실수로 삭제할 수 있습니다. 따라서 이러한 경우에는 사용하지 않도록 합니다.
   

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

다음은 동일한 powershell의 작성된 예제입니다.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

배포가 완료되면 새 인증서를 사용하여 클러스터에 연결하고 일부 쿼리를 수행합니다. 가능한 경우 이전 기본 인증서를 삭제할 수 있습니다.

자체 서명된 인증서를 사용하는 경우 로컬 TrustedPeople 인증서 저장소로 인증서를 가져와야 하는 것을 잊지 마세요.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
다음은 빠른 참조를 위해 제공되는 보안 클러스터 연결 명령입니다.
```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
다음은 빠른 참조를 위해 제공되는 클러스터 상태 확인 명령입니다.
```powershell
Get-ServiceFabricClusterHealth 
```
 
## 포털을 사용하여 이전 인증서 제거
다음은 클러스터에서 사용하지 않도록 오래된 인증서를 제거하는 프로세스입니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인하고 클러스터의 보안 설정으로 이동합니다.
2. 제거하려는 인증서를 마우스 오른쪽 단추로 클릭합니다.
3. 삭제를 선택하고 지시를 따릅니다.

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## 다음 단계
클러스터 관리에 대한 자세한 내용은 다음 문서를 읽어보세요.

- [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
- [클라이언트에 대한 역할 기반 액세스 설정](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png [SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png [SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png [SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!---HONumber=AcomDC_0817_2016-->