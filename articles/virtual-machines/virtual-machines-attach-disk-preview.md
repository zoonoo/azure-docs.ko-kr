<properties
	pageTitle="데이터 디스크 연결 | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 Azure Preview 포털에서 새 데이터 디스크 또는 기존 데이터 디스크를 VM에 연결하는 방법"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="cynthn"/>

# Azure Preview 포털에서 데이터 디스크를 연결하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 리소스 관리자 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다. [클래식 배포 모델](storage-windows-attach-disk.md)을 사용하여 리소스를 만들 수도 있습니다.

이 문서에서는 Azure Preview 포털을 통해 새 디스크와 기존 디스크를 모두 가상 컴퓨터에 연결하는 방법을 보여줍니다. 이 작업을 수행 하기 전에 다음 팁을 검토하세요.

- 가상 컴퓨터의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-size-specs.md)를 참조하세요.
- 프리미엄 저장소를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 컴퓨터가 필요합니다. 이 가상 컴퓨터를 사용하여 프리미엄 및 표준 저장소 계정에서 모두 디스크를 사용할 수 있습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../storage/storage-premium-storage-preview-portal.md)를 참조하세요.
- 가상 컴퓨터에 연결된 디스크는 실제로 Azure 저장소 계정의 .vhd 파일입니다. 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](virtual-machines-disks-vhds.md)를 참조하세요.
- 새 디스크의 경우 Azure가 디스크를 연결할 때 생성하므로 먼저 생성하지 않아도 됩니다.
- 기존 디스크의 경우 Azure 저장소 계정에서 .vhd 파일을 사용할 수 있어야 합니다. 다른 가상 컴퓨터에 연결되어 있지 않은 경우 이미 있는 디스크를 사용하거나 자기만의 .vhd 파일을 저장소 계정에 업로드할 수 있습니다.

## 가상 컴퓨터 찾기

1. [Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **찾아보기**를 클릭합니다.

3. 검색 블레이드에서 아래로 스크롤하여 **가상 컴퓨터**를 클릭합니다.

	![가상 컴퓨터 검색](./media/virtual-machines-attach-disk-preview/search-blade-preview-portal.png)

4.	목록에서 가상 컴퓨터를 선택합니다.

5. 오른쪽의 **Essentials** 아래에서 **모든 설정**을 클릭한 다음 **디스크**를 클릭합니다.

	![디스크 설정 열기](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

새 디스크 또는 기존 디스크를 연결하려면 다음 지침에 따라 계속합니다.

## 옵션 1: 새 디스크 연결

1.	**디스크** 블레이드에서 **새 연결**을 클릭합니다.

2.	기본 설정을 검토하고 필요에 따라 업데이트한 다음 **확인**을 클릭합니다.

 	![디스크 설정 검토](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	Azure가 디스크를 만들고 가상 컴퓨터에 연결한 후 가상 컴퓨터의 디스크 설정에서 **데이터 디스크** 아래에 새 디스크가 나열됩니다.

## 옵션 2: 기존 디스크 연결

1.	**디스크** 블레이드에서 **기존 연결**을 클릭합니다.

2.	**기존 디스크 연결**에서 **VHD 파일**을 클릭합니다.

	![기존 디스크 연결](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	**저장소 계정**에서 계정 및 .vhd 파일을 보관하는 컨테이너를 선택합니다.

	![VHD 위치 찾기](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	.vhd 파일을 업로드합니다.

5.	**기존 디스크 연결**에서 방금 선택한 파일이 **VHD 파일** 아래에 나열됩니다. **확인**을 클릭합니다.

6.	Azure가 디스크를 가상 컴퓨터에 연결한 후 가상 컴퓨터의 디스크 설정에서 **데이터 디스크** 아래에 해당 디스크가 나열됩니다.

## 다음 단계

디스크를 추가한 후 가상 컴퓨터의 운영 체제에서 사용하도록 준비해야 합니다.

- Linux의 경우 이 [문서](virtual-machines-linux-how-to-attach-disk.md)의 "방법: Linux에서 새 데이터 디스크 초기화"를 참조하세요.
- Windows의 경우 이 [문서](storage-windows-attach-disk.md)의 "방법: Windows에서 새 데이터 디스크 초기화"를 참조하세요.

## 추가 리소스

[Azure 저장소 계정 정보]

<!--Link references-->

[Azure 저장소 계정 정보]: ../storage-whatis-account/

<!---HONumber=Sept15_HO4-->