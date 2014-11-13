<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기" metaKeywords="Azure image gallery vm" description="Azure 미리 보기 포털의 VM 갤러리를 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터)를 만드는 방법에 대해 알아봅니다." metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep,kathydav,rasquill" />

# Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/virtual-machines-windows-tutorial/" title="Azure 포털">Azure 포털</a><a href="/ko-kr/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure 미리 보기 포털" class="current">Azure 미리 보기 포털</a></div>

이 자습서에서는 Azure 미리 보기 포털의 이미지 갤러리에서 Windows Server 이미지를 예로 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터)를 쉽게 만드는 방법을 보여 줍니다. 이 갤러리는 Windows 운영 체제, Linux 기반 운영 체제, 응용 프로그램 이미지 등 다양한 이미지를 제공합니다.

> [WACOM.NOTE] 이전에 Azure VM을 사용한 경험이 없어도 이 자습서를 완료할 수 있습니다. 그러나 Azure 계정은 필요합니다. 몇 분 만에 무료 체험 계정을 만들 수 있습니다. 자세한 내용은 [Azure 계정 만들기][Azure 계정 만들기](영문)를 참조하세요.

이 자습서에서는 다음을 보여 줍니다.

-   [가상 컴퓨터를 만드는 방법][가상 컴퓨터를 만드는 방법]
-   [가상 컴퓨터를 만든 후 로그온하는 방법][가상 컴퓨터를 만든 후 로그온하는 방법]

더 자세히 알고 싶으면 [가상 컴퓨터][가상 컴퓨터]를 참조하세요.

## <span id="createvirtualmachine"></span> </a>가상 컴퓨터를 만드는 방법

이 섹션에서는 미리 보기 포털을 통해 Windows Server를 예로 사용하여 VM을 만드는 방법을 보여 줍니다. 대부분의 구성에서 Azure 기본 설정을 사용할 수 있고 단 몇 분 만에 VM을 만들 수 있습니다.

> [WACOM.NOTE] 갤러리에서 사용 가능한 이미지의 수와 종류는 보유 중인 구독의 유형에 따라 달라집니다. 이 자습서에서는 Windows Server 이미지를 사용하지만 MSDN 구독에는 데스크톱 이미지와 같은 추가 이미지가 제공될 수도 있습니다.

1.  [Azure 미리 보기 포털][1]에 로그인합니다. 아직 구독이 없으면 [무료 평가판][무료 평가판] 서비스를 확인하세요.

2.  허브 메뉴에서 **새로 만들기**를 클릭합니다.

    ![명령 모음에서 새로 만들기 선택][명령 모음에서 새로 만들기 선택]

3.  **새로 만들기**에서 **모두**를 클릭하고 **갤러리**에서 **가상 컴퓨터**를 클릭합니다. **Windows Server 2012 R2 Datacenter**를 클릭합니다. 여기에서 **만들기**를 클릭합니다.

    ![갤러리에서 VM 이미지 선택][갤러리에서 VM 이미지 선택]

4.  **VM 만들기** 블레이드에서 VM에 대해 원하는 **호스트 이름**, 관리 **사용자 이름** 및 강력한 **암호**를 입력합니다.

    ![호스트 이름 및 로그온 자격 증명 구성][호스트 이름 및 로그온 자격 증명 구성]

    > [WACOM.NOTE] **사용자 이름**은 서버를 관리하는 데 사용하는 관리 계정을 나타냅니다. 이 계정의 고유한 암호를 만들고 기억해 둡니다. **가상 컴퓨터에 로그온하는 데 이 사용자 이름과 암호가 필요합니다**.

