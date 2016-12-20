---
title: "Windows VHD 일반화 | Microsoft Docs"
description: "Sysprep을 사용하여 Resource Manager 배포 모델에서 사용할 Windows VM을 일반화하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 9e9d973ef36cde4f0a1ada2ba7bf7d01a8d8f687


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Sysprep을 사용하여 Windows 가상 컴퓨터 일반화
이 섹션에서는 이미지로 사용하기 위해 Windows 가상 컴퓨터를 일반화하는 방법을 보여 줍니다. Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Azure에 VHD를 업로드하기 전에 Sysprep을 처음으로 실행하는 경우 Sysprep을 실행하기 전에 [VM을 준비](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

1. Windows 가상 컴퓨터에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
   
    ![Sysprep 시작](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 컴퓨터를 종료합니다. 

## <a name="next-steps"></a>다음 단계
* VM이 온-프레미스에 있는 경우 이제 [Azure에 VHD를 업로드](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수 있습니다.
* VM이 Azure에 이미 있는 경우 이제 [일반화된 VM에서 이미지를 만들](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 수 있습니다.




<!--HONumber=Nov16_HO3-->


