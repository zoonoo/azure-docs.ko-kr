---
title: Azure Portal에서 Linux 문제 해결 VM 사용 | Microsoft Docs
description: Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux 가상 머신 문제를 해결하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: 87b4d761eb7bd1c4a16998e44e8160cda24a05b4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088255"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결
Linux 가상 머신(VM)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목으로 인해 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에는 가상 하드 디스크를 다른 Linux VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure Portal을 사용하는 방법을 자세히 설명합니다.

## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 영향을 받는 VM을 중지합니다.
1. VM의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. 스냅숏에서 가상 하드 디스크를 만듭니다.
1. 문제 해결을 위해 가상 하드 디스크를 다른 Windows VM에 연결하고 탑재합니다.
1. 문제 해결 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 원래의 가상 하드 디스크에서 문제를 수정합니다.
1. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
1. VM에 대 한 OS 디스크를 교환 합니다.

> [!NOTE]
> 이 문서는 관리 되지 않는 디스크가 있는 VM에는 적용 되지 않습니다.

## <a name="determine-boot-issues"></a>부팅 문제 확인
VM이 올바르게 부팅할 수 없는 원인을 확인하려면 부팅 진단 및 VM 스크린샷을 검사합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목 또는 삭제하거나 이동 중인 기본 가상 하드 디스크입니다.

포털에서 VM을 선택하고 **지원+문제 해결** 섹션까지 아래로 스크롤합니다. **부팅 진단**을 클릭하여 VM에서 스트리밍된 콘솔 메시지를 볼 수 있습니다. 콘솔 로그를 확인하여 VM에서 문제가 발생하는 이유를 확인할 수 있습니다. 다음 예제에서는 수동 상호 작용을 필요로 하는 유지 관리 모드에 묶여 있는 VM을 보여 줍니다.

