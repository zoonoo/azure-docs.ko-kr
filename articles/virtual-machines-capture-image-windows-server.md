<properties 
	pageTitle="Windows Server를 실행하는 가상 컴퓨터의 이미지 캡처" 
	description="Windows Server 2008 R2를 실행하는 Azure VM(가상 컴퓨터)의 이미지를 캡처하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
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
	ms.date="03/13/2015" 
	ms.author="kathydav"/>

#Windows 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법#

이 문서에서는 Windows가 실행되는 Azure 가상 컴퓨터를 캡처하여 다른 가상 컴퓨터를 만들 때 템플릿처럼 사용하는 방법을 소개합니다. 이 템플릿에는 OS 디스크를 비롯해 가상 컴퓨터에 연결되는 모든 데이터 디스크가 포함됩니다. 네트워킹 구성은 포함되지 않으므로 템플릿을 사용하는 다른 가상 컴퓨터를 만들 때 구성해야 합니다.

Azure에서는 이 템플릿이 이미지로 취급되고 **내 이미지**에 저장됩니다. 이 위치에 업로드한 이미지도 저장됩니다. 이미지에 대한 자세한 내용은 [Azure의 가상 컴퓨터 이미지 정보][]를 참조하세요.

##시작하기 전에##

이러한 단계는 이미 Azure 가상 컴퓨터를 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. 아직 완료하지 못한 경우 다음 지침을 참조하세요.

- [사용자 지정 가상 컴퓨터를 만드는 방법][]
- [가상 컴퓨터에 데이터 디스크를 연결하는 방법][]

##가상 컴퓨터 캡처##

1. 명령 모음에서 **연결**을 클릭하여 가상 컴퓨터에 연결합니다. 자세한 내용은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][]을 참조하십시오.

2.	관리자로 명령 프롬프트 창을 엽니다.


3.	디렉터리를 `%windir%\system32\sysprep`로 변경한 후 sysprep.exe를 실행합니다.


4. 	**시스템 준비 도구** 대화 상자가 나타납니다. 다음을 수행합니다.


	- **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개][]를 참조하세요.

	- **종료 옵션**에서 **종료**를 선택합니다.

	- **확인**을 클릭합니다.

	![Sysprep 실행](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep에서 가상 컴퓨터를 종료합니다. 그러면 [관리 포털](http://manage.windowsazure.com)의 컴퓨터 상태가 **중지됨**으로 변경됩니다.


8.	**가상 컴퓨터**를 클릭한 후 캡처하려는 가상 컴퓨터를 선택합니다.

9.	명령 모음에서 **캡처**를 클릭합니다.

	![가상 컴퓨터 캡처](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	**가상 컴퓨터 캡처** 대화 상자가 나타납니다.

10.	**이미지 이름**에 새 이미지의 이름을 입력합니다.

11.	사용자 지정 이미지 집합에 Windows Server 이미지를 추가하기 전에 이전 단계에서 설명한 대로 Sysprep을 실행하여 범용화해야 합니다. **가상 컴퓨터에서 Sysprep을 실행했습니다.**를 클릭하여 이를 수행했음을 나타냅니다.

12.	확인 표시를 클릭하여 이미지를 캡처합니다.

  **참고: 범용화한 가상 컴퓨터의 이미지를 캡처하면 가상 컴퓨터가 삭제됩니다.**

 이제 **이미지**에서 새 이미지를 사용할 수 있습니다.![이미지 캡처 성공](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##다음 단계##
가상 컴퓨터를 만들기 위한 템플릿으로 사용할 이미지가 마련됩니다. 이렇게 하려면 **갤러리에서** 방법을 사용하여 사용자 지정 가상 컴퓨터를 만들고 위에서 만든 이미지를 선택합니다. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법][](영문)을 참조하십시오.

	
[Azure의 가상 컴퓨터 이미지 정보]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[사용자 지정 가상 컴퓨터를 만드는 방법]: virtual-machines-create-custom.md
[가상 컴퓨터에 데이터 디스크를 연결하는 방법]: storage-windows-attach-disk.md
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: http://www.windowsazure.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[Sysprep 사용 방법: 소개]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=58-->