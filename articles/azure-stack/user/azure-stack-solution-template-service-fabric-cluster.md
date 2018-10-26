---
title: Azure Stack에서 보안된 Service Fabric 클러스터 배포 | Microsoft Docs
description: Azure Stack에서 보안된 Service Fabric 클러스터를 배포 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: shnatara
ms.openlocfilehash: abb77f59e49a98c1195dcd2e7b622b2b8e3fd7b8
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087287"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Azure Stack에서 Service Fabric 클러스터 배포

사용 된 **Service Fabric 클러스터** Azure Stack에서 보안된 Service Fabric 클러스터를 배포 하려면 Azure Marketplace에서 항목입니다. 

Service Fabric을 사용 하 여 작업에 대 한 자세한 내용은 참조 하세요. [Azure Service Fabric의 개요](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) 하 고 [Service Fabric 클러스터 보안 시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), Azure 설명서에서.

Azure Stack에서 Service Fabric 클러스터는 Microsoft.ServiceFabric 리소스 공급자를 사용 하지 않습니다. 대신, Azure Stack에서 Service Fabric 클러스터는 Desired State Configuration (DSC)를 사용 하 여 설정 하는 사전 설치 된 소프트웨어를 사용 하 여 설정 하는 가상 머신 확장 합니다.

## <a name="prerequisites"></a>필수 조건

다음은 Service Fabric 클러스터를 배포 하려면 필요 합니다.
1. **클러스터 인증서**  
   Service Fabric을 배포 하는 경우 key Vault를 추가할 X.509 서버 인증서입니다. 
   - 합니다 **CN** 이 인증서는 완전히 정규화 된 도메인 이름 (FQDN) 사용자가 만든 Service Fabric 클러스터의 일치 해야 합니다. 
   - 인증서 형식 공개 및 개인 키가 필요한 대로 PFX 여야 합니다. 
   참조 [요구 사항](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) 이 서버 쪽 인증서를 만들기 위한 합니다.

    > [!NOTE]  
    > X.509 서버 인증서는 자체 서명 된 인증서 내부 테스트용으로 사용할 수 있습니다. 자체 서명 된 인증서는 클러스터의 FQDN과 일치 필요가 없습니다.

1.  **관리 클라이언트 인증서** 클라이언트는 자체 서명 될 수 있는 Service Fabric 클러스터에 인증 하는 인증서입니다. 참조 [요구 사항](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) 이 클라이언트 인증서 만들기에 대 한 합니다.

1.  **Azure Stack Marketplace에서 다음 항목을 사용할 수 있어야 합니다.**
     - **Windows Server 2016** – 템플릿은 Windows Server 2016 이미지를 사용 하 여 클러스터를 만듭니다.  
     - **고객 스크립트 확장** -Microsoft에서 가상 머신 확장 합니다.  
     - **PowerShell 필요한 구성 단계** -Microsoft에서 가상 머신 확장 합니다.


## <a name="add-a-secret-to-key-vault"></a>Key Vault에 비밀 추가
Service Fabric 클러스터를 배포 하려면 올바른 KeyVault를 지정 해야 합니다 *비밀 식별자* 또는 Service Fabric 클러스터에 대 한 URL입니다. Azure Resource Manager 템플릿에서 key Vault 입력 변수로 사용 합니다. 다음 템플릿에 Service Fabric 클러스터를 설치 하는 경우 클러스터 인증서를 검색 합니다.

> [!IMPORTANT]  
> PowerShell을 사용 하 여 Service Fabric 사용에 대 한 key Vault에 비밀을 추가 해야 합니다. 포털을 사용 하지 마세요.  

