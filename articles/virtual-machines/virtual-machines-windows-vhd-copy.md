---
title: "Azure에서 전문화된 VM 사본 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 Azure에서 실행 중인 전문화된 Windows VM의 사본을 만드는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 9d3b8d0e9c13443cf29adb8c94cb817063110ae3
ms.lasthandoff: 03/23/2017


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Azure에서 실행 중인 전문화된 Windows VM의 복사본 만들기
이 문서에서는 AZCopy 도구를 사용하여 Azure에서 실행 중인 전문화된 Windows VM에서 VHD의 사본을 만드는 방법을 보여 줍니다. 그런 다음 새 VM을 만드는 데 VHD의 복사본을 사용할 수 있습니다. 

* 일반화된 VM을 복사하려면 [기존 일반화된 Azure VM에서 VM 이미지를 만드는 방법](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
* Hyper-V를 사용하여 만든 것처럼 온-프레미스 VM에서 VHD를 업로드하려는 경우 [온-프레미스 VM에서 Azure로 Windows VHD 업로드](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
다음 사항을 확인합니다.

* **원본 및 대상 저장소 계정**에 대한 정보가 있습니다. 원본 VM의 경우 저장소 계정 및 컨테이너 이름이 필요합니다. 일반적으로 컨테이너 이름은 **vhds**가 됩니다. 또한 대상 저장소 계정도 있어야 합니다. 아직 없는 경우 포털(**더 많은 서비스** > 저장소 계정 > 추가) 또는 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet을 사용하여 만들 수 있습니다. 
* Azure [PowerShell 1.0](/powershell/azureps-cmdlets-docs)(또는 이상)이 설치되어 있습니다.
* [AzCopy 도구](../storage/storage-use-azcopy.md)를 다운로드 및 설치했습니다. 

## <a name="deallocate-the-vm"></a>VM 할당을 취소합니다.
VM 할당을 취소하여 복사할 VHD를 해제합니다. 

* **포털**: **가상 컴퓨터** > **myVM** > 중지를 클릭합니다.
* **Powershell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM`은 **myResourceGroup** 리소스 그룹에서 **myVM**으로 명명된 VM의 할당을 취소합니다.

Azure 포털의 VM에 대한 **상태**가 **중지됨**에서 **중지됨(할당 취소됨)**으로 변경됩니다.

## <a name="get-the-storage-account-urls"></a>저장소 계정 URL 가져오기
원본 및 대상 저장소 계정의 URL이 필요합니다. URL은 `https://<storageaccount>.blob.core.windows.net/<containerName>/` 형태입니다. 저장소 계정 및 컨테이너 이름을 이미 알고 있는 경우 괄호 사이의 정보를 바꿔서 URL을 만듭니다. 

Azure 포털 또는 Azure PowerShell을 사용하여 URL을 가져옵니다.

* **포털**: **더 많은 서비스** > **저장소 계정** > <storage account> **Blobs**를 클릭하면 원본 VHD 파일이 **vhds** 컨테이너에 있을 것입니다. 컨테이너의 **속성**을 클릭하고 레이블이 **URL**인 텍스트를 복사합니다. 원본 및 대상 컨테이너의 URL이 모두 필요합니다. 
* **Powershell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"`은 **myResourceGroup** 리소스 그룹에서 **myVM**으로 명명된 VM에 대한 정보를 가져옵니다. 결과에서 **Vhd Uri**에 대한 **저장소 프로필** 섹션을 살펴봅니다. Uri의 첫 번째 부분은 컨테이너에 대한 URL이고 마지막 부분은 VM에 대한 OS VHD 이름입니다.

## <a name="get-the-storage-access-keys"></a>저장소 액세스 키 가져오기
원본 및 대상 저장소 계정에 대한 액세스 키를 찾습니다. 액세스 키에 대한 자세한 내용은 [Azure 저장소 계정 방법](../storage/storage-create-storage-account.md)을 참조하세요.

* **포털**: **더 많은 서비스** > **저장소 계정** > <storage account> **모든 설정** > **액세스 키**를 클릭합니다. **key1**로 레이블이 지정된 키를 복사합니다.
* **Powershell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup`은 **myResourceGroup** 리소스 그룹에서 **mystorageaccount** 저장소 계정에 대한 저장소 키를 가져옵니다. **key1**로 레이블이 지정된 키를 복사합니다.

## <a name="copy-the-vhd"></a>VHD 복사
AzCopy를 사용하여 저장소 계정 간에 파일을 복사할 수 있습니다. 대상 컨테이너에 대해 지정된 컨테이너가 존재하지 않는 경우 컨테이너가 만들어집니다. 

AzCopy를 사용하려면 로컬 컴퓨터에서 명령 프롬프트를 열고 AzCopy가 설치된 폴더로 이동합니다. 이 폴더는 *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*와 유사합니다. 

컨테이너 내의 모든 파일을 복사하려면 **/S** 스위치를 사용합니다. 동일한 컨테이너에 있는 경우 OS VHD 및 모든 데이터 디스크를 복사하는 데 사용할 수 있습니다. 이 예에서는 **mysourcestorageaccount** 저장소 계정의 **mysourcecontainer** 컨테이너에 있는 모든 파일을 **mydestinationstorageaccount** 저장소 계정의 **mydestinationcontainer** 컨테이너로 복사하는 방법을 보여 줍니다. 저장소 계정 및 컨테이너의 이름을 사용자 고유 값으로 바꿉니다. `<sourceStorageAccountKey1>` 및 `<destinationStorageAccountKey1>`을 사용자 고유 키로 바꿉니다.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

여러 파일과 함께 컨테이너에서 특정 VHD를 복사하려면 /Pattern 스위치를 사용하여 파일 이름을 지정할 수도 있습니다. 이 예에서는 **myFileName.vhd**라는 파일만 복사됩니다.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


완료되면 다음과 유사한 메시지를 받게 됩니다.

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>문제 해결
* AZCopy를 사용하는 경우 “서버가 요청을 인증하지 못했습니다.”라는 오류 메시지가 표시되면 권한 부여 헤더의 값이 서명을 포함하여 올바른 형식으로 지정되었는지 확인합니다. 키 2 또는 보조 저장소 키를 사용 중인 경우 주 또는 첫 번째 저장소 키를 사용해 보세요.

## <a name="next-steps"></a>다음 단계
* [VHD의 복사본을 OS 디스크로 VM에 연결](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하여 새 VM을 만들 수 있습니다.


