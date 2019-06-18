---
title: Azure Key Vault 인증서를 만듭니다. | Azure Marketplace
description: Azure에서 배포한 VHD에서 VM을 등록하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 5163aa0a9195aa712fa333667b3f7ccf227469be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938397"
---
# <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault용 인증서 만들기

이 문서에서는 Azure 호스티드 VM(가상 머신)에 대한 Windows 원격 관리(WinRM) 연결을 설정하는 데 필요한 자체 서명된 인증서를 프로비전하는 방법을 설명합니다. 이 프로세스는 다음 세 단계로 구성됩니다.

1.  보안 인증서를 만듭니다. 
2.  이 인증서를 저장할 Azure Key Vault를 만듭니다. 
3.  이 Key Vault에 인증서를 저장합니다. 

이 작업에 대한 새 또는 기존 Azure 리소스 그룹을 사용할 수 있습니다.  다음 설명에서는 이전 접근 방식이 사용됩니다.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>인증서 만들기

Azure Powershell 스크립트를 편집한 후 실행하여 로컬 폴더에 인증서 파일(.pfx)을 만듭니다.  다음 매개 변수 값을 바꾸어야 합니다.

|  **매개 변수**        |   **설명**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | .pfx 파일을 저장할 로컬 폴더  |
| `$location`    | Azure 표준 지리적 위치 중 하나  |
| `$vmName`      | 계획된 Azure Virtual Machine의 이름   |
| `$certname`    | 인증서의 이름으로, 계획된 VM의 정규화된 도메인 이름과 일치해야 합니다.  |
| `$certpassword` | 인증서에 대한 암호로, 계획된 VM에 사용되는 암호와 일치해야 합니다.  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> 이러한 단계 동안 PowerShell 콘솔 세션을 활성 상태로 유지하여 다양한 매개 변수 값이 보존되도록 합니다.

> [!WARNING]
> 이 스크립트는 보안 정보(암호)를 포함하므로 안전한 위치에 저장합니다.


## <a name="create-the-key-vault"></a>Key Vault 만들기

[Key Vault 배포 템플릿](./cpp-key-vault-deploy-template.md)의 내용을 로컬 컴퓨터의 파일에 복사합니다. (아래 예제 스크립트에서 이 리소스는 `C:\certLocation\keyvault.json`입니다.)  다음 Azure Powershell 스크립트를 편집한 후 실행하여 Azure Key Vault 인스턴스 및 연결된 리소스 그룹을 만듭니다.  다음 매개 변수 값을 바꾸어야 합니다.

|  **매개 변수**        |   **설명**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | 배포 식별자에 추가되는 임의의 숫자 문자열                     |
| `$rgName`             | 만들려는 Azure RG(리소스 그룹) 이름                                        |
|  `$location`          | Azure 표준 지리적 위치 중 하나                                  |
| `$kvTemplateJson`     | Key Vault에 대한 Resource Manager 템플릿을 포함하는 파일(keyvault.json)의 경로 |
| `$kvname`             | 새 Key Vault의 이름                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>인증서 저장

이제 다음 스크립트를 실행하여 .pfx 파일에 포함된 인증서를 새 Key Vault에 저장할 수 있습니다. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
    "@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>다음 단계

다음으로 [사용자 VM 이미지에서 VM을 배포](./cpp-deploy-vm-user-image.md)합니다.
