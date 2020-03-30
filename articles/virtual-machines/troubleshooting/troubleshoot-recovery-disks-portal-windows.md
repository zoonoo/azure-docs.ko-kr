---
title: Azure Portal에서 Windows 문제 해결 VM 사용 | Microsoft Docs
description: Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Azure에서 Windows 가상 머신 문제를 해결하는 방법을 알아봅니다.
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
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249998"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결
Azure에서 Windows VM(가상 머신)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 애플리케이션 업데이트가 실패하여 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에는 가상 하드 디스크를 다른 Windows VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure Portal을 사용하는 방법을 자세히 설명합니다. 

## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 영향을 받는 VM을 중지합니다.
1. VM의 OS 디스크에 대한 스냅숏을 만듭니다.
1. 스냅샷에서 가상 하드 디스크를 만듭니다.
1. 문제 해결을 위해 가상 하드 디스크를 다른 Windows VM에 연결하고 탑재합니다.
1. 문제 해결 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 원래의 가상 하드 디스크에서 문제를 수정합니다.
1. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
1. OS 디스크를 VM으로 교체합니다.

> [!NOTE]
> 이 문서는 관리되지 않는 디스크가 있는 VM에는 적용되지 않습니다.

## <a name="take-a-snapshot-of-the-os-disk"></a>OS 디스크의 스냅샷 생성
스냅샷은 VHD(가상 하드 드라이브)의 전체 읽기 전용 복사본입니다. 스냅숏을 찍기 전에 VM을 깨끗하게 종료하여 진행 중인 프로세스를 지우는 것이 좋습니다. OS 디스크의 스냅숏을 찍려면 다음 단계를 따르십시오.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. 사이드바에서 **가상 컴퓨터를** 선택한 다음 문제가 있는 VM을 선택합니다.
1. 왼쪽 창에서 디스크를 선택한 다음 OS 디스크의 이름을 **선택합니다.**
    ![OS 디스크 의 이름에 대한 이미지](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. OS 디스크의 **개요** 페이지에서 **스냅숏 만들기를**선택합니다.
1. OS 디스크와 동일한 위치에서 스냅샷을 만듭니다.

## <a name="create-a-disk-from-the-snapshot"></a>스냅샷에서 디스크 만들기
스냅샷에서 디스크를 만들려면 다음 단계를 따르십시오.

1. Azure 포털에서 **클라우드 셸을** 선택합니다.

    ![오픈 클라우드 쉘에 대한 이미지](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 다음 PowerShell 명령을 실행하여 스냅샷에서 관리되는 디스크를 만듭니다. 이러한 샘플 이름을 적절한 이름으로 바꿔야 합니다.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. 명령이 성공적으로 실행되면 제공한 리소스 그룹에 새 디스크가 표시됩니다.

## <a name="attach-the-disk-to-another-vm"></a>디스크를 다른 VM에 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 문제 해결 VM에 디스크를 연결한 후 디스크의 콘텐츠를 찾아보고 편집할 수 있습니다. 이 프로세스를 사용하면 구성 오류를 수정하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다. 디스크를 다른 VM에 연결하려면 다음 단계를 따르십시오.

1. 포털에서 리소스 그룹을 선택하고 문제를 해결하는 VM을 선택합니다. **디스크를**선택하고 **편집을**선택한 다음 **데이터 디스크 추가를**클릭합니다.

    ![포털에서 기존 디스크 연결](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 데이터 **디스크** 목록에서 식별한 VM의 OS 디스크를 선택합니다. OS 디스크가 표시되지 않으면 문제 해결 VM과 OS 디스크가 동일한 영역(위치)에 있는지 확인합니다. 
3. 변경 내용을 적용하려면 **저장을** 선택합니다.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>연결된 데이터 디스크를 VM에 마운트합니다.

1. 문제 해결 VM에 대한 원격 데스크톱 연결을 엽니다. 
2. 문제 해결에서 VM, 열린 **서버 관리자**, 다음 파일 및 **저장소 서비스를**선택합니다. 

    ![서버 관리자에서 파일 및 Storage 서비스 선택](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. 데이터 디스크가 자동으로 감지되고 연결됩니다. 연결된 디스크 목록을 보려면 **디스크**를 선택합니다. 데이터 디스크를 선택하여 드라이브 문자를 포함한 볼륨 정보를 볼 수 있습니다. 다음 예에서는 **F:** 를 사용하여 연결된 데이터 디스크를 보여 줍니다.

    ![서버 관리자의 연결된 디스크 및 볼륨 정보](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 탑재 해제 및 분리
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 사용할 수 없습니다.

1. VM에 대한 RDP 세션에서 **서버 관리자**를 연 다음 **파일 및 Storage 서비스**를 선택합니다.

    ![서버 관리자에서 파일 및 Storage 서비스 선택](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. **디스크**를 선택한 다음 데이터 디스크를 선택합니다. 데이터 디스크를 마우스 오른쪽 단추로 클릭하고 **오프라인 상태로 전환**을 선택합니다.

    ![서버 관리자에서 데이터 디스크를 오프라인으로 설정](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 이제 VM에서 가상 하드 디스크를 분리합니다. Azure Portal에서 VM을 선택하고 **디스크**를 클릭합니다. 
4. **편집을**선택하고 연결한 OS 디스크를 선택한 다음 **분리를**클릭합니다.

    ![기존 가상 하드 디스크 분리](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    계속하기 전에 VM이 데이터 디스크를 성공적으로 분리할 때까지 기다립니다.

## <a name="swap-the-os-disk-for-the-vm"></a>Os 디스크를 VM용으로 교체

이제 Azure 포털은 VM의 OS 디스크 변경을 지원합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. 사이드바에서 **가상 컴퓨터를** 선택한 다음 문제가 있는 VM을 선택합니다.
1. 왼쪽 창에서 디스크 를 선택한 다음 **OS 디스크 교환을** **선택합니다.**
        ![Azure 포털의 OS 디스크 교환에 대한 이미지](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 복구한 새 디스크를 선택한 다음 VM 이름을 입력하여 변경 을 확인합니다. 목록에 디스크가 표시되지 않으면 문제 해결 VM에서 디스크를 분리한 후 10~15분 후에 기다립니다. 또한 디스크가 VM과 동일한 위치에 있는지 확인합니다.
1. 확인을 선택합니다.

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 RDP 연결 문제 해결](troubleshoot-rdp-connection.md)을 참조하세요. VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Windows VM에서 애플리케이션 연결 문제 해결](troubleshoot-app-connection.md)을 참조하세요.

Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.


