<properties
	pageTitle="VM에 디스크 연결 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터에 데이터 디스크를 연결하고 초기화합니다."
	services="virtual-machines-windows, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="cynthn"/>

# 클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터에 데이터 디스크 연결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-attach-disk-portal.md).

추가 데이터 디스크가 필요한 경우 빈 디스크나, 데이터가 있는 기존 디스크를 VM에 연결할 수 있습니다. 두 경우 모두, 디스크는 Azure 저장소 계정에 상주하는 .vhd 파일입니다. 새 디스크의 경우 디스크를 연결한 후, Windows VM에서 사용할 수 있게 초기화가 필요합니다.

디스크에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](virtual-machines-windows-about-disks-vhds.md)를 참조하세요.

## 연습 동영상

이 자습서의 단계 [비디오 연습](https://azure.microsoft.com/documentation/videos/attaching-a-data-disk-to-a-windows-vm/)은 다음과 같습니다.


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## 디스크 초기화

1. 가상 컴퓨터에 연결합니다. 지침은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][logon]을 참조하세요.

2. 가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **파일 및 저장소 서비스**를 선택합니다.

	![서버 관리자 열기](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. 메뉴를 확장하고 **디스크**를 선택합니다.

4. **디스크** 섹션은 디스크를 나열합니다. 대부분의 경우에서 디스크 0, 디스크 1 및 디스크 2가 됩니다. 디스크 0은 운영 체제 디스크이고, 디스크 1은 임시 리소스 디스크이며, 디스크 2는 방금 가상 컴퓨터에 연결한 데이터 디스크입니다. 새 데이터 디스크의 파티션은 **알 수 없음**으로 나열됩니다. 디스크를 마우스 오른쪽 단추로 클릭한 다음 **초기화**를 선택합니다.

5.	디스크가 초기화 될 때 모든 데이터를 삭제된다고 알려 줍니다. **예**를 클릭하여 경고를 확인하고 디스크를 초기화합니다. 완료되면 파티션이 **GPT**로 나열됩니다. 디스크를 다시 마우스 오른쪽 단추로 클릭하고 **새 볼륨**을 선택합니다.

6.	기본값을 사용하여 마법사를 완료합니다. 마법사를 완료하면 새 볼륨이 **볼륨** 섹션에 나열됩니다. 이제 디스크가 온라인 상태이며 데이터를 저장할 준비가 완료되었습니다.

	![볼륨 초기화됨](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] VM의 크기로 연결할 수 있는 디스크 개수가 결정됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md)를 참조하세요.

## 추가 리소스

[Windows 가상 컴퓨터에서 디스크를 분리하는 방법](virtual-machines-windows-classic-detach-disk.md)

[가상 컴퓨터용 디스크 및 VHD에 대하여](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md

<!---HONumber=AcomDC_0525_2016-->