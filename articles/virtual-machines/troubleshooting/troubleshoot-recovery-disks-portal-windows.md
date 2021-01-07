---
title: Azure Portal에서 Windows VM 문제 해결 | Microsoft Docs
description: Azure Portal를 통해 OS 디스크를 복구 VM에 연결 하 여 Azure에서 Windows 가상 머신 문제를 해결 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735231"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Azure Portal를 통해 OS 디스크를 복구 VM에 연결 하 여 Windows VM 문제 해결
Azure의 Windows VM (가상 머신)에서 시작 또는 디스크 오류가 발생 하는 경우 VHD (가상 하드 디스크)에서 문제 해결 단계를 수행 해야 할 수 있습니다. 일반적인 예로 VM이 성공적으로 시작 되지 않도록 하는 실패 한 응용 프로그램 업데이트가 있습니다. 이 문서에서는 Azure Portal를 사용 하 여 가상 하드 디스크를 다른 Windows VM에 연결 하 여 오류를 해결 한 다음 원래 VM을 다시 만드는 방법을 자세히 설명 합니다. 

## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 영향을 받는 VM을 중지합니다.
1. VM의 OS 디스크에서 스냅샷 만들기
1. 스냅숏에서 가상 하드 디스크를 만듭니다.
1. 문제 해결을 위해 가상 하드 디스크를 다른 Windows VM에 연결하고 탑재합니다.
1. 문제 해결 VM에 연결합니다. 원본 가상 하드 디스크의 문제를 해결 하려면 파일을 편집 하거나 도구를 실행 합니다.
1. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
1. VM에 대 한 OS 디스크를 교환 합니다.

> [!NOTE]
> 이 문서는 관리 되지 않는 디스크가 있는 Vm에는 적용 되지 않습니다.

