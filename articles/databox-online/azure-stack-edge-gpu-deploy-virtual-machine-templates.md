---
title: 템플릿을 통해 Azure Stack Edge Pro 장치에 Vm 배포
description: 템플릿을 사용 하 여 Azure Stack Edge Pro 장치에서 Vm (가상 머신)을 만들고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2922e9620f558e072cfc1264f7e661efd11a90fb
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437011"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>템플릿을 통해 Azure Stack Edge Pro GPU 장치에 Vm 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 자습서에서는 템플릿을 사용 하 여 Azure Stack Edge Pro 장치에서 VM을 만들고 관리 하는 방법을 설명 합니다. 이러한 템플릿은 VM에 대 한 인프라 및 구성을 정의 하는 JSON (JavaScript Object Notation) 파일입니다. 이러한 템플릿에서는 배포할 리소스 및 해당 리소스에 대 한 속성을 지정 합니다.

템플릿은 런타임에 파일의 입력으로 매개 변수를 사용할 수 있으므로 다양 한 환경에서 유연 합니다. 표준 명명 구조는 `TemplateName.json` 템플릿 및 매개 변수 파일에 대 한 것입니다 `TemplateName.parameters.json` . ARM 템플릿에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 이란?](../azure-resource-manager/templates/overview.md)을 참조 하세요.

이 자습서에서는 리소스를 만들기 위해 미리 작성 된 샘플 템플릿을 사용 합니다. 템플릿 파일을 편집할 필요는 없으며 `.parameters.json` 파일만 수정 하 여 컴퓨터에 배포를 사용자 지정할 수 있습니다. 

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

여러 장치에 Azure Stack Edge Pro Vm을 배포 하기 위해 전체 부에 대해 단일 sysprep VHD를 사용 하 고, 배포에 대해 동일한 템플릿을 사용 하 고, 각 배포 위치에 대 한 매개 변수를 변경 하는 것이 가능 합니다. 이러한 변경은 여기에서 수행 하는 것과 같은 방식으로 또는 프로그래밍 방식으로 변경 될 수 있습니다. 

템플릿을 사용한 배포 워크플로의 개략적인 요약은 다음과 같습니다.

1. **필수 구성 요소 구성** -장치, 클라이언트 및 VM에 대 한 세 가지 필수 구성 요소가 있습니다.

    1. **장치 필수 조건**

        1. 장치에서 Azure Resource Manager에 연결 합니다.
        2. 로컬 UI를 통해 계산을 사용 하도록 설정 합니다.

    2. **클라이언트 필수 조건**

        1. 클라이언트에서 VM 템플릿 및 관련 파일을 다운로드 합니다.
        1. 필요에 따라 클라이언트에서 TLS 1.2을 구성 합니다.
        1. 클라이언트에 [Microsoft Azure Storage 탐색기를 다운로드 하 여 설치](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) 합니다.

    3. **VM 필수 구성 요소**

        1. 모든 VM 리소스를 포함 하는 장치 위치에 리소스 그룹을 만듭니다.
        1. VM 이미지를 만드는 데 사용 되는 VHD를 업로드 하는 저장소 계정을 만듭니다.
        1. 장치에 액세스 하는 클라이언트의 DNS 또는 hosts 파일에 로컬 저장소 계정 URI를 추가 합니다.
        1. 장치 및 로컬 클라이언트에 장치에 액세스 하는 blob storage 인증서를 설치 합니다. 필요에 따라 Storage 탐색기에 blob storage 인증서를 설치 합니다.
        1. 이전에 만든 저장소 계정에 VHD를 만들고 업로드 합니다.

2. **템플릿에서 VM 만들기**

    1. `CreateImage.parameters.json`매개 변수 파일 및 배포 템플릿을 사용 하 여 VM 이미지를 만듭니다 `CreateImage.json` .
    1. `CreateVM.parameters.json`매개 변수 파일 및 배포 템플릿을 사용 하 여 이전에 만든 리소스를 사용 하 여 VM을 만듭니다 `CreateVM.json` .

