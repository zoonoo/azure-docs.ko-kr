<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Linux를 실행하는 가상 컴퓨터의 이미지를 캡처하는 방법
=====================================================

이미지 갤러리의 이미지를 사용하여 쉽게 가상 컴퓨터를 만들 수도 있고 고유의 이미지를 캡처하여 사용자 지정 가상 컴퓨터를 만드는 데 사용할 수도 있습니다. 이미지는 가상 컴퓨터를 만드는 데 템플릿으로 사용되는 가상 하드 디스크(.vhd) 파일입니다. 이미지에는 호스트 이름, 사용자 계정 설정 등 구성된 가상 컴퓨터와 같은 특정 설정이 없으므로 템플릿입니다. 동일한 방식으로 설정된 가상 컴퓨터를 여러 개 만들려면 구성된 가상 컴퓨터의 이미지를 캡처하여 해당 이미지를 템플릿으로 사용할 수 있습니다.

1.  [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines-linux-how-to-log-on)에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.

2.  SSH 창에서 다음 명령을 입력하고 가상 컴퓨터에서 만든 계정의 암호를 입력합니다. `waagent`의 출력은 이 유틸리티의 버전에 따라 약간씩 다를 수 있습니다.

    `sudo waagent -deprovision`

    ![가상 컴퓨터 프로비전 해제](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

3.  계속하려면 **y**를 입력합니다.

    ![가상 컴퓨터 프로비전 해제 성공](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4.  **Exit**를 입력하여 SSH 클라이언트를 닫습니다.

5.  [관리 포털](http://manage.windowsazure.com)에서 가상 컴퓨터를 선택하고 **종료**를 클릭합니다.

    ![가상 컴퓨터 종료](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6.  **예**를 클릭하여 실행 중이 아닌 경우에도 계속 가상 컴퓨터에 대해 요금이 청구되는 것을 승인합니다.

7.  가상 컴퓨터가 중지되면 명령 모음에서 **캡처**를 클릭합니다.

    ![가상 컴퓨터의 이미지 캡처](./media/virtual-machines-linux-capture-image/CaptureVM.png)

    **가상 컴퓨터 캡처** 대화 상자가 나타납니다.

    ![캡처 세부 정보 입력](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.  **이미지 이름**에 새 이미지의 이름을 입력합니다.

9.  모든 Linux 이미지는 `-deprovision` 옵션으로 `waagent` 명령을 실행하여 *프로비전이 해제*되어야 합니다. **I have run the de-provision command on the Virtual Machine**을 클릭하여 운영 체제가 이미지로 캡처될 준비가 되었음을 나타냅니다.

10. 확인 표시를 클릭하여 이미지를 캡처합니다.

    이제 **이미지**에서 새 이미지를 사용할 수 있습니다. 가상 컴퓨터는 이미지가 캡처된 후 삭제됩니다.

    ![이미지 캡처 성공](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

    **갤러리에서** 메서드를 사용하여 가상 컴퓨터를 만드는 경우 **Select the virtual machine operating system** 페이지에서 **My Images**를 클릭하여 캡처한 이미지를 사용할 수 있습니다.


