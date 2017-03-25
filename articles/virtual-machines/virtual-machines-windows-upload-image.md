---
title: "리소스 관리자에 대한 Windows VHD 업로드 | Microsoft Docs"
description: "리소스 관리자 배포 모델을 사용하여 온-프레미스에서 Azure로 Windows 가상 컴퓨터 VHD 업로드에 대해 알아봅니다. 일반화된 또는 특수한 VM에서 VHD를 업로드할 수 있습니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 28a6858729adbef439625864abc2fd7aa77fab35
ms.lasthandoff: 03/11/2017


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>온-프레미스 VM에서 Azure로 Windows VHD 업로드
이 문서에서는 Azure VM 만들기에 사용될 Windows VHD(가상 하드 디스크)를 만들고 업로드하는 방법을 보여 줍니다. 일반화된 VM 또는 특수한 VM에서 VHD를 업로드할 수 있습니다. 

Managed Disks를 사용하여 새 VM을 준비, 업로드 및 만드는 방법에 대한 전체 연습을 보려면 [Managed Disks를 사용하여 Azure에 업로드된 일반화된 VHD에서 새 VM 만들기](virtual-machines-windows-upload-generalized-managed.md) 또는 [Azure에서 특수화된 VHD를 업로드하여 VM 만들기](virtual-machines-windows-upload-specialized.md)를 참조하세요.

Azure의 디스크 및 VHD에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="prepare-the-vm"></a>VM 준비
일반화된 VHD 및 특수한 VHD 모두를 Azure에 업로드할 수 있습니다. 각 유형은 시작하기 전에 VM을 준비해야 합니다.

* **일반화된 VHD** - 일반화된 VHD에는 Sysprep을 사용하여 제거된 모든 개인 계정 정보가 포함되어 있습니다. 새 VM을 만드는 이미지로 VHD를 사용하려는 경우 다음을 수행해야 합니다.
  
  * [Azure에 업로드할 Windows VHD를 준비합니다](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [Sysprep을 사용하여 가상 컴퓨터를 일반화합니다](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **특수한 VHD** - 특수한 VHD는 사용자 계정, 응용 프로그램 및 원본 VM의 다른 상태 데이터를 유지 관리합니다. 새 VM을 만드는데 VHD를 그대로 사용하려는 경우 다음 단계가 완료되었는지 확인합니다. 
  
  * [Azure에 업로드할 Windows VHD를 준비합니다](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sysprep을 사용하여 VM을 일반화하지 **마십시오**.
  * 모든 게스트 가상화 도구 및 VM에 설치된 에이전트를 제거합니다(예: VMware 도구).
  * VM이 DHCP를 통해 해당 IP 주소 및 DNS 설정을 가져오도록 구성되었는지 확인합니다. 이렇게 하면 서버를 시작할 때 VNet 내의 IP 주소를 가져옵니다. 

## <a name="log-in-to-azure"></a>Azure에 로그인
PowerShell 버전 1.4 이상을 아직 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

1. Azure PowerShell을 열고 Azure 계정에 로그인합니다. Azure 계정 자격 증명을 입력하기 위한 팝업 창이 열립니다.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 사용 가능한 구독에 대한 구독을 ID를 가져옵니다.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 구독 ID를 사용하여 올바른 구독을 설정합니다. `<subscriptionID>`를 올바른 구독의 ID로 바꿉니다.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>저장소 계정 가져오기
업로드한 VM 이미지를 저장할 Azure 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다. 

사용 가능한 저장소 계정을 표시하려면 다음을 입력합니다.

```powershell
Get-AzureRmStorageAccount
```

기존 저장소 계정을 사용하려면 [VM 이미지 업로드](#upload-the-vm-vhd-to-your-storage-account) 섹션을 진행합니다.

저장소 계정을 만들어야 하는 경우 다음 단계를 따릅니다.

1. 저장소 계정을 만들어야 하는 리소스 그룹의 이름을 알아야 합니다. 구독의 모든 리소스 그룹을 찾으려면 다음을 입력합니다.
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    **미국 서부** 지역에 **myResourceGroup** 이라는 이름의 리소스 그룹을 만들려면 다음을 입력합니다.

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet을 사용하여 이 리소스 그룹에 **mystorageaccount**라는 이름의 저장소 계정을 만듭니다.
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName에 대한 유효한 값은 다음과 같습니다.
   
   * **Standard_LRS** - 로컬 중복 저장소 
   * **Standard_ZRS** - 영역 중복 저장소
   * **Standard_GRS** - 지역 중복 저장소 
   * **Standard_RAGRS** - 읽기 액세스 지역 중복 저장소 
   * **Premium_LRS** - 프리미엄 로컬 중복 저장소 

## <a name="upload-the-vhd-to-your-storage-account"></a>저장소 계정에 VHD 업로드
[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet을 사용하여 저장소 계정의 컨테이너에 이미지를 업로드합니다. 이 예제에서는 `"C:\Users\Public\Documents\Virtual hard disks\"`에서 **myResourceGroup** 리소스 그룹의 **mystorageaccount**라는 저장소 계정에 파일 **myVHD.vhd**를 업로드합니다. 파일은 **mycontainer**라는 컨테이너에 배치되고 새 파일 이름은 **myUploadedVHD.vhd**가 됩니다.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


성공하면 다음과 유사한 응답을 얻게 됩니다.

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

이 명령은 네트워크 연결 및 VHD 파일의 크기에 따라 완료하는 데 다소 시간이 걸립니다.

## <a name="next-steps"></a>다음 단계
* [일반화된 VHD에서 Azure에 VM 만들기](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* 새 VM을 만들 때 OS 디스크로 연결하여 [특수한 VHD에서 Azure에 VM을 만듭니다](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


