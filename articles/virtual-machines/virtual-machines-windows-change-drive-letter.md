<properties
	pageTitle="Windows 임시 디스크의 드라이브 문자를 변경하는 방법"
	description="Azure에서 Windows 기반 VM의 임시 디스크를 다시 매핑하는 방법에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="kathydav"/>

#Windows 임시 디스크의 드라이브 문자를 변경하는 방법

D 드라이브를 사용하여 데이터를 저장해야 하는 경우 다음 지침에 따라 임시 디스크에 다른 드라이브를 사용할 수 있습니다. 보관해야 하는 데이터를 저장하는 데 임시 디스크를 사용하지 마세요.

시작하기 전에 이 절차를 수행하는 동안 Windows 페이지 파일(pagefile.sys)을 저장할 수 있도록 데이터 디스크를 가상 컴퓨터에 연결해야 합니다. 아직 연결하지 않은 경우 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법][Attach]을 참조하세요. 연결된 디스크를 확인하는 방법에 자세한 내용은 [Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법][Detach]의 "디스크 찾기"를 참조하세요.

D 드라이브에서 기존 데이터 디스크를 사용하려면 VHD를 저장소 계정으로 업로드해야 합니다. 지침은 [Windows Server VHD를 만들어 Azure에 업로드][VHD]의 3, 4단계를 참조하세요.

> [AZURE.WARNING]가상 컴퓨터의 크기를 조정하고 이로 인해 가상 컴퓨터가 다른 호스트로 이동한 경우에는 임시 디스크가 다시 D 드라이브로 변경됩니다.

##드라이브 문자 변경

1. 가상 컴퓨터에 로그온합니다. 자세한 내용은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Logon]을 참조하세요.

2. D 드라이브의 pagefile.sys를 다른 드라이브로 옮깁니다.

3. 가상 컴퓨터를 다시 시작합니다.

4. 다시 로그온하고 드라이브 문자를 D에서 E로 변경합니다.

5. [Azure 포털](http://manage.windowsazure.com)에서 기존 데이터 디스크 또는 빈 데이터 디스크를 연결합니다.

6.	가상 컴퓨터에 다시 로그온하고 디스크를 초기화한 후 방금 연결한 디스크의 드라이브 문자로 D를 할당합니다.

7.	E가 임시 디스크에 매핑되어 있는지 확인합니다.

8.	다른 드라이브에 옮겨 놓았던 pagefile.sys를 E 드라이브로 옮깁니다.

## 추가 리소스
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Logon]

[Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법][Detach]

[Azure 저장소 계정 정보][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=August15_HO7-->