## <a name="device-prerequisites"></a>장치 필수 조건

Azure Stack Edge Pro 장치에서 이러한 필수 구성 요소를 구성 합니다.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>클라이언트 필수 조건

Azure Stack Edge Pro 장치에 액세스 하는 데 사용 되는 클라이언트에서 이러한 필수 구성 요소를 구성 합니다.

1. VHD를 업로드 하는 데 사용 하는 경우 [Storage 탐색기를 다운로드](https://azure.microsoft.com/features/storage-explorer/) 합니다. 또는 AzCopy를 다운로드 하 여 VHD를 업로드할 수 있습니다. 이전 버전의 AzCopy를 실행 하는 경우 클라이언트 컴퓨터에서 TLS 1.2을 구성 해야 할 수 있습니다. 
1. 클라이언트 컴퓨터에 [VM 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates) 합니다. 작업 디렉터리로 사용할 디렉터리에 압축을 풉니다.


## <a name="vm-prerequisites"></a>VM 필수 구성 요소

이러한 필수 구성 요소를 구성 하 여 VM을 만드는 데 필요한 리소스를 만듭니다. 

    
### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 저장소 계정, 디스크, 관리 디스크와 같은 Azure 리소스가 배포 및 관리 되는 논리적 컨테이너입니다.

> [!IMPORTANT]
> 모든 리소스는 장치와 동일한 위치에 만들어지고 위치는 **Dbelocal** 로 설정 됩니다.

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

이전 단계에서 만든 리소스 그룹을 사용 하 여 새 저장소 계정을 만듭니다. 이 계정은 VM에 대 한 가상 디스크 이미지를 업로드 하는 데 사용 되는 **로컬 저장소 계정** 입니다.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 로컬 중복 저장소 (Standard_LRS 또는 Premium_LRS)와 같은 로컬 저장소 계정만 Azure Resource Manager를 통해 만들 수 있습니다. 계층화 된 저장소 계정을 만들려면 [Azure Stack Edge Pro에서 저장소 계정에 연결 추가](azure-stack-edge-j-series-deploy-add-storage-accounts.md)의 단계를 참조 하세요.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

저장소 계정 키를 가져오려면 `Get-AzureRmStorageAccountKey` 명령을 실행 합니다. 이 명령의 샘플 출력은 다음과 같습니다.

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

Blob storage에 연결 하는 데 사용 하는 클라이언트의 호스트 파일에 blob URI를 이미 추가 했는지 확인 합니다. **관리자 권한으로 메모장을 실행** 하 고에 blob URI에 대 한 다음 항목을 추가 합니다 `C:\windows\system32\drivers\etc\hosts` .

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

일반적인 환경에서는 모든 저장소 계정이 항목을 사용 하 여 Azure Stack Edge Pro 장치를 가리키도록 DNS를 구성 해야 `*.blob.devicename.domainname.com` 합니다.

### <a name="optional-install-certificates"></a>필드 인증서 설치

*Http* 를 사용 하 여 Storage 탐색기를 통해 연결 하는 경우이 단계를 건너뜁니다. *Https* 를 사용 하는 경우 Storage 탐색기에 적절 한 인증서를 설치 해야 합니다. 이 경우 blob 끝점 인증서를 설치 합니다. 자세한 내용은 [인증서 관리](azure-stack-edge-gpu-manage-certificates.md)에서 인증서를 만들고 업로드 하는 방법을 참조 하세요. 

### <a name="create-and-upload-a-vhd"></a>VHD 만들기 및 업로드

이후 단계에서 업로드 하는 데 사용할 수 있는 가상 디스크 이미지가 있는지 확인 합니다. [VM 이미지 만들기](azure-stack-edge-gpu-create-virtual-machine-image.md)의 단계를 따릅니다. 

이전 단계에서 만든 로컬 저장소 계정의 페이지 blob에 사용할 디스크 이미지를 복사 합니다. [Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 또는 AzCopy와 같은 도구를 사용 하 여 이전 단계에서 만든 [저장소 계정에 VHD를 업로드할](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) 수 있습니다. 

### <a name="use-storage-explorer-for-upload"></a>업로드에 Storage 탐색기 사용

1. Storage Explorer를 엽니다. **편집** 으로 이동 하 여 응용 프로그램이 **대상 Azure Stack api** 로 설정 되었는지 확인 합니다.

    ![대상을 Azure Stack Api로 설정](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. 클라이언트 인증서를 PEM 형식으로 설치 합니다. **편집 > SSL 인증서 > 인증서 가져오기** 로 이동 합니다. 클라이언트 인증서를 가리킵니다.

    ![Blob 저장소 끝점 인증서 가져오기](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - 장치에서 생성 한 인증서를 사용 하는 경우 blob storage 끝점 인증서를 형식으로 다운로드 하 여 변환 `.cer` `.pem` 합니다. 다음 명령을 실행합니다. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - 사용자 고유의 인증서를 가져오는 경우 서명 체인 루트 인증서를 형식으로 사용 `.pem` 합니다.

3. 인증서를 가져온 후에는 Storage 탐색기를 다시 시작 하 여 변경 내용을 적용 합니다.

    ![Storage Explorer 다시 시작](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. 왼쪽 창에서 **저장소 계정** 을 마우스 오른쪽 단추로 클릭 하 고 **Azure Storage에 연결을** 선택 합니다. 

    ![Azure Storage 1에 연결](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. **스토리지 계정 이름 및 키 사용** 을 선택합니다. **다음** 을 선택합니다.

    ![Azure Storage 2에 연결](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. **연결 이름 및 키** 에 **표시 이름**, **저장소 계정 이름**, Azure Storage **계정 키** 를 제공 합니다. **다른** 저장소 도메인을 선택한 다음 `<device name>.<DNS domain>` 연결 문자열을 입력 합니다. Storage 탐색기에서 인증서를 설치 하지 않은 경우 **HTTP 사용** 옵션을 선택 합니다. **다음** 을 선택합니다.

    ![이름 및 키로 연결](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. **연결 요약** 을 검토 하 고 **연결** 을 선택 합니다.

8. 저장소 계정이 왼쪽 창에 표시 됩니다. 저장소 계정을 선택 하 고 확장 합니다. **Blob 컨테이너** 를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **blob 컨테이너 만들기** 를 선택 합니다. Blob 컨테이너에 대 한 이름을 제공 합니다.

9. 방금 만든 컨테이너를 선택 하 고 오른쪽 창에서 **업로드 > 파일 업로드** 를 선택 합니다. 

    ![VHD 파일 업로드 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. **선택 된 파일** 에서 업로드 하려는 VHD를 찾아서 가리킵니다. **Blob 유형** 을 **페이지 blob** 으로 선택 하 고 **업로드** 를 선택 합니다.

    ![VHD 파일 업로드 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. VHD가 blob 컨테이너에 로드 되 면 VHD를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **속성** 을 선택 합니다. 

    ![VHD 파일 업로드 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. 이후 단계에서 사용할 **Uri** 를 복사 하 고 저장 합니다.

    ![URI 복사](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>VM에 대 한 이미지 만들기

VM에 대 한 이미지를 만들려면 `CreateImage.parameters.json` 매개 변수 파일을 편집 하 고 `CreateImage.json` 이 매개 변수 파일을 사용 하는 템플릿을 배포 합니다.


### <a name="edit-parameters-file"></a>매개 변수 파일 편집

이 파일은 `CreateImage.parameters.json` 다음 매개 변수를 사용 합니다. 

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

파일을 편집 `CreateImage.parameters.json` 하 여 Azure Stack Edge Pro 장치에 대해 다음 값을 포함 합니다.

1. 업로드할 VHD에 해당 하는 OS 유형을 제공 합니다. OS 유형은 Windows 또는 Linux 일 수 있습니다.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. 이미지 URI를 이전 단계에서 업로드 한 이미지의 URI로 변경 합니다.

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   *Http* 를 Storage 탐색기와 함께 사용 하는 경우 uri를 *http* uri로 변경 합니다.

3. 고유한 이미지 이름을 제공 합니다. 이 이미지는 이후 단계에서 VM을 만드는 데 사용 됩니다. 

   이 문서에서 사용 되는 샘플 json은 다음과 같습니다.

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

템플릿을 배포 `CreateImage.json` 합니다. 이 템플릿은 이후 단계에서 Vm을 만드는 데 사용 되는 이미지 리소스를 배포 합니다.

> [!NOTE]
> 인증 오류가 발생 하는 경우 템플릿을 배포할 때이 세션의 Azure 자격 증명이 만료 되었을 수 있습니다. 명령을 다시 실행 `login-AzureRM` 하 여 Azure Stack Edge Pro 장치에서 Azure Resource Manager에 다시 연결 합니다.

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
    이 명령은 이미지 리소스를 배포 합니다. 리소스를 쿼리하려면 다음 명령을 실행 합니다.

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    성공적으로 생성 된 이미지의 샘플 출력은 다음과 같습니다.
    
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

### <a name="edit-parameters-file-to-create-vm"></a>VM을 만들기 위해 매개 변수 파일 편집
 
VM을 만들려면 `CreateVM.parameters.json` 매개 변수 파일을 사용합니다. 다음 매개 변수를 사용 합니다.
    
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

에서 Azure Stack Edge Pro 장치에 적절 한 매개 변수를 할당 `CreateVM.parameters.json` 합니다.

1. 고유한 이름, 네트워크 인터페이스 이름 및 ipconfig 이름을 제공 합니다. 
1. 사용자 이름, 암호 및 지원 되는 VM 크기를 입력 합니다.
1. Compute에 대해 네트워크 인터페이스를 사용 하도록 설정 하면 가상 스위치와 가상 네트워크가 해당 네트워크 인터페이스에 자동으로 만들어집니다. 기존 가상 네트워크를 쿼리하여 Vnet 이름, 서브넷 이름 및 Vnet 리소스 그룹 이름을 가져올 수 있습니다.

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

    Vnet 이름에는 ASEVNET, 서브넷 이름에는 ASEVNETsubNet, Vnet 리소스 그룹 이름에는 ASERG를 사용 합니다.
    
1. 이제 위에 정의 된 서브넷 네트워크에 있는 VM에 할당할 고정 IP 주소가 필요 합니다. **PrivateIPAddress** 를 매개 변수 파일의이 주소로 바꿉니다. VM이 로컬 DCHP 서버에서 IP 주소를 가져오도록 하려면 `privateIPAddress` 값을 비워 둡니다.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. 매개 변수 파일을 저장합니다.

    이 문서에서 사용 되는 샘플 json은 다음과 같습니다.
    
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

### <a name="deploy-template-to-create-vm"></a>템플릿을 배포 하 여 VM 만들기

VM 만들기 템플릿을 배포 `CreateVM.json` 합니다. 이 템플릿은 기존 VNet에서 네트워크 인터페이스를 만들고 배포 된 이미지에서 VM을 만듭니다.

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

    VM 생성에는 15-20 분이 소요 됩니다. 성공적으로 만든 VM의 샘플 출력은 다음과 같습니다.
    
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

    `New-AzureRmResourceGroupDeployment`매개 변수를 사용 하 여 비동기적으로 명령을 실행할 수도 있습니다 `–AsJob` . 다음은 cmdlet이 백그라운드에서 실행 될 때의 샘플 출력입니다. 그런 다음 cmdlet을 사용 하 여 만든 작업의 상태를 쿼리할 수 있습니다 `Get-Job` .

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

7. VM이 성공적으로 프로 비전 되었는지 확인 합니다. 다음 명령 실행:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>VM에 연결

Windows 또는 Linux VM을 만들었는지 여부에 따라 연결 단계가 다를 수 있습니다.

### <a name="connect-to-windows-vm"></a>Windows VM에 연결

Windows VM에 연결 하려면 다음 단계를 수행 합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Linux VM에 연결

Linux VM에 연결 하려면 다음 단계를 수행 합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)