![VM 부팅 진단 콘솔 로그 보기](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

VM 스크린샷의 캡처를 다운로드하려면 부팅 진단 로그의 위쪽에 있는 **스크린 샷**을 클릭할 수도 있습니다.

## <a name="take-a-snapshot-of-the-os-disk"></a>OS 디스크의 스냅숏 만들기
스냅샷은 VHD(가상 하드 드라이브)의 전체 읽기 전용 복사본입니다. 스냅숏을 만들기 전에 VM을 완전히 종료 하 여 진행 중인 모든 프로세스를 지워야 하는 것이 좋습니다. OS 디스크의 스냅숏을 만들려면 다음 단계를 수행 합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. 사이드바에서 **Virtual machines** 를 선택한 다음 문제가 있는 VM을 선택 합니다.
1. 왼쪽 창에서 **디스크**를 선택 하 고 OS 디스크의 이름을 선택 합니다.
    ![OS 디스크의 이름에 대 한 이미지](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. OS 디스크의 **개요** 페이지에서 **스냅숏 만들기**를 선택 합니다.
1. OS 디스크와 동일한 위치에 스냅숏을 만듭니다.

## <a name="create-a-disk-from-the-snapshot"></a>스냅샷에서 디스크 만들기
스냅숏에서 디스크를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **Cloud Shell** 를 선택 합니다.

    ![Open Cloud Shell에 대 한 이미지](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 다음 PowerShell 명령을 실행 하 여 스냅숏에서 관리 디스크를 만듭니다. 이러한 샘플 이름을 적절 한 이름으로 바꾸어야 합니다.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
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
3. 명령이 성공적으로 실행 되 면 사용자가 제공한 리소스 그룹에 새 디스크가 표시 됩니다.

## <a name="attach-disk-to-another-vm"></a>다른 VM에 디스크 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 문제 해결 VM에 디스크를 연결 하면 디스크의 콘텐츠를 찾아보고 편집할 수 있습니다. 이 프로세스를 통해 모든 구성 오류를 수정 하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다. 다른 VM에 디스크를 연결 하려면 다음 단계를 수행 합니다.

1. 포털에서 리소스 그룹을 선택하고 문제를 해결하는 VM을 선택합니다. **디스크**를 선택 하 고 **편집**을 선택한 다음 **데이터 디스크 추가**를 클릭 합니다.

    ![포털에서 기존 디스크 연결](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. **데이터 디스크** 목록에서 식별 한 VM의 OS 디스크를 선택 합니다. OS 디스크가 표시 되지 않으면 VM 및 OS 디스크의 문제 해결이 동일한 지역 (위치)에 있는지 확인 합니다. 
3. **저장** 을 선택 하 여 변경 내용을 적용 합니다.

## <a name="mount-the-attached-data-disk"></a>연결된 데이터 디스크 탑재

> [!NOTE]
> 다음 예제에서는 Ubuntu VM에 필요한 단계를 자세히 설명합니다. Red Hat Enterprise Linux 또는 SUSE와 같은 다른 Linux 배포판을 사용하는 경우 로그 파일 위치와 `mount` 명령을 약간 다를 수 있습니다. 명령의 적절한 변경에 대한 특정 배포에 대한 설명서를 참조하세요.

1. 적절한 자격 증명을 사용하여 문제 해결 VM에 대한 SSH입니다. 이 디스크가 문제 해결 VM에 연결된 첫 번째 데이터 디스크인 경우 `/dev/sdc`에 연결할 수 있습니다. `dmseg`를 사용하여 연결된 디스크를 나열할 수 있습니다.

    ```bash
    dmesg | grep SCSI
    ```
    다음 예제와 유사하게 출력됩니다.

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    앞의 예제에서 OS 디스크는 `/dev/sda`에 있으며 각 VM에 제공된 임시 디스크는 `/dev/sdb`에 있습니다. 여러 데이터 디스크가 있는 경우 `/dev/sdd`, `/dev/sde` 등에 있어야 합니다.

2. 디렉터리를 만들어 기존 가상 하드 디스크를 탑재합니다. 다음 예제는 디렉터리 `troubleshootingdisk`를 만듭니다.

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 기존 가상 하드 디스크에 여러 개의 파티션이 있는 경우 필수 파티션을 탑재합니다. 다음 예제에서는 `/dev/sdc1`에 첫 번째 주 파티션을 탑재합니다.

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 가상 하드 디스크의 UUID(Universally Unique Identifier)를 사용하여 Azure의 VM에 데이터 디스크를 마운트하는 것이 가장 좋습니다. 이 짧은 문제 해결 시나리오에서는 UUID를 사용하여 가상 하드 디스크를 탑재할 필요는 없습니다. 그러나 일반적인 사용 환경에서 UUID 대신 디바이스 이름을 사용하여 가상 하드 디스크를 탑재하기 위해 `/etc/fstab`을 편집하면 VM이 부팅되지 않을 수 있습니다.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 탑재 해제 및 분리
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 사용할 수 없습니다.

1. SSH 세션에서 문제 해결 VM까지 기존 가상 하드 디스크를 탑재 해제합니다. 먼저 탑재 지점에 대한 상위 디렉터리에서 변경합니다.

    ```bash
    cd /
    ```

    이제 기존 가상 하드 디스크를 탑재 해제합니다. 다음 예제는 `/dev/sdc1`에 있는 디바이스를 탑재 해제합니다.

    ```bash
    sudo umount /dev/sdc1
    ```

2. 이제 VM에서 가상 하드 디스크를 분리합니다. 포털에서 VM을 선택하고 **디스크**를 클릭합니다. 기존 가상 하드 디스크를 선택한 다음 **분리**를 클릭합니다.

    ![기존 가상 하드 디스크 분리](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    계속하기 전에 VM이 데이터 디스크를 성공적으로 분리할 때까지 기다립니다.

## <a name="swap-the-os-disk-for-the-vm"></a>VM에 대 한 OS 디스크 교체

Azure Portal은 이제 VM의 OS 디스크 변경을 지원 합니다. 이렇게 하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. 사이드바에서 **Virtual machines** 를 선택한 다음 문제가 있는 VM을 선택 합니다.
1. 왼쪽 창에서 **디스크**를 선택 하 고 **OS 디스크 교체**를 선택 합니다.
        ![Azure Portal에서 OS 디스크 교체에 대 한 이미지](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 복구한 새 디스크를 선택한 다음 VM의 이름을 입력 하 여 변경 내용을 확인 합니다. 목록에 디스크가 표시 되지 않으면 문제 해결 VM에서 디스크를 분리 한 후 10 ~ 15 분 동안 기다립니다. 또한 디스크가 VM과 동일한 위치에 있어야 합니다.
1. 확인을 선택 합니다.

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Linux VM에서 애플리케이션 연결 문제 해결](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
