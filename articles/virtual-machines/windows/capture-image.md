---
title: "일반화된 Azure VM에서 VM 이미지 캡처 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 만든 일반화된 Azure VM에서 VM 이미지를 캡처하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>일반화된 Azure VM에서 VM 이미지를 캡처하는 방법
이 문서에서는 Azure PowerShell을 사용하여 일반화된 Azure VM 이미지를 만드는 방법을 보여줍니다. 그런 다음 이미지를 사용하여 다른 VM을 만들 수 있습니다. 이 이미지에는 OS 디스크를 비롯해 가상 컴퓨터에 연결된 데이터 디스크가 포함됩니다. 새 VM을 만들 때 해당 리소스를 설정해야 하므로 이미지는 가상 네트워크 리소스를 포함하지 않습니다. 

## <a name="prerequisites"></a>필수 조건
Azure PowerShell 버전 1.0.x 이상을 설치해야 합니다. PowerShell을 아직 설치하지 않은 경우 설치 단계에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Azure에 VHD를 업로드하기 전에 Sysprep을 처음으로 실행하는 경우 Sysprep을 실행하기 전에 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

1. Windows 가상 컴퓨터에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 컴퓨터를 종료합니다. VM을 다시 시작하지 마세요.


`sudo waagent -deprovision+user`을 사용하여 Linux VM을 일반화한 다음 PowerShell을 사용하여 VM을 캡처할 수 있습니다. VM을 캡처하는 데 CLI를 사용하는 방법에 대한 정보는 [Azure CLI를 사용하여 Linux 가상 컴퓨터 일반화하고 캡처하는 방법](../linux/capture-image.md)을 참조하세요.

## <a name="log-in-to-azure-powershell"></a>Azure PowerShell에 로그인합니다.
1. Azure PowerShell을 열고 Azure 계정에 로그인합니다.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Azure 계정 자격 증명을 입력하기 위한 팝업 창이 열립니다.
2. 사용 가능한 구독에 대한 구독을 ID를 가져옵니다.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 구독 ID를 사용하여 올바른 구독을 설정합니다.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>VM의 할당을 취소하고 상태를 일반화됨으로 설정합니다.
1. VM 리소스 할당을 취소합니다.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure Portal의 VM에 대한 *상태*가 **중지됨**에서 **중지됨(할당 취소됨)**으로 변경됩니다.
2. 가상 컴퓨터의 상태를 **일반화됨**으로 설정합니다. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. VM의 상태를 확인합니다. VM에 대한 **OSState/일반화됨** 섹션은 **DisplayStatus**를 **VM 일반화됨**으로 설정해야 합니다.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>이미지 만들기
1. 이 명령을 사용하여 가상 컴퓨터 이미지를 대상 저장소 컨테이너에 복사합니다. 이미지는 원래 가상 컴퓨터와 동일한 저장소 계정에 만들어집니다. `-Path` 매개 변수는 JSON 템플릿의 복사본을 로컬로 저장합니다. `-DestinationContainerName` 매개 변수는 이미지를 유지할 컨테이너의 이름입니다. 컨테이너가 없으면 컨테이너가 만들어집니다.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    JSON 파일 템플릿에서 이미지의 URL을 얻을 수 있습니다. 이미지의 전체 경로에 대한 **resources** > **storageProfile** > **osDisk** > **image** > **uri** 섹션으로 이동합니다. 이미지의 URL은 `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`과 같습니다.
   
    포털에서 URI를 확인할 수도 있습니다. 이미지는 저장소 계정에서 **시스템** 라는 컨테이너에 복사됩니다. 

## <a name="next-steps"></a>다음 단계
* 이제 [이미지에서 VM을 만들 수 있습니다](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