## <a name="take-a-snapshot-of-the-os-disk"></a>OS 디스크의 스냅샷 만들기
스냅숏은 가상 하드 디스크의 완전 한 읽기 전용 복사본입니다. 스냅숏을 만들기 전에 VM을 완전히 종료 하 여 진행 중인 모든 프로세스를 지워야 하는 것이 좋습니다. OS 디스크의 스냅숏을 만들려면 다음 단계를 수행 합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. 사이드바에서 **Virtual machines** 를 선택한 다음 문제가 있는 VM을 선택 합니다.
1. 왼쪽 창에서 **디스크** 를 선택 하 고 OS 디스크의 이름을 선택 합니다.
    ![디스크 설정에서 OS 디스크의 이름을 표시 하는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. OS 디스크의 **개요** 페이지에서 **스냅숏 만들기** 를 선택 합니다.
1. OS 디스크와 동일한 위치에 스냅숏을 만듭니다.

## <a name="create-a-disk-from-the-snapshot"></a>스냅샷에서 디스크 만들기
스냅숏에서 디스크를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **Cloud Shell** 를 선택 합니다.

    ![Azure Cloud Shell을 여는 단추를 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 다음 PowerShell 명령을 실행 하 여 스냅숏에서 관리 디스크를 만듭니다. 예제 이름을 적절 한 이름으로 바꿉니다.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. 명령이 성공적으로 실행 되 면 사용자가 제공한 리소스 그룹에 새 디스크가 표시 됩니다.

## <a name="attach-the-disk-to-another-vm"></a>다른 VM에 디스크 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 문제 해결 VM에 디스크를 연결 하면 디스크의 콘텐츠를 찾아보고 편집할 수 있습니다. 이 프로세스를 통해 모든 구성 오류를 수정 하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다. 다른 VM에 디스크를 연결 하려면 다음 단계를 수행 합니다.

1. 포털에서 리소스 그룹을 선택한 다음 문제 해결 VM을 선택 합니다. **디스크**  >  **편집**  >  **데이터 디스크 추가** 를 선택 합니다.

    ![포털에서 기존 디스크를 연결 하기 위한 선택 항목을 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. **데이터 디스크** 목록에서 식별 한 VM의 OS 디스크를 선택 합니다. OS 디스크가 표시 되지 않는 경우 문제 해결 VM 및 OS 디스크가 동일한 지역 (위치)에 있는지 확인 합니다. 
3. **저장** 을 선택 하 여 변경 내용을 적용 합니다.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>VM에 연결 된 데이터 디스크 탑재

1. 문제 해결 VM에 대 한 원격 데스크톱 연결을 엽니다. 
2. 문제 해결 VM에서 **서버 관리자** 을 열고 **파일 및 저장소 서비스** 를 선택 합니다. 

    ![서버 관리자 내에서 파일 및 저장소 서비스를 선택 하는 것을 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. 데이터 디스크가 자동으로 감지되고 연결됩니다. 연결된 디스크 목록을 보려면 **디스크** 를 선택합니다. 데이터 디스크를 선택하여 드라이브 문자를 포함한 볼륨 정보를 볼 수 있습니다. 다음 예에서는 드라이브 **F** 를 사용 하 여 연결 된 데이터 디스크를 보여 줍니다.

    ![서버 관리자에 연결 된 디스크 및 볼륨 정보를 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>원본 가상 하드 디스크의 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 모든 오류를 해결 한 후 다음 단계를 계속 합니다.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 탑재 해제 및 분리
문제 해결 VM에서 기존 가상 하드 디스크를 분리 합니다. 가상 하드 디스크를 문제 해결 VM에 연결 하는 임대가 해제 될 때까지 다른 VM에서 가상 하드 디스크를 사용할 수 없습니다.

1. VM에 대 한 원격 데스크톱 세션에서 **서버 관리자** 를 연 다음 **파일 및 저장소 서비스** 를 선택 합니다.

    ![서버 관리자에서 파일 및 저장소 서비스를 선택 하는 것을 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. **디스크** 를 선택 하 고 데이터 디스크를 마우스 오른쪽 단추로 클릭 한 다음 **오프 라인 상태로 만들기** 를 선택 합니다.

    ![서버 관리자에서 데이터 디스크를 오프 라인으로 설정 하는 것을 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. VM에서 가상 하드 디스크를 분리 합니다. Azure Portal에서 VM을 선택 하 고 **디스크** 를 선택 합니다. 
4. **편집** 을 선택 하 고 연결 된 OS 디스크를 선택한 다음 **삭제** 를 선택 합니다.

    ![기존 가상 하드 디스크를 분리 하기 위한 선택 항목을 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    계속 하기 전에 VM에서 데이터 디스크가 성공적으로 삭제 될 때까지 기다립니다.

## <a name="swap-the-os-disk-for-the-vm"></a>VM에 대 한 OS 디스크 교체

Azure Portal은 이제 VM의 OS 디스크 변경을 지원 합니다. 이를 수행하려면 다음 단계를 따르십시오.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. 사이드바에서 **Virtual machines** 를 선택한 다음 문제가 있는 VM을 선택 합니다.
1. 왼쪽 창에서 **디스크** 를 선택 하 고 **OS 디스크 교체** 를 선택 합니다.
   
    ![Azure Portal에서 OS 디스크를 교환 하는 단추를 보여 주는 스크린샷](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 복구한 새 디스크를 선택한 다음 VM의 이름을 입력 하 여 변경 내용을 확인 합니다. 목록에 디스크가 표시 되지 않으면 문제 해결 VM에서 디스크를 분리 한 후 10 ~ 15 분 동안 기다립니다. 또한 디스크가 VM과 동일한 위치에 있어야 합니다.
1. **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계
VM에 연결 하는 데 문제가 있는 경우 [AZURE vm에 대 한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md)을 참조 하세요. VM에서 실행 중인 응용 프로그램에 액세스 하는 데 문제가 있는 경우 [WINDOWS vm에서 응용 프로그램 연결 문제 해결](troubleshoot-app-connection.md)을 참조 하세요.

Azure Resource Manager 사용에 대 한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조 하세요.


