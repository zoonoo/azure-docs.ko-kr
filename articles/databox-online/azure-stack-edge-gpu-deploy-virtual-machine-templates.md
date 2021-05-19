---
title: 템플릿을 통해 Azure Stack Edge Pro 디바이스에 VM 배포
description: 템플릿을 사용하여 Azure Stack Edge Pro 디바이스에서 VM(가상 머신)을 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: cf236396f080af9676f211c42178ddda6a794420
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568344"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>템플릿을 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 자습서에서는 템플릿을 사용하여 Azure Stack Edge Pro 디바이스에서 VM을 만들고 관리하는 방법을 설명합니다. 이러한 템플릿은 VM을 위한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이러한 템플릿에서 배포할 리소스와 해당 리소스의 속성을 지정합니다.

템플릿은 런타임 시 파일에서 매개 변수를 입력으로 사용할 수 있으므로 다양한 환경에서 유연하게 사용할 수 있습니다. 표준 이름 지정 구조는 템플릿의 경우에는 `TemplateName.json`이고 매개 변수 파일의 경우에는 `TemplateName.parameters.json`입니다. ARM 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿이란?](../azure-resource-manager/templates/overview.md)을 참조하세요.

이 자습서에서는 리소스를 만들기 위해 미리 작성된 샘플 템플릿을 사용합니다. 템플릿 파일을 편집할 필요가 없으며 `.parameters.json` 파일만 수정하여 머신에 대한 배포를 사용자 지정할 수 있습니다. 

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

여러 디바이스에 Azure Stack Edge Pro VM을 배포하려면 전체 집합에 sysprep된 단일 VHD를 사용하고, 배포에 동일한 템플릿을 사용하고, 각 배포 위치에 대해 해당 템플릿의 매개 변수를 약간만 변경하면 됩니다(여기서와 같이 수작업으로 변경하거나 프로그래밍 방식으로 변경 가능). 

템플릿을 사용하는 배포 워크플로의 개략적인 요약은 다음과 같습니다.

