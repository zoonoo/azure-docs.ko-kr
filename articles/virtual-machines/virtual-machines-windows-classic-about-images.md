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
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 28720bcedb4df66c2f7434f25ed251d4e116c415


---
# <a name="about-images-for-windows-virtual-machines"></a>Windows 가상 컴퓨터에 대한 이미지 정보
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager 모델의 이미지를 찾고 사용하는 방법에 대한 정보는 [여기](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>이미지 작업
Azure PowerShell 모듈을 사용하여 Azure 구독에 사용할 수 있는 이미지를 관리할 수 있습니다. 또한 일부 이미지 작업에 Azure 클래식 포털을 사용할 수 있지만 명령줄에는 추가 옵션이 있습니다.

* **모든 이미지 가져오기**:`Get-AzureVMImage`현재 구독에서 사용할 수 있는 모든 이미지 목록 반환: 사용자의 이미지뿐만 아니라 Azure 또는 파트너에서 제공하는 이미지입니다. 이 작업을 수행하면 목록이 커질 수 있습니다. 다음 예에서는 짧은 목록을 가져오는 방법을 보여 줍니다.
* **이미지 제품군 가져오기**:`Get-AzureVMImage | select ImageFamily` **ImageFamily** 속성 문자열을 표시하여 이미지 제품군 목록을 가져옵니다.
* **특정 제품군의 모든 이미지 가져오기**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **VM 이미지 찾기**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` VM 이미지에만 적용되는 DataDiskConfiguration 속성을 필터링하여 작동합니다. 또한 이 예에서는 출력을 레이블 및 이미지 이름으로만 필터링합니다.
* **일반화된 이미지 저장**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **특수화된 이미지 저장**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] 데이터 디스크 및 운영 체제 디스크를 포함 하는 VM 이미지를 만들려면 OSState 매개 변수가 필요합니다. 매개 변수를 사용하지 않으면 cmdlet에서 OS 이미지를 만듭니다. 매개 변수의 값은 운영 체제 디스크가 다시 사용하도록 준비되어 있는지 여부에 따라 이미지가 일반화되었는지 특수화되었는지를 나타냅니다.
* **이미지 삭제**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>다음 단계
또한 [클래식 포털을 사용하여 Windows 컴퓨터를 만들](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 수도 있습니다.




<!--HONumber=Nov16_HO3-->


