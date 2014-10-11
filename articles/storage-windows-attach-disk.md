<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="How to Attach a Data Disk to a Virtual Machine" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법

빈 디스크와 데이터가 포함된 디스크를 모두 연결할 수 있습니다. 두 경우 모두, 디스크는 실제로 Azure 저장소 계정에 상주하는 .vhd 파일입니다. 또한 두 경우 모두 디스크를 연결한 후 초기화를 해야 사용 준비가 완료됩니다.

> [WACOM.NOTE] 모범 사례는 별도 디스크를 하나 이상 사용하여 가상 컴퓨터의 데이터를 저장하는 것입니다. Azure 가상 컴퓨터를 만들면 C 드라이브에 매핑되는 운영 체제용 디스크와 D 드라이브에 매핑되는 임시 디스크가 있습니다. **D 드라이브는 데이터 저장에 사용하지 마세요.** 이름이 의미하는 것과 같이 D 드라이브는 임시 저장소만 제공합니다. Azure 저장소에 상주하지 않으므로 중복성이나 백업을 제공하지 않습니다.

-   [방법: 빈 디스크 연결][]
-   [방법: 기존 디스크 연결][]
-   [방법: Windows Server에서 새 데이터 디스크 초기화][]

[WACOM.INCLUDE [howto-attach-disk-windows-linux][]]

## <span id="initializeinWS"></span></a>방법: Windows Server에서 새 데이터 디스크 초기화

1.  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][]에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.

2.  로그온한 후 **Server Manager**를 열고 왼쪽 창에서 **Storage**를 확장한 후 **Disk Management**를 클릭합니다.

    ![서버 관리자 열기][]

3.  **Disk 2**를 마우스 오른쪽 단추로 클릭한 후 **Initialize Disk**를 클릭하고 **OK**를 클릭합니다.

    ![디스크 초기화][]

4.  디스크 2에 대한 공간 할당 영역을 마우스 오른쪽 단추로 클릭하고 **New Simple Volume**을 클릭한 다음 기본값을 사용하여 마법사를 완료합니다.

    ![볼륨 초기화][]



    The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [방법: 빈 디스크 연결]: #attachempty
  [방법: 기존 디스크 연결]: #attachexisting
  [방법: Windows Server에서 새 데이터 디스크 초기화]: #initializeinWS
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: ../virtual-machines-log-on-windows-server/
  [서버 관리자 열기]: ./media/storage-windows-attach-disk/ServerManager.png
  [디스크 초기화]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [볼륨 초기화]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
