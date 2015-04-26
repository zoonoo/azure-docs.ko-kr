<properties 
	pageTitle="가상 컴퓨터에 디스크 연결 | Azure" 
	description="데이터 디스크를 Azure 가상 컴퓨터에 연결하고 사용 가능하도록 초기화하는 방법에 대해 알아봅니다." 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법

빈 디스크와 데이터가 포함된 디스크를 모두 연결할 수 있습니다. 두 경우 모두, 디스크는 실제로 Azure 저장소 계정에 상주하는 .vhd 파일입니다. 또한 두 경우 모두 디스크를 연결한 후 초기화를 해야 사용 준비가 완료됩니다. 

> [AZURE.NOTE] 모범 사례는 별도 디스크를 하나 이상 사용하여 가상 컴퓨터의 데이터를 저장하는 것입니다. Azure 가상 컴퓨터를 만들면 C 드라이브에 매핑되는 운영 체제용 디스크와 D 드라이브에 매핑되는 임시 디스크가 있습니다. **D 드라이브는 데이터 저장에 사용하지 마세요.** 이름이 의미하는 것과 같이 D 드라이브는 임시 저장소만 제공합니다. Azure 저장소에 상주하지 않으므로 중복성이나 백업을 제공하지 않습니다.

- [방법: 빈 디스크 연결](#attachempty)
- [방법: 기존 디스크 연결](#attachexisting)
- [방법: Windows Server에서 새 데이터 디스크 초기화](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>방법: Windows Server에서 새 데이터 디스크 초기화

1. 가상 컴퓨터에 연결합니다. 자세한 내용은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][logon]을 참조하세요.

2. 로그온한 후 **서버 관리자**를 열고 왼쪽 창에서 **저장소**를 확장한 후 **디스크 관리**를 클릭합니다.



	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)



3. **디스크 2**를 마우스 오른쪽 단추로 클릭한 후 **디스크 초기화**를 클릭하고 **확인**을 클릭합니다.



	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)


4. 디스크 2에 대한 공간 할당 영역을 마우스 오른쪽 단추로 클릭하고 **새 단순 볼륨**을 클릭한 다음 기본값을 사용하여 마법사를 완료합니다.
 

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)


[logon]: ../virtual-machines-log-on-windows-server/



	이제 디스크가 온라인 상태이며 새 드라이브 문자로 사용할 수 있습니다.



	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] 가상 컴퓨터에 연결할 수 있는 디스크 수는 가상 컴퓨터의 크기에 따라 달라집니다. 예를 들어 표준 A2에는 디스크 4개만 연결할 수 있지만 표준 D14에는 디스크 32개, 표준 G5에는 디스크 64개를 연결할 수 있습니다. 가상 컴퓨터 크기별로 연결할 수 있는 디스크 수에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/ko-kr/library/azure/dn197896.aspx)에서 확인할 수 있습니다.



<!--HONumber=42-->
