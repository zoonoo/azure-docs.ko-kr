<properties
	pageTitle="임시 디스크의 드라이브 문자 변경 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터에서 임시 디스크의 드라이브 문자 변경"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
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
	ms.author="cynthn"/>

#클래식 배포 모델을 사용하여 만든 가상 컴퓨터에서 Windows 임시 디스크의 드라이브 문자 변경

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

D 드라이브를 사용하여 데이터를 저장해야 하는 경우 다음 지침에 따라 임시 디스크에 다른 드라이브를 사용할 수 있습니다. 보관해야 하는 데이터를 저장하는 데 임시 디스크를 사용하지 마세요.

시작하기 전에 이 절차를 수행하는 동안 Windows 페이지 파일(pagefile.sys)을 저장할 수 있도록 데이터 디스크를 가상 컴퓨터에 연결해야 합니다. 아직 연결하지 않은 경우 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법][Attach]을 참조하세요. 연결된 디스크를 확인하는 방법에 자세한 내용은 [Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법][Detach]의 "디스크 찾기"를 참조하세요.

D 드라이브에서 기존 데이터 디스크를 사용하려면 VHD를 저장소 계정으로 업로드해야 합니다. 지침은 [Windows Server VHD를 만들어 Azure에 업로드][VHD]의 3, 4단계를 참조하세요.

> [AZURE.WARNING]가상 컴퓨터의 크기를 조정하거나 "중지(할당 취소)"하는 경우 새 하이퍼바이저로 가상 컴퓨터의 배치를 트리거할 수 있습니다. 계획되거나 계획되지 않은 유지 관리 이벤트로 이 배치가 트리거될 수도 있습니다. 이 시나리오에서는 임시 디스크가 첫 번째 사용 가능한 드라이브 문자에 다시 할당됩니다. 특히 "D" 드라이브를 필요로 하는 응용 프로그램이 있는 경우 페이지 파일을 이동한 후 새 영구 디스크를 할당하고 이를 문자 D에 할당합니다. Azure에서는 문자 D를 회수하지 않습니다.

> [AZURE.WARNING]페이지 파일을 명시적으로 이동한 후 가상 컴퓨터 크기를 조정할 경우 새 가상 컴퓨터의 임시 디스크가 원래 VM 크기의 페이지 파일을 포함할 만큼 크지 않은 경우 부팅 시 오류가 발생할 수 있습니다. 또한 임시 드라이브가 다음 사용 가능한 드라이브 문자로 설정되지 않은 경우 Azure가 다음 사용 가능한 드라이브 문자로 임시 드라이브를 만드는 동안 Windows가 페이지 파일 구성에서 유효하지 않은 드라이브 문자를 참조하므로 이 오류가 발생할 수도 있습니다.

##드라이브 문자 변경

1. 가상 컴퓨터에 로그온합니다. 자세한 내용은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Logon]을 참조하세요.

2. D 드라이브의 pagefile.sys를 다른 드라이브로 옮깁니다.

3. 가상 컴퓨터를 다시 시작합니다.

4. 다시 로그온하고 드라이브 문자를 D에서 E로 변경합니다.

5. [Azure 포털](http://manage.windowsazure.com)에서 기존 데이터 디스크 또는 빈 데이터 디스크를 연결합니다.

6.	가상 컴퓨터에 다시 로그온하고 디스크를 초기화한 후 방금 연결한 디스크의 드라이브 문자로 D를 할당합니다.

7.	E가 임시 디스크에 매핑되어 있는지 확인합니다.

8.	다른 드라이브에 옮겨 놓았던 pagefile.sys를 E 드라이브로 옮깁니다.

9.	가상 컴퓨터를 다시 시작합니다.



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

<!---HONumber=Sept15_HO4-->