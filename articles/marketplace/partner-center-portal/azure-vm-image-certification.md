---
title: Azure 가상 시스템 인증 - Azure 마켓플레이스
description: 상용 시장에서 가상 머신 오퍼를 테스트하고 제출하는 방법을 알아봅니다.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731119"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Azure 가상 머신(VM) 이미지 인증

> [!NOTE]
> Azure VM 오퍼의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털의 [Azure Key Vault에 대한 인증서 만들기의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) 지침을 계속 따라 오퍼를 관리하십시오.

이 문서에서는 상용 마켓플레이스에서 VM(가상 시스템) 이미지를 테스트하고 제출하여 최신 Azure Marketplace 게시 요구 사항을 충족하는지 확인하는 방법을 설명합니다.

VM 오퍼를 제출하기 전에 다음 단계를 완료하십시오.

1. 인증서를 만들고 배포합니다.
2. 일반화된 이미지를 사용하여 Azure VM을 배포합니다.
3. 유효성 검사를 실행합니다.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Azure 키 자격 증명 모음에 대한 인증서 만들기 및 배포

이 섹션에서는 Azure 호스팅 가상 시스템에 Windows 원격 관리(WinRM) 연결을 설정하는 데 필요한 자체 서명된 인증서를 만들고 배포하는 방법에 대해 설명합니다.

### <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault용 인증서 만들기

이 프로세스는 다음 세 단계로 구성됩니다.

1. 보안 인증서를 만듭니다.
2. Azure 키 자격 증명 모음을 만들어 인증서를 저장합니다.
3. 인증서를 키 자격 증명 모음에 저장합니다.

이 작업에 대한 새 또는 기존 Azure 리소스 그룹을 사용할 수 있습니다.

#### <a name="create-the-security-certificate"></a>보안 인증서 만들기

다음 Azure PowerShell 스크립트를 편집하고 실행하여 로컬 폴더에 인증서 파일(.pfx)을 만듭니다. 다음 표에 표시된 매개 변수의 값을 바꿉습니다.

| **매개 변수** | **설명** |
| --- | --- |
| $certroopath | 로컬 폴더에 .pfx 파일을 저장합니다. |
| $location | Azure 표준 지리적 위치 중 하나입니다. |
| $vmName | 계획된 Azure 가상 시스템의 이름입니다. |
| $certname | 인증서 의 이름; 계획된 VM의 정규화된 도메인 이름과 일치해야 합니다. |
| $certpassword | 인증서의 암호는 계획된 VM에 사용되는 암호와 일치해야 합니다. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
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
> 다양한 매개 변수의 값을 유지하기 위해 이러한 단계에서 동일한 Azure PowerShell 콘솔 세션을 열고 실행합니다.

> [!WARNING]
> 이 스크립트를 저장하는 경우 보안 정보(암호)가 포함되어 있으므로 보안 위치에만 저장합니다.

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Azure 키 자격 증명 모음을 만들어 인증서를 저장합니다.

아래 템플릿의 내용을 로컬 컴퓨터의 파일에 복사합니다. 아래 예제 스크립트에서 이 `C:\certLocation\keyvault.json`리소스는 )입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

다음 Azure PowerShell 스크립트를 편집하고 실행하여 Azure 키 자격 증명 모음 및 관련 리소스 그룹을 만듭니다. 다음 표에 표시된 매개 변수의 값 바꾸기

| **매개 변수** | **설명** |
| --- | --- |
| $postfix | 배포 식별자에 연결된 임의의 숫자 문자열입니다. |
| $rgName | 만들 Azure 리소스 그룹(RG) 이름입니다. |
| $location | Azure 표준 지리적 위치 중 하나입니다. |
| $kvTemplateJson | 키 자격 증명 모음에 대한 리소스 관리자 템플릿을 포함하는 파일 경로(keyvault.json)입니다. |
| $kvname | 새 키 자격 증명 모음의 이름입니다.|
|   |   |

```PowerShell
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

#### <a name="store-the-certificates-to-the-key-vault"></a>인증서를 키 자격 증명 모음에 저장합니다.

이 스크립트를 사용하여 .pfx 파일에 포함된 인증서를 새 키 자격 증명 모음에 저장합니다.

```PowerShell
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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>일반화된 이미지를 사용하여 Azure VM 배포

이 섹션에서는 새 Azure VM 리소스를 만들기 위해 일반화된 VHD 이미지를 배포하는 방법에 대해 설명합니다. 이 프로세스에서는 제공된 Azure 리소스 관리자 템플릿 및 Azure PowerShell 스크립트를 사용합니다.

### <a name="prepare-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿 준비

VHD 배포에 대한 다음 Azure 리소스 관리자 템플릿을 VHDtoImage.json이라는 로컬 파일에 복사합니다. 다음 스크립트는 이 JSON을 사용하도록 로컬 컴퓨터의 위치를 요청합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

다음 매개 변수에 대한 값을 제공하기 위해 이 파일을 편집합니다.

