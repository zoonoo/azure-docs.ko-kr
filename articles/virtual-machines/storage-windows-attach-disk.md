<properties
	pageTitle="가상 컴퓨터에 디스크 연결 | Microsoft Azure"
	description="데이터 디스크를 Azure 가상 컴퓨터에 연결하고 사용 가능하도록 초기화하는 방법에 대해 알아봅니다."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="kathydav"/>

# Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법

빈 디스크와 데이터가 포함된 디스크를 모두 연결할 수 있습니다. 두 경우 모두, 디스크는 실제로 Azure 저장소 계정에 상주하는 .vhd 파일입니다. 또한 두 경우 모두 디스크를 연결한 후 초기화를 해야 사용 준비가 완료됩니다.

> [AZURE.NOTE]모범 사례는 별도 디스크를 하나 이상 사용하여 가상 컴퓨터의 데이터를 저장하는 것입니다. Azure 가상 컴퓨터를 만들면 C 드라이브에 매핑되는 운영 체제용 디스크와 D 드라이브에 매핑되는 임시 디스크가 있습니다. **D 드라이브는 데이터 저장에 사용하지 마세요.** 이름에서 추측할 수 있듯이 D 드라이브는 임시 저장소만 제공합니다. Azure 저장소에 상주하지 않으므로 중복성이나 백업을 제공하지 않습니다.

## 연습 동영상

이 자습서의 단계 연습은 다음과 같습니다.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>방법: Windows Server에서 새 데이터 디스크 초기화

1. 가상 컴퓨터에 연결합니다. 지침은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][logon]을 참조하세요.

2. 가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **파일 및 저장소 서비스**를 선택합니다.

	![서버 관리자 열기](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. 메뉴를 확장하고 **디스크**를 선택합니다.

4. **디스크** 섹션의 목록에는 디스크 0, 디스크 1, 디스크 2의 3개 디스크가 있습니다. 디스크 0은 운영 체제 디스크이며, 디스크 1은 임시 리소스 디스크(데이터 저장소에 사용되지 않음), 디스크 2는 가상 컴퓨터에 연결한 데이터 디스크입니다. 데이터 디스크의 용량은 디스크를 연결할 때 지정한 대로 5GB입니다. 디스크 2를 마우스 오른쪽 단추로 클릭한 다음 **초기화**를 선택합니다.

5.	디스크가 초기화 될 때 모든 데이터를 삭제된다고 알려 줍니다. **예**클릭하여 경고를 확인하고 디스크를 초기화합니다. 그런 다음, 디스크 2를 마우스 오른쪽 단추로 다시 클릭하고 **새 볼륨**을 선택합니다.

6.	기본값을 사용하여 마법사를 완료합니다. 마법사를 완료하면 새 볼륨이 **볼륨** 섹션에 나열됩니다. 이제 디스크가 온라인 상태이며 데이터를 저장할 준비가 완료되었습니다.

	![볼륨 초기화됨](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]가상 컴퓨터의 크기로 연결할 수 있는 디스크 개수가 결정됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-size-specs.md)를 참조하세요.

## 추가 리소스

[Windows 가상 컴퓨터에서 디스크를 분리하는 방법](storage-windows-detach-disk.md)

[가상 컴퓨터용 디스크 및 VHD에 대하여](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=September15_HO1-->