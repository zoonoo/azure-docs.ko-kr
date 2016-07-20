<properties
	pageTitle="Linux VM에 데이터 디스크 연결 | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 Azure 포털의 Linux VM에 신규 및 기존 데이터 디스크를 연결하는 방법."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# Azure 포털에서 Linux VM에 데이터 디스크를 연결하는 방법

이 문서에서는 Azure 포털을 통해 신규 및 기존 디스크를 Linux 가상 컴퓨터에 연결하는 방법을 보여줍니다. 또한 [Azure 포털에서 Windows VM에 데이터 디스크를 연결](virtual-machines-windows-attach-disk-portal.md)할 수도 있습니다. 이 작업을 수행 하기 전에 다음 팁을 검토하세요.

- 가상 컴퓨터의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md)를 참조하세요.
- 프리미엄 저장소를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 컴퓨터가 필요합니다. 이 가상 컴퓨터를 사용하여 프리미엄 및 표준 저장소 계정에서 모두 디스크를 사용할 수 있습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../storage/storage-premium-storage.md)를 참조하세요.
- 가상 컴퓨터에 연결된 디스크는 실제로 Azure 저장소 계정의 .vhd 파일입니다. 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](virtual-machines-linux-about-disks-vhds.md)를 참조하세요.
- 새 디스크의 경우 Azure가 디스크를 연결할 때 생성하므로 먼저 생성하지 않아도 됩니다.
- 기존 디스크의 경우 Azure 저장소 계정에서 .vhd 파일을 사용할 수 있어야 합니다. 다른 가상 컴퓨터에 연결되지 않은 경우 이미 있는 .vhd 파일을 사용하거나 고유의 .vhd 파일을 저장소 계정에 업로드할 수 있습니다.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## 다음 단계

디스크를 추가한 후 해당 디스크를 사용할 수 있도록 준비해야 합니다. 이 [문서](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux)에서 가상 컴퓨터 운영 체제의 "방법: Linux에서 새 데이터 디스크 초기화"를 참조하세요.

<!---HONumber=AcomDC_0706_2016-->