5.  나머지 VM 옵션에 대해 기본 설정을 사용하고 VM을 만들기 시작하려면 **만들기**를 클릭합니다. 그렇지만 원할 경우 **만들기**를 클릭하기 전에 **옵션 구성** 설정을 확인할 수 있습니다. 예를 들어 나중에 VM에서 여러 메트릭을 추적하도록 선택적 진단을 구성할 수 있습니다. **옵션 구성**, **진단**을 차례로 클릭하고 **상태**를 **켜기**로 설정합니다.

    ![VM 진단 켜기][VM 진단 켜기]

    > [WACOM.NOTE] Azure 진단을 켜면 Azure는 진단 데이터를 Azure 저장소 계정에 저장하므로 추가 저장소 비용이 발생합니다.

6.  Azure가 VM을 만드는 동안 허브 메뉴의 **알림**에서 진행 상태를 추적할 수 있습니다. Azure가 VM을 만든 후에는 시작 보드에 해당 VM이 표시됩니다.

    ![VM이 시작 보드에 나타남][VM이 시작 보드에 나타남]

## <span id="logon"></span> </a>가상 컴퓨터를 만든 후 로그온하는 방법

이 섹션에서는 VM에서 실행되는 설정 및 응용 프로그램을 관리할 수 있도록 VM에 로그온하는 방법을 보여 줍니다.

> [WACOM.NOTE] 요구 사항 및 문제 해결 팁에 대해서는 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결][RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](영문)을 참조하세요.

1.  아직 로그인하지 않은 경우 [Azure 미리 보기 포털][1]에 로그인합니다.

2.  시작 보드에서 VM을 클릭합니다. 해당 VM을 찾아야 할 경우 **찾아보기**, **가상 컴퓨터**를 차례로 클릭합니다.

    ![VM 찾아보기][VM 찾아보기]

3.  VM 블레이드 맨 위에 있는 **연결**을 클릭합니다.

    ![가상 컴퓨터에 로그온][가상 컴퓨터에 로그온]

4.  **열기**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

5.  **연결**을 클릭하여 연결 프로세스를 진행합니다.

    ![연결 계속][연결 계속]

6.  가상 컴퓨터에서 관리자 계정의 사용자 이름 및 암호를 입력한 후 **확인**을 클릭합니다.

7.  **예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

    ![컴퓨터의 ID 확인][컴퓨터의 ID 확인]

    이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

## 다음 단계

Azure에서 Windows 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

[클라우드 서비스에서 가상 컴퓨터를 연결하는 방법][클라우드 서비스에서 가상 컴퓨터를 연결하는 방법]

[Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법][Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법]

[가상 컴퓨터에 데이터 디스크 연결][가상 컴퓨터에 데이터 디스크 연결]

[가상 컴퓨터의 가용성 관리][가상 컴퓨터의 가용성 관리]

[Azure VM 구성 설정 정보][Azure VM 구성 설정 정보]

  [Azure 미리 보기 포털]: /ko-kr/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure 미리 보기 포털"
  [Azure 계정 만들기]: http://www.windowsazure.com/ko-kr/develop/php/tutorials/create-a-windows-azure-account/
  [가상 컴퓨터를 만드는 방법]: #createvirtualmachine
  [가상 컴퓨터를 만든 후 로그온하는 방법]: #logon
  [가상 컴퓨터]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/
  [명령 모음에서 새로 만들기 선택]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [갤러리에서 VM 이미지 선택]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [호스트 이름 및 로그온 자격 증명 구성]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [VM 진단 켜기]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [VM이 시작 보드에 나타남]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [VM 찾아보기]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [가상 컴퓨터에 로그온]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [연결 계속]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [컴퓨터의 ID 확인]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [클라우드 서비스에서 가상 컴퓨터를 연결하는 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/cloud-services-connect-virtual-machine/
  [Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [가상 컴퓨터에 데이터 디스크 연결]: http://www.windowsazure.com/ko-kr/documentation/articles/storage-windows-attach-disk/
  [가상 컴퓨터의 가용성 관리]: http://www.windowsazure.com/ko-kr/documentation/articles/manage-availability-virtual-machines/
  [Azure VM 구성 설정 정보]: http://msdn.microsoft.com/library/azure/dn763935.aspx
