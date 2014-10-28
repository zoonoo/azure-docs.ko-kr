<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Linux 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법

이 문서에서는 Linux 가상 컴퓨터를 캡처하여 템플릿으로 사용하고 이를 통해 다른 가상 컴퓨터를 만드는 방법을 소개합니다. 이 가상 컴퓨터 템플릿에는 OS 디스크를 비롯해 가상 컴퓨터에 연결되는 모든 데이터 디스크가 포함됩니다. 네트워킹 구성은 포함되지 않으므로 템플릿을 사용하는 다른 가상 컴퓨터를 만들 때 구성해야 합니다.

가상 컴퓨터를 캡처하고 나면 가상 컴퓨터를 만들 때 **내 이미지**에서 확인할 수 있습니다. 이미지 파일은 저장소 계정에 VHD로 저장됩니다. 이미지에 대한 자세한 내용은 [디스크 및 이미지 관리][디스크 및 이미지 관리]를 참조하십시오.

## 시작하기 전에

이러한 단계는 이미 Azure 가상 컴퓨터를 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. 아직 완료하지 못한 경우 다음 지침을 참조하십시오.

-   [사용자 지정 가상 컴퓨터를 만드는 방법][사용자 지정 가상 컴퓨터를 만드는 방법]
-   [가상 컴퓨터에 데이터 디스크를 연결하는 방법][가상 컴퓨터에 데이터 디스크를 연결하는 방법]

## 가상 컴퓨터 캡처

1.  명령 모음에서 **연결**을 클릭하여 가상 컴퓨터에 연결합니다. 자세한 내용은 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법][Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]을 참조하십시오.

2.  SSH 창에서 다음 명령을 입력하고 가상 컴퓨터에서 만든 계정의 암호를 입력합니다. `waagent`의 출력은 이 유틸리티의 버전에 따라 약간씩 다를 수 있습니다.

    `sudo waagent -deprovision`

    ![가상 컴퓨터 프로비전 해제][가상 컴퓨터 프로비전 해제]

3.  계속하려면 **y**를 입력합니다.

    ![가상 컴퓨터 프로비전 해제 성공][가상 컴퓨터 프로비전 해제 성공]

4.  **Exit**를 입력하여 SSH 클라이언트를 닫습니다.

5.  [관리 포털][관리 포털]에서 가상 컴퓨터를 선택하고 **종료**를 클릭합니다.

6.  가상 컴퓨터가 중지되면 명령 모음에서 **캡처**를 클릭하여 **가상 컴퓨터 캡처** 대화 상자를 엽니다.

7.  **이미지 이름**에 새 이미지의 이름을 입력합니다.

8.  모든 Linux 이미지는 `-deprovision` 옵션으로 `waagent` 명령을 실행하여 *프로비전이 해제*되어야 합니다. **I have run waagent-deprovision on the virtual machine**을 클릭하여 운영 체제가 이미지로 캡처될 준비가 되었음을 나타냅니다.

9.  확인 표시를 클릭하여 이미지를 캡처합니다.

    이제 **이미지**에서 새 이미지를 사용할 수 있습니다. 가상 컴퓨터는 이미지가 캡처된 후 삭제됩니다.

    ![이미지 캡처 성공][이미지 캡처 성공]

## 다음 단계

가상 컴퓨터를 만들기 위한 템플릿으로 사용할 이미지가 마련됩니다. 이렇게 하려면 **갤러리에서** 방법을 사용하여 사용자 지정 가상 컴퓨터를 만들고 위에서 만든 이미지를 선택합니다. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법][사용자 지정 가상 컴퓨터를 만드는 방법](영문)을 참조하십시오.

  [디스크 및 이미지 관리]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [사용자 지정 가상 컴퓨터를 만드는 방법]: ../virtual-machines-create-custom/
  [가상 컴퓨터에 데이터 디스크를 연결하는 방법]: ../storage-windows-attach-disk/
  [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]: ../virtual-machines-linux-how-to-log-on
  [가상 컴퓨터 프로비전 해제]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision.png
  [가상 컴퓨터 프로비전 해제 성공]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png
  [관리 포털]: http://manage.windowsazure.com
  [이미지 캡처 성공]: ./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png