| **매개 변수** | **설명** |
| --- | --- |
| ResourceGroupName | 기존 Azure 리소스 그룹 이름입니다. 일반적으로 키 자격 증명 모음과 동일한 RG를 사용합니다. |
| TemplateFile | 파일 VHDtoImage.json에 대한 전체 경로 이름입니다. |
| userStorageAccountName | 스토리지 계정 이름 |
| sNameForPublicIP | 공용 IP의 DNS 이름; 소문자여야 합니다. |
| subscriptionId | Azure 구독 식별자입니다. |
| 위치 | 리소스 그룹의 표준 Azure 지리적 위치입니다. |
| vmName | 가상 시스템의 이름입니다. |
| vaultName | 키 자격 증명 모음의 이름입니다. |
| vaultResourceGroup | Key Vault의 리소스 그룹 |
| certificateUrl | 키 자격 증명 모음에 저장된 버전을 포함하여 인증서의 웹 `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`주소(URL)는 다음과 같은 것입니다. |
| vhdUrl | 가상 하드 디스크의 웹 주소입니다. |
| vmSize | 가상 시스템 인스턴스의 크기입니다. |
| publicIPAddressName | 공용 IP 주소의 이름입니다. |
| virtualNetworkName | 가상 네트워크의 이름입니다. |
| nicName | 가상 네트워크에 대한 네트워크 인터페이스 카드의 이름입니다. |
| adminUserName | 관리자 계정의 사용자 이름입니다. |
| adminPassword | 관리자 암호입니다. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Azure VM 배포

`$storageaccount` 다음 스크립트를 복사하고 편집하여 및 `$vhdUrl` 변수에 대한 값을 제공합니다. 이 스크립트를 실행하여 일반화된 기존 VHD에서 Azure VM 리소스를 만듭니다.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>유효성 검사 실행

배포된 이미지에서 유효성 검사를 실행하는 방법에는 두 가지가 있습니다.

- Azure 인증에 대한 인증 테스트 도구 사용
- 자체 테스트 API 사용

### <a name="download-and-run-the-certification-test-tool"></a>인증 테스트 도구 다운로드 및 실행

Azure 인증에 대한 인증 테스트 도구는 로컬 Windows 컴퓨터에서 실행되지만 Azure 기반 Windows 또는 Linux VM을 테스트합니다. 사용자 VM 이미지를 Microsoft Azure와 함께 사용할 수 있으며 VHD 준비에 대한 지침과 요구 사항이 충족되었음을 확인합니다. 도구의 출력은 파트너 센터 포털에 업로드하여 VM 인증을 요청하는 호환성 보고서입니다.

1. 가장 최근의 [Azure Certified용 인증 테스트 도구](https://www.microsoft.com/download/details.aspx?id=44299)를 다운로드하여 설치합니다.
2. 인증 도구를 연 다음 **새 테스트 시작을**선택합니다.
3. **테스트 정보** 화면에서 테스트 실행에 대한 **테스트 이름**이름을 입력합니다.
4. Windows 서버 또는 Linux중 VM용 **플랫폼을** 선택합니다. 플랫폼 선택은 나머지 옵션에 영향을 줍니다.
5. VM에서 이 데이터베이스 서비스를 사용하는 경우 **Azure SQL Database 테스트** 확인란을 선택합니다.

### <a name="connect-the-certification-tool-to-a-vm-image"></a>VM 이미지에 인증 도구 연결

이 도구는 [Azure PowerShell을](https://docs.microsoft.com/powershell/) 사용하여 Windows 기반 VM에 연결하고 [SSH.Net](https://www.ssh.com/ssh/protocol/)통해 Linux VM에 연결합니다.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Linux VM 이미지에 인증 도구 연결

1. **SSH 인증** 모드: 암호 인증 또는 키 파일 인증을 선택합니다.
2. 암호 기반 인증을 사용하는 경우 **VM DNS 이름,** **사용자 이름**및 **암호**에 대한 값을 입력합니다. 기본 **SSH 포트** 번호를 변경할 수도 있습니다.

    ![Azure 인증 테스트 도구, Linux VM 이미지의 암호 인증](media/avm-cert2.png)

3. 키 파일 기반 인증을 사용하는 경우 **VM DNS Name**, **사용자 이름** 및 **프라이빗 키** 위치에 대한 값을 입력합니다. **암호를** 포함하거나 기본 **SSH 포트** 번호를 변경할 수도 있습니다.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Windows 기반 VM 이미지에 인증 도구 연결**

1. 정규화된 **VM DNS** 이름(예: MyVMName.Cloudapp.net)을 입력합니다.
2. **사용자 이름** 및 **암호**에 대한 값을 입력합니다.

    ![Azure 인증 테스트 도구, Windows 기반 VM 이미지의 암호 인증](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>인증 테스트 실행

인증 도구에서 VM 이미지에 대한 매개 변수 값을 지정한 후 **테스트 연결을** 선택하여 VM에 대한 유효한 연결을 만듭니다. 연결이 확인되면 **다음**을 선택하여 테스트를 시작합니다. 테스트가 완료되면 테스트 결과가 테이블에 표시됩니다. 상태 열에는 각 테스트에 대해 통과/실패/경고가 표시됩니다. 테스트 중 하나라도 실패하면 이미지가 인증되지 _않습니다_. 이 경우 요구 사항 및 실패 메시지를 검토하고 제안된 변경 내용을 수정한 다음 테스트를 다시 실행합니다.

자동화된 테스트가 완료되면 **설문지** 화면의 두 탭인 **일반 평가** 및 **커널 사용자 지정탭에**VM 이미지에 대한 추가 정보를 제공한 다음 **다음을**선택합니다.

마지막 화면에서는 Linux VM 이미지에 대한 SSH 액세스 정보 및 예외를 찾고 있는 경우 실패한 평가에 대한 설명과 같은 자세한 정보를 제공할 수 있습니다.

마지막으로 **보고서 생성을** 선택하여 설문지에 대한 답변과 함께 실행된 테스트 사례에 대한 테스트 결과 및 로그 파일을 다운로드합니다. VHD와 동일한 컨테이너에 결과를 저장합니다.

## <a name="next-step"></a>다음 단계

- [각 VHD에 대해 균일한 리소스 식별자(URI) 생성](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