다음 스크립트를 사용 하 여 KeyVault를 만들고 추가 합니다 *클러스터 인증서* 되도록 합니다. (참조를 [필수 구성 요소](#prerequisites).) 스크립트를 실행 하기 전에 샘플 스크립트를 검토 하 고 환경에 맞게 표시 된 매개 변수를 업데이트 합니다. 이 스크립트는 Azure Resource Manager 템플릿에 제공 해야 하는 값을 출력도. 

> [!TIP]  
> 스크립트가 성공 하려면 계산, 네트워크, 저장소 및 key Vault에 대 한 서비스를 포함 하는 공개 제공 되어야 합니다. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 KeyVault 관리](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell)합니다.

## <a name="deploy-the-marketplace-item"></a>Marketplace 항목 배포

1. 사용자 포털에서로 이동 **+ 리소스 만들기** > **계산** > **Service Fabric 클러스터**합니다. 

   ![Service Fabric 클러스터를 선택 합니다.](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. 각 페이지에 대 한 같은 *기본 사항*, 배포 양식을 작성 하세요. 값의 잘 모를 경우 기본값을 사용 합니다. 선택 **확인** 다음 페이지로 이동 합니다.

   ![기본 사항](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. 에 *네트워크 설정을* 페이지를 열려면 응용 프로그램에 대 한 특정 포트를 지정할 수 있습니다.

   ![네트워크 설정](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. 에 *보안* 페이지에서 가져온 값을 추가 합니다 [Azure key Vault 만들기](#add-a-secret-to-key-vault) 및 암호를 업로드 합니다.

   에 대 한는 *관리 클라이언트 인증서 지문이*, 지문을 입력 합니다 *관리 클라이언트 인증서*합니다. (참조를 [필수 구성 요소](#prerequisites).)
   
   - 원본 키 자격 증명 모음: 전체 지정할 *keyVault id* 스크립트 결과 문자열입니다. 
   - 클러스터 인증서 URL: 전체 URL을 지정 합니다 *비밀 Id* 스크립트 결과에서. 
   - 클러스터 인증서 지문: 지정 된 *클러스터 인증서 지문과* 스크립트 결과에서.
   - 관리자 클라이언트 인증서 지문: 지정 된 *관리 클라이언트 인증서 지문이* 필수 구성 요소에서 만든 합니다. 

   ![스크립트 출력](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![보안](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. 마법사를 완료 하 고 선택한 **만들기** Service Fabric 클러스터를 배포 합니다.



## <a name="access-the-service-fabric-cluster"></a>Service Fabric 클러스터에 액세스
Service Fabric Explorer 또는 Service Fabric PowerShell을 사용 하 여 Service Fabric 클러스터에 액세스할 수 있습니다.


### <a name="use-service-fabric-explorer"></a>Service Fabric Explorer를 사용 합니다.
1.  웹 브라우저 관리자 클라이언트 인증서에 대 한 액세스 권한이 확인 하 여 Service Fabric 클러스터에 인증할 수 있습니다.  

    a. Internet Explorer를 열고 이동할 **인터넷 옵션** > **콘텐츠** > **인증서**합니다.
  
    b. 인증서 선택 **가져오기** 시작 하는 *인증서 가져오기 마법사*를 클릭 하 고 **다음**합니다. 에 *가져올 파일* 페이지 **찾아보기**, 선택한를 **관리자 클라이언트 인증서** Azure Resource Manager 템플릿에 제공 합니다.
        
       > [!NOTE]  
       > 이 인증서는 이전에 KeyVault에 추가 된 클러스터 인증서 없습니다.  

    다. "개인 정보 교환" 파일 탐색기 창의 확장 드롭다운 목록에서 선택 했는지 확인 합니다.  

       ![개인 정보 교환](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. 에 *인증서 저장소* 페이지에서 **개인**, 한 다음 마법사를 완료 합니다.  
       ![인증서 저장소](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Service Fabric 클러스터의 FQDN을 찾으려면:  

    a. 클러스터에 Service Fabric과 사용 하 여 연결 된 리소스 그룹을 찾아서 이동 합니다 *공용 IP 주소* 리소스입니다. 열려는 공용 IP 주소와 연결 된 개체를 선택 합니다 *공용 IP 주소* 블레이드입니다.  

      ![공용 IP 주소](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. 공용 IP 주소 블레이드에서 FQDN으로 표시 *DNS 이름*합니다.  

      ![DNS 이름](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. 클라이언트 연결 끝점 및 Service Fabric Explorer에 대 한 URL을 찾으려면, 템플릿 배포의 결과 검토 합니다.

1. 브라우저에서 https://로 이동*FQDN*: 19080 합니다. 바꿉니다 *FQDN* 2 단계에서 Service Fabric 클러스터의 FQDN을 사용 하 여 합니다.   
   자체 서명 된 인증서를 사용한 경우에 연결이 안전 하지 않다는 경고가 얻게 됩니다. 웹 사이트를 계속 하려면 선택 **자세한 정보**를 차례로 **웹 페이지로 이동**합니다. 

1. 사이트에 인증에 사용할 인증서를 선택 해야 합니다. 선택 **더 많은 선택 사항**를 적절 한 인증서를 선택 하 고 클릭 **확인** Service Fabric Explorer에 연결 합니다. 

   ![인증](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Service Fabric PowerShell 사용

1. 설치 합니다 *Microsoft Azure Service Fabric SDK* 에서 [Windows에서 개발 환경 준비](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) Azure Service Fabric 설명서에서.  

1. 설치를 완료 한 후에 서비스 패브릭 cmdlet은 PowerShell에서 액세스할 수 있는지 확인 하도록 시스템 환경 변수를 구성 합니다.  
    
    a. 로 이동 **Control Panel** > **시스템 및 보안** > **System**를 선택한 후 **고급 시스템 설정**.  
    
      ![제어판](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. 에 **고급** 탭의 *시스템 속성*를 선택 **환경 변수**합니다.  

    다. 에 대 한 *시스템 변수*을 편집 **경로** 했는지 **c:\\Program Files\\Microsoft Service Fabric\\bin\\패브릭 \\Fabric.Code** 환경 변수의 목록 맨 위에 있는 됩니다.  

      ![환경 변수 목록](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. 환경 변수의 순서를 변경한 후 PowerShell을 다시 시작 하 고 Service Fabric 클러스터에 액세스 하려면 다음 PowerShell 스크립트를 실행 하십시오.

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > 방법이 없는 *https://* 스크립트에서 클러스터의 이름 앞입니다. 19000 포트가 필요 합니다.
 
