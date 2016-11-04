---
title: Azure Windows VM의 이미지 캡처 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 만든 Azure Windows 가상 컴퓨터의 이미지를 캡처합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model."></a>클래식 배포 모델을 사용하여 만든 Azure Windows 가상 컴퓨터의 이미지를 캡처합니다.
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager 모델 정보에 대해서는 [Azure에서 실행되는 복사본 Windows VM 만들기](virtual-machines-windows-vhd-copy.md)를 참조하세요.

이 문서에서는 Windows가 실행되는 Azure 가상 컴퓨터를 캡처하여 다른 가상 컴퓨터를 만들 때 이미지로 사용하는 방법을 소개합니다. 이 이미지에는 OS 디스크를 비롯해 가상 컴퓨터에 연결되는 모든 데이터 디스크가 포함됩니다. 네트워킹 구성은 포함되지 않으므로, 이미지를 사용하는 다른 가상 컴퓨터를 만들 때 구성해야 합니다.

Azure에서는 이미지를 **내 이미지**아래에 저장합니다. 사용자가 업로드한 모든 이미지도 이 위치에 저장됩니다. 이미지에 대한 자세한 내용은 [가상 컴퓨터 이미지 정보](virtual-machines-linux-classic-about-images.md)를 참조하세요.

## <a name="before-you-begin##"></a>시작하기 전에
이러한 단계는 이미 Azure 가상 컴퓨터를 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. 아직 완료하지 못한 경우 다음 지침을 참조하세요.

* [이미지에서 가상 컴퓨터 만들기](virtual-machines-windows-classic-createportal.md)
* [가상 컴퓨터에 데이터 디스크를 연결하는 방법](virtual-machines-windows-classic-attach-disk.md)
* 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

> [!WARNING]
> 이 프로세스는 캡처된 후 원래의 가상 컴퓨터를 삭제합니다. 
> 
> 

Azure 가상 컴퓨터의 이미지를 캡처하기 전에 대상 가상 컴퓨터를 백업하는 것이 좋습니다. Azure 백업을 사용하여 Azure 가상 컴퓨터를 백업할 수 있습니다. 자세한 내용은 [Azure 가상 컴퓨터 백업](../backup/backup-azure-vms.md)을 참조하세요. 다른 솔루션은 인증된 파트너에서 사용할 수 있습니다. 무엇이 현재 사용 가능한지 알아보려면, Azure 마켓플레이스를 검색합니다.

## <a name="capture-the-virtual-machine"></a>가상 컴퓨터 캡처
1. [Azure 클래식 포털](http://manage.windowsazure.com)에서, 가상 컴퓨터를 **연결** 합니다. 지침은 [Windows Server를 실행하여 가상 컴퓨터에 로그인하는 방법] []을 참조하세요.
2. 관리자로 명령 프롬프트 창을 엽니다.
3. 디렉터리를 `%windir%\system32\sysprep`로 변경한 후 sysprep.exe를 실행합니다.
4. **시스템 준비 도구** 대화 상자가 나타납니다. 다음을 수행합니다.
   
   * **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개][Sysprep 사용 방법: 소개]를 참조하세요.
   * **종료 옵션**에서 **종료**를 선택합니다.
   * **확인**을 클릭합니다.
   
   ![Sysprep 실행](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)
5. Sysprep는 Azure 클래식 포털에서 가상 컴퓨터의 상태를 변경하는 가상 컴퓨터를 **중지**하기 위해 차단시킵니다.
6. Azure 클래식 포털에서, **가상 컴퓨터** 를 클릭하고 캡쳐할 가상 컴퓨터를 선택합니다.
7. 명령 모음에서 **캡처**를 클릭합니다.
   
   ![가상 컴퓨터 캡처](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)
   
   **가상 컴퓨터 캡처** 대화 상자가 나타납니다.
8. **이미지 이름**에 새 이미지의 이름을 입력합니다.
9. 사용자 지정 이미지 집합에 Windows Server 이미지를 추가하기 전에 이전 단계에서 설명한 대로 Sysprep을 실행하여 범용화해야 합니다. **가상 컴퓨터에서 Sysprep을 실행했습니다.** 를 클릭하여 이를 수행했음을 나타냅니다.
10. 확인 표시를 클릭하여 이미지를 캡처합니다. 이제 **이미지**에서 새 이미지를 사용할 수 있습니다.
    
    ![이미지 캡처 성공](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>다음 단계
이제 이미지를 사용하여 가상 컴퓨터를 만들 수 있습니다. 이렇게 하려면 **갤러리에서** 메뉴 항목을 사용하여 가상 컴퓨터를 만들고 위에서 만든 이미지를 선택합니다. 지침에 대해서는 [이미지에서 가상 컴퓨터 만들기](virtual-machines-windows-classic-createportal.md)를 참조하세요.

[Windows Server를 실행하는 가상 컴퓨터에 로그인하는 방법]: virtual-machines-windows-classic-connect-logon.md
[Sysprep 사용 방법: 소개]: http://technet.microsoft.com/library/bb457073.aspx
[Sysprep.exe 실행]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Sysprep.exe 옵션 입력]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[가상 컴퓨터가 중지됨]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[가상 컴퓨터의 이미지 캡처]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[이미지 이름 입력]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[이미지 캡처 성공]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[캡처한 이미지 사용]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Oct16_HO2-->


