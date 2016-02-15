    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# VM 템플릿 만들기

## 개요

[랩을 만든](devtest-lab-create-lab.md) 후 VM 템플릿 목록에서 [해당 랩에 VM을 추가](devtest-lab-add-vm-with-artifacts.md)할 수 있습니다. 이 문서에서는 VM을 만들 템플릿인 VHD(가상 하드 디스크) 이미지 파일을 업로드하고 구성하는 방법에 대해 알아봅니다. VHD 이미지에 익숙하지 않은 경우 VHD 이미지를 만드는 방법을 알아보려면 [Windows Server VHD를 만들어서 Azure에 업로드](/virtual-machines/virtual-machines-create-upload-vhd-windows-server.md)를 참조하세요. VHD 이미지를 만들고 이에 대한 액세스 권한을 얻었으면 이 문서가 해당 이미지를 업로드하고 이 이미지로부터 템플릿을 만드는 방법을 안내합니다.

## VM 템플릿 만들기

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭한 다음 목록에서 **DevTest Lab**을 탭합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 랩 블레이드에서 **설정**을 탭합니다.

    ![랩 설정](./media/devtest-lab-create-template/lab-blade-settings.png)

1. 랩 **설정** 블레이드에서 **템플릿**을 탭합니다.

    ![템플릿 옵션](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. **템플릿** 블레이드에서 **+ 템플릿**을 탭합니다.

    ![템플릿 추가](./media/devtest-lab-create-template/add-template.png)

1. **템플릿 추가** 블레이드에서

	1. 템플릿의 이름을 입력합니다. 이 이름은 새 VM을 만들 때 템플릿 목록에 표시됩니다.

	1. 템플릿에 대한 설명을 입력합니다. 이 설명은 새 VM을 만들 때 템플릿 목록에 표시됩니다.

	1. **이미지**를 탭합니다.

	1. 원하는 이미지가 나열되지 않은 상태에서 이미지를 추가하려는 경우 [새 템플릿 이미지 추가](#add-a-new-template-image) 섹션으로 건너뛴 다음 완료되면 여기로 돌아옵니다.

	1. 원하는 이미지를 선택합니다.

	1. **확인**을 눌러 **템플릿 추가** 블레이드를 닫습니다.

1. **OS 구성**을 탭합니다.

1. **OS 구성** 탭에서 **Windows** 또는 **Linux**를 선택합니다.

1. **Windows**가 선택되면 확인란을 통해 컴퓨터에서 *Sysprep*의 실행 여부를 지정합니다.

1. 컴퓨터의 **사용자 이름**을 입력합니다.

1. 컴퓨터의 **암호**를 입력합니다. **참고:** 암호는 일반 텍스트로 표시됩니다.

1. **확인**을 탭하여 **OS 구성** 블레이드를 닫습니다.

1. **위치**를 지정합니다.

1. **확인**을 탭하여 템플릿을 만듭니다.

##새 템플릿 이미지 추가

새 템플릿 이미지를 추가하려면 VHD 이미지 파일에 액세스해야 합니다.

1. **템플릿 이미지 추가** 블레이드에서 **Upload an image using PowerShell**(PowerShell을 사용하여 이미지 업로드)을 탭합니다.

    ![이미지 업로드](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 다음 블레이드는 Azure 구독에 VHD 이미지 파일을 업로드하는 PowerShell 스크립트를 수정하고 실행하기 위한 지침을 표시합니다. **참고:** 이 프로세스는 이미지 파일 크기 및 연결 속도에 따라 시간이 오래 걸릴 수 있습니다.

##다음 단계

VM을 만들 때 사용할 VM 템플릿을 추가했으면 다음 단계는 [DevTest Lab에 VM을 추가](devtest-lab-add-vm-with-artifacts)하는 것입니다.

<!---HONumber=AcomDC_0204_2016-->