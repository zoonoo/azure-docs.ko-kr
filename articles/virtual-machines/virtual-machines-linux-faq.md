<properties
	pageTitle="Linux VM에 대한 FAQ | Microsoft Azure"
	description="리소스 관리자 모델을 사용하여 만든 Linux 가상 컴퓨터에 대해 가장 일반적인 질문 중 일부에 대한 답변을 제공합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="cynthn"/>

# Linux 가상 컴퓨터에 대한 질문과 대답 


이 문서에서는 Azure에서 리소스 관리자 배포 모델을 사용하여 만든 Linux 가상 컴퓨터에 대한 일반적인 사용자 질문을 일부 해결합니다. 이 항목의 Windows 버전에 대해서는 [Windows 가상 컴퓨터에 대한 질문과 대답](virtual-machines-windows-faq.md)을 참조하세요.

## Azure VM에서 무엇을 실행할 수 있습니까?

모든 구독자는 Azure 가상 컴퓨터에서 서버 소프트웨어를 실행할 수 있습니다. 자세한 내용은 [Azure 인증 배포의 Linux](virtual-machines-linux-endorsed-distros.md)를 참조하세요.


## 가상 컴퓨터에 얼마나 많은 용량의 저장소를 사용할 수 있습니까?

각 데이터 디스크의 최대 용량은 1 TB 입니다. 사용할 수 있는 데이터 디스크의 수는 가상 컴퓨터의 크기에 따라 달라집니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md)를 참조하세요.

Azure 저장소 계정은 운영 체제 디스크 및 모든 데이터 디스크에 대한 저장소를 제공합니다. 각 디스크는 페이지 blob으로 저장된 .vhd 파일입니다. 가격 책정에 대한 자세한 내용은 [저장소 가격 세부 정보](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.



## 나의 가상 컴퓨터에 액세스 하려면 어떻게 해야 합니까?

가상 컴퓨터 로그온 하려면 SSH(Secure Shell)를 사용하여 원격 연결을 설정해야 합니다. [Windows에서](virtual-machines-linux-ssh-from-windows.md) 또는 [Linux 및 Mac에서](virtual-machines-linux-ssh-from-linux.md) 연결하는 방법은 지침을 참조하세요. 기본적으로, SSH는 최대 10개의 동시 연결을 허용합니다. 구성 파일을 편집하여 이 수를 늘릴 수 있습니다.


문제가 있는 경우 [SSH(Secure Shell) 연결 문제 해결](virtual-machines-linux-troubleshoot-ssh-connection.md)을 확인하세요.

## 임시 디스크(/dev/sdb1)를 데이터 저장에 사용할 수 있나요?

임시 디스크 (/dev/sdb1)를 데이터 저장에 사용하지 마세요. 해당 드라이브는 임시 저장소일 뿐이므로 복구할 수 없는 데이터가 손실될 위험이 있습니다.

## 기존 Azure VM을 복사 또는 복제할 수 있나요?

예. 자세한 내용은 [리소스 관리자 배포 모델에서 Linux 가상 컴퓨터의 복사본을 만드는 방법](virtual-machines-linux-specialized-image.md)을 참조하세요.

## Azure Resource Manager를 통해 캐나다 중부 및 캐나다 동부 지역이 보이지 않는 이유가 무엇인가요?

캐나다 중부 및 캐나다 동부의 새로운 두 지역은 기존의 Azure 구독에 대한 가상 컴퓨터 만들기에 자동으로 등록되지 않습니다. 가상 컴퓨터가 Azure 포털을 통해 Azure Resource Manager를 사용하는 다른 지역에 배포될 때 자동으로 등록됩니다. 가상 컴퓨터가 다른 Azure 지역에 배포된 후 새로운 지역은 다음 가상 컴퓨터에 대해 사용할 수 있어야 합니다.

<!---HONumber=AcomDC_0518_2016-->