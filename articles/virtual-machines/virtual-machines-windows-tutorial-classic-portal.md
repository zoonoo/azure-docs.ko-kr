<properties
	pageTitle="Azure에서 Windows를 실행하는 가상 컴퓨터 만들기"
	description="Azure 포털에서 Windows 가상 컴퓨터만들기"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

# Azure 포털에서 Windows를 실행하는 가상 컴퓨터 만들기

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다. [리소스 관리자 배포 모델](virtual-machines-windows-tutorial.md)을 사용하여 리소스를 만들 수도 있습니다.

이 자습서에서는 Azure 포털에서 Azure 가상 컴퓨터(VM)를 간편하게 만드는 방법을 보여줍니다. 한 예로, Windows Server 이미지를 사용할 것이지만, 해당 아미지는 Azure가 제공하는 여러 이미지 중 하나일 뿐입니다. 참고: 이미지 선택은 구독에 따라 달라집니다. 예를 들어 데스크톱 이미지는 MSDN 구독자가 사용할 수 있습니다.

[사용자 고유의 이미지](virtual-machines-create-upload-vhd-windows-server.md)를 사용하여 VM을 만들 수도 있습니다. 이 방법 및 다른 방법에 대한 자세한 내용은 [Windows 가상 컴퓨터를 만드는 다양한 방법](virtual-machines-windows-choices-create-vm.md)을 참조하세요.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 연습 동영상

이 자습서의 연습은 다음과 같습니다.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>가상 컴퓨터를 만드는 방법

이 섹션에서는 Azure 포털의 **갤러리에서** 옵션을 사용하여 가상 컴퓨터를 만드는 방법을 보여 줍니다. 이 옵션은 **빠른 생성** 옵션보다 많은 구성 선택 항목을 제공합니다. 예를 들어 가상 컴퓨터를 가상 네트워크에 가입하려는 경우 **갤러리에서** 옵션을 사용해야 합니다.

> [AZURE.NOTE]또한 보다 다양하며 사용자 지정 가능한 [Azure Preview 포털](https://portal.azure.com)에서 가상 컴퓨터를 만들고, 향상된 모니터링 및 진단과 프리미엄 저장소 등을 사용해 볼 수 있습니다. 두 포털에서의 가상 컴퓨터 구성에 사용 가능한 옵션은 상당 부분 중복되지만 동일하지는 않습니다. 예를 들어, 미리 보기 포털을 사용하여 프리미엄 저장소를 적용한 가상 컴퓨터를 구성합니다.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## 다음 단계

- 가상 컴퓨터에 로그온합니다. 지침은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-log-on-windows-server.md)을 참조하세요.

- 데이터를 저장할 디스크를 연결합니다. 빈 디스크와 데이터가 포함된 디스크를 모두 연결할 수 있습니다. 자세한 내용은 [데이터 디스크 연결 자습서](storage-windows-attach-disk.md)를 참조하세요.

<!---HONumber=Oct15_HO2-->