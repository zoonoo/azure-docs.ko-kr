---
title: "Windows 가상 컴퓨터에 대한 이미지 정보 | Microsoft Docs"
description: "Azure에서 Windows 가상 컴퓨터와 함께 이미지를 사용하는 방법에 대해 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.lasthandoff: 03/27/2017


---
# <a name="about-images-for-windows-virtual-machines"></a>Windows 가상 컴퓨터에 대한 이미지 정보
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델의 이미지를 찾고 사용하는 방법에 대한 정보는 [여기](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>이미지 작업

Azure PowerShell 모듈 및 Azure Portal을 사용하여 Azure 구독에 사용할 수 있는 이미지를 관리할 수 있습니다. Azure PowerShell 모듈은 확인하거나 보려는 내용을 정확하게 겨냥할 수 있도록 더 많은 명령 옵션을 제공합니다. Azure Portal은 일상적인 대부분의 관리 태스크에 대한 GUI를 제공합니다.

Azure PowerShell 모듈을 사용하는 몇 가지 예는 다음과 같습니다.

* **모든 이미지 가져오기**:`Get-AzureVMImage`현재 구독에서 사용할 수 있는 모든 이미지 목록 반환: 사용자의 이미지 및 Azure 또는 파트너에서 제공하는 이미지입니다. 결과 목록은 클 수 있습니다. 다음 예에서는 짧은 목록을 가져오는 방법을 보여 줍니다.
* **이미지 제품군 가져오기**:`Get-AzureVMImage | select ImageFamily` **ImageFamily** 속성 문자열을 표시하여 이미지 제품군 목록을 가져옵니다.
* **특정 제품군의 모든 이미지 가져오기**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **VM 이미지 찾기**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` 이 cmdlet은 VM 이미지에만 적용되는 DataDiskConfiguration 속성을 필터링하여 작동합니다. 또한 이 예에서는 출력을 레이블 및 이미지 이름으로만 필터링합니다.
* **일반화된 이미지 저장**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **특수화된 이미지 저장**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > 운영 체제 디스크 및 연결된 데이터 디스크를 포함하는 VM 이미지를 만들려면 OSState 매개 변수가 필요합니다. 매개 변수를 사용하지 않으면 cmdlet에서 OS 이미지를 만듭니다. 매개 변수의 값은 운영 체제 디스크가 다시 사용하도록 준비되어 있는지 여부에 따라 이미지가 일반화되었는지 특수화되었는지를 나타냅니다.

* **이미지 삭제**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>다음 단계
또한 [Azure Portal을 사용하여 Windows 컴퓨터를 만들](tutorial.md) 수도 있습니다.