1. **사전 요구 사항 구성** - 디바이스, 클라이언트 및 VM에 대한 세 가지 유형의 사전 요구 사항이 있습니다.

    1. **디바이스 필수 구성 요소**

        1. 디바이스에서 Azure Resource Manager에 연결합니다.
        2. 로컬 UI를 통해 컴퓨팅을 사용하도록 설정합니다.

    2. **클라이언트 필수 조건**

        1. 클라이언트에서 VM 템플릿 및 관련 파일을 다운로드합니다.
        1. 선택적으로 클라이언트에서 TLS 1.2를 구성합니다.
        1. 클라이언트에서 [Microsoft Azure Storage Explorer를 다운로드하고 설치합니다](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

    3. **VM 필수 구성 요소**

        1. 모든 VM 리소스를 포함할 디바이스 위치에 리소스 그룹을 만듭니다.
        1. VM 이미지를 만드는 데 사용되는 VHD를 업로드할 스토리지 계정을 만듭니다.
        1. 디바이스에 액세스하는 클라이언트의 DNS 또는 호스트 파일에 로컬 스토리지 계정 URI를 추가합니다.
        1. 디바이스 및 디바이스에 액세스하는 로컬 클라이언트에 Blob Storage 인증서를 설치합니다. 선택적으로 스토리지 탐색기에 Blob Storage 인증서를 설치합니다.
        1. VHD를 만들고 앞서 만든 스토리지 계정에 업로드합니다.

2. **템플릿에서 VM 만들기**

    1. `CreateImage.parameters.json` 매개 변수 파일 및 `CreateImage.json` 배포 템플릿을 사용하여 VM 이미지를 만듭니다.
    1. `CreateVM.parameters.json`매개 변수 파일 및 `CreateVM.json` 배포 템플릿을 사용하여 이전에 만든 리소스로 VM을 만듭니다.

## <a name="device-prerequisites"></a>디바이스 필수 구성 요소

Azure Stack Edge Pro 디바이스에서 이러한 필수 구성 요소를 구성합니다.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>클라이언트 필수 조건

Azure Stack Edge Pro 디바이스에 액세스하는 데 사용할 클라이언트에서 이러한 필수 구성 요소를 구성합니다.

1. VHD를 업로드하는 데 사용할 [스토리지 탐색기를 다운로드](https://azure.microsoft.com/features/storage-explorer/)합니다. 또는 AzCopy를 다운로드하여 VHD를 업로드할 수 있습니다. 이전 버전의 AzCopy를 실행하는 경우 클라이언트 컴퓨터에서 TLS 1.2를 구성해야 할 수 있습니다. 
1. 클라이언트 시스템에 [VM 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates)합니다. 작업 디렉터리로 사용할 디렉터리에 압축을 풉니다.


## <a name="vm-prerequisites"></a>VM 필수 구성 요소

VM 생성에 필요한 리소스를 생성하려면 이러한 필수 구성 요소를 구성합니다. 

    
### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 스토리지 계정, 디스크, 관리 디스크와 같은 Azure 리소스가 배포 및 관리형 논리적 컨테이너입니다.

> [!IMPORTANT]
> 모든 리소스는 디바이스와 동일한 위치에 생성되며 위치는 **DBELocal** 로 설정됩니다.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

샘플 출력은 다음과 같습니다.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

이전 단계에서 만든 리소스 그룹을 사용하여 새 스토리지 계정을 만듭니다. 이 계정은 VM에 대한 가상 디스크 이미지를 업로드하는 데 사용되는 **로컬 스토리지 계정** 입니다.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager를 통해 로컬 중복 스토리지(표준_LRS 또는 프리미엄_LRS)와 같은 로컬 스토리지 계정만 만들 수 있습니다. 계층형 스토리지 계정을 만들려면 [Azure Stack Edge Pro에서 스토리지 계정 추가, 연결](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)의 단계를 참조하세요.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

스토리지 계정 키를 얻으려면 `Get-AzureRmStorageAccountKey` 명령을 실행합니다. 이 명령의 샘플 출력이 여기에 표시됩니다.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>호스트 파일에 Blob URI 추가

Blob Storage에 연결하는 데 사용하는 클라이언트의 호스트 파일에 Blob URI를 이미 추가했는지 확인합니다. **관리자로 메모장을 실행** 하고 `C:\windows\system32\drivers\etc\hosts`의 blob URI에 다음 항목을 추가합니다.

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

일반적인 환경에서는 모든 스토리지 계정이 `*.blob.devicename.domainname.com` 항목이 있는 Azure Stack Edge Pro 디바이스를 가리키도록 DNS를 구성합니다.

### <a name="optional-install-certificates"></a>(선택 사항) 인증서 설치

*http* 를 사용하여 스토리지 탐색기를 통해 연결하려면 이 단계를 건너뜁니다. *https* 를 사용하는 경우 스토리지 탐색기에 적절한 인증서를 설치해야 합니다. 이 경우 Blob 엔드포인트 인증서를 설치합니다. 자세한 내용은 [인증서 관리](azure-stack-edge-gpu-manage-certificates.md)에서 인증서를 만들고 업로드하는 방법을 참조하세요. 

### <a name="create-and-upload-a-vhd"></a>VHD 만들기 및 업로드

이후 단계에서 업로드하는 데 사용할 수 있는 가상 디스크 이미지가 있는지 확인하세요. [VM 이미지 만들기](azure-stack-edge-gpu-create-virtual-machine-image.md)의 단계를 따릅니다. 

이전 단계에서 만든 로컬 스토리지 계정의 페이지 Blob에 사용할 디스크 이미지를 복사합니다. [스토리지 탐색기](https://azure.microsoft.com/features/storage-explorer/) 또는 [AzCopy와 같은 도구를 사용하여 이전 단계에서 만든 스토리지 계정](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd)에 VHD를 업로드할 수 있습니다. 

### <a name="use-storage-explorer-for-upload"></a>업로드에 스토리지 탐색기 사용

1. Storage Explorer를 엽니다. **편집** 으로 이동하여 애플리케이션이 **대상 Azure Stack API** 로 설정되어 있는지 확인합니다.

    ![대상을 Azure Stack API로 설정](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. PEM 형식으로 클라이언트 인증서를 설치합니다. **편집 > SSL 인증서> 인증서 가져오기** 로 이동합니다. 클라이언트 인증서를 가리킵니다.

    ![Blob Storage 엔드포인트 인증서 가져오기](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - 디바이스 생성 인증서를 사용하는 경우 Blob Storage 엔드포인트 `.cer` 인증서를 다운로드하여 `.pem` 형식으로 변환합니다. 다음 명령을 실행합니다. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - 자체 인증서를 가져오는 경우 `.pem` 형식의 서명 체인 루트 인증서를 사용합니다.

3. 인증서를 가져온 후 변경 사항을 적용하려면 스토리지 탐색기를 다시 시작합니다.

    ![Storage Explorer 다시 시작](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. 왼쪽 창에서 **스토리지 계정** 을 마우스 오른쪽 단추로 클릭하고 **Azure Storage에 연결** 을 선택합니다. 

    ![Azure Storage에 연결 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. **스토리지 계정 이름 및 키 사용** 을 선택합니다. **다음** 을 선택합니다.

    ![Azure Storage에 연결 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. **이름 및 키로 연결** 에서 **표시 이름**, **스토리지 계정 이름**, Azure Storage **계정 키** 를 제공합니다. **다른** 스토리지 도메인을 선택한 다음 `<device name>.<DNS domain>` 연결 문자열을 제공합니다. 스토리지 탐색기에 인증서를 설치하지 않은 경우 **HTTP 사용** 옵션을 선택합니다. **다음** 을 선택합니다.

    ![이름 및 키로 연결](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. **연결 요약** 을 검토하고 **연결** 을 선택합니다.

8. 스토리지 계정이 왼쪽 창에 나타납니다. 스토리지 계정을 선택하고 확장합니다. **Blob 컨테이너** 를 선택하고, 마우스 오른쪽 단추로 클릭하여 **Blob 컨테이너 만들기** 를 선택합니다. Blob 컨테이너의 이름을 제공합니다.

9. 방금 만든 컨테이너를 선택하고 오른쪽 창에서 **업로드 > 파일 업로드** 를 선택합니다. 

    ![VHD 파일 1 업로드](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. **선택한 파일** 에서 업로드할 VHD를 찾아서 가리킵니다. **페이지 Blob** 으로 **Blob 유형** 을 선택하고 **업로드** 를 선택합니다.

    ![VHD 파일 2 업로드](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. VHD가 Blob 컨테이너에 로드되면 VHD를 선택하고 마우스 오른쪽 단추를 클릭한 다음 **속성** 을 선택합니다. 

    ![VHD 파일 3 업로드](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. 이후 단계에서 사용할 **URI** 를 복사하고 저장합니다.

    ![URI 복사](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>VM용 이미지 만들기

VM의 이미지를 만들려면 `CreateImage.parameters.json`매개 변수 파일을 편집한 다음 이 매개 변수 파일을 사용하는 템플릿`CreateImage.json`을 배포합니다.


### <a name="edit-parameters-file"></a>매개 변수 파일 편집

`CreateImage.parameters.json` 파일은 다음 매개 변수를 사용합니다. 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Azure Stack Edge Pro 디바이스에 대한 다음 값을 포함하도록 `CreateImage.parameters.json` 파일을 편집합니다.

1. 업로드할 VHD에 해당하는 OS 유형을 제공합니다. OS 유형은 Windows 또는 Linux일 수 있습니다.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. 이미지 URI를 이전 단계에서 업로드한 이미지의 URI로 변경합니다.

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   스토리지 탐색기에서 *http* 를 사용하는 경우 URI를 *http* URI로 변경합니다.

3. 고유한 이미지 이름을 제공합니다. 이 이미지는 이후 단계에서 VM을 만드는 데 사용됩니다. 

   다음은 이 문서에서 사용되는 샘플 json입니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. 매개 변수 파일을 저장합니다.


### <a name="deploy-template"></a>템플릿 배포 

`CreateImage.json` 템플릿을 배포합니다. 이 템플릿은 이후 단계에서 VM을 만드는 데 사용될 이미지 리소스를 배포합니다.

> [!NOTE]
> 템플릿을 배포할 때 인증 오류가 발생하면 이 세션에 대한 Azure 자격 증명이 만료되었을 수 있습니다. `login-AzureRM` 명령을 다시 실행하여 Azure Stack Edge Pro 디바이스에서 Azure Resource Manager에 다시 연결합니다.

1. 다음 명령 실행: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    이 명령은 이미지 리소스를 배포합니다. 리소스를 쿼리하려면 다음 명령을 실행합니다.

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    다음은 성공적으로 생성된 이미지의 샘플 출력입니다.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>VM 만들기

### <a name="edit-parameters-file-to-create-vm"></a>매개 변수 파일을 편집하여 VM 생성
 
VM을 만들려면 `CreateVM.parameters.json` 매개 변수 파일을 사용합니다. 다음 매개 변수를 사용합니다.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

`CreateVM.parameters.json`에서 Azure Stack Edge Pro 디바이스에 적절한 매개 변수를 할당합니다.

1. 고유한 이름, 네트워크 인터페이스 이름 및 IP 구성 이름을 제공합니다. 
1. 사용자 이름, 암호 및 지원되는 VM 크기를 입력합니다.
1. 컴퓨팅을 위해 네트워크 인터페이스를 사용하도록 설정했을 때 해당 네트워크 인터페이스에 가상 스위치와 가상 네트워크가 자동으로 생성되었습니다. 기존 가상 네트워크를 쿼리하여 Vnet 이름, 서브넷 이름 및 Vnet 리소스 그룹 이름을 가져올 수 있습니다.

    다음 명령 실행:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    샘플 출력은 다음과 같습니다.
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Vnet 이름에는 ASEVNET, 서브넷 이름에는 ASEVNETsubNet, Vnet 리소스 그룹 이름에는 ASERG를 사용합니다.
    
1. 이제 위에서 정의한 서브넷 네트워크에 있는 VM에 할당할 고정 IP 주소가 필요합니다. 매개 변수 파일에서 **PrivateIPAddress** 를 이 주소로 바꿉니다. VM이 로컬 DCHP 서버에서 IP 주소를 받도록 하려면 `privateIPAddress` 값을 비워둡니다.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. 매개 변수 파일을 저장합니다.

    다음은 이 문서에서 사용되는 샘플 json입니다.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>VM을 만드는 템플릿 배포

VM을 만드는 템플릿 `CreateVM.json`을 배포합니다. 이 템플릿은 기존 VNet에서 네트워크 인터페이스를 만들고 배포된 이미지에서 VM을 만듭니다.

1. 다음 명령 실행: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    VM 만들기에는 15~20분이 소요됩니다. 다음은 성공적으로 만든 VM의 샘플 출력입니다.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    `–AsJob` 매개 변수를 사용하여 `New-AzureRmResourceGroupDeployment` 명령을 비동기식으로 실행할 수도 있습니다. 다음은 cmdlet이 백그라운드에서 실행될 때의 샘플 출력입니다. 그런 다음 `Get-Job` cmdlet을 사용하여 만든 작업의 상태를 쿼리할 수 ​​있습니다.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. VM이 성공적으로 프로비저닝되었는지 확인합니다. 다음 명령 실행:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>VM에 연결

Windows 또는 Linux VM을 만들었는지 여부에 따라 연결 단계가 다를 수 있습니다.

### <a name="connect-to-windows-vm"></a>Windows VM에 연결

Windows VM에 연결하려면 다음 단계를 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Linux VM에 연결

Linux VM에 연결하려면 다음 단계를 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)