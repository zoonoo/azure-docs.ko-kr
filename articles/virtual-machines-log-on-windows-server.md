<properties urlDisplayName="Log on to a VM" pageTitle="Windows Server를 실행하는 가상 컴퓨터에 로그온" metaKeywords="Azure logging on vm, vm portal" description="Azure 관리 포털을 사용하여 Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법에 대해 알아봅니다." metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav" />

# Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법

Windows Server 운영 체제를 실행하는 가상 컴퓨터의 경우 관리 포털에서 연결 단추를 사용하여 원격 데스크톱 연결을 시작할 수 있습니다.

> [WACOM.NOTE] 사용자 이름 또는 암호를 재설정해야 하거나 가상 컴퓨터에서 RDP를 사용하도록 설정해야 하는 경우 [VMAccess][VMAccess] 확장을 사용하여 이러한 작업을 수행할 수 있습니다. 자세한 요구 사항 및 문제 해결 팁은 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결][RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]을 참조하세요.

1.  아직 로그인하지 않은 경우 [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

3.  명령 모음에서 **연결**을 클릭합니다.

    ![가상 컴퓨터에 로그온][가상 컴퓨터에 로그온]

4.  **열기**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

5.  **연결**을 클릭하여 계속합니다.

    ![연결 계속][연결 계속]

6.  가상 컴퓨터에서 관리자 계정의 사용자 이름 및 암호를 입력한 후 **확인**을 클릭합니다. 이는 가상 컴퓨터를 만들 때 지정한 사용자 이름과 암호입니다. 단, 가상 컴퓨터가 현재 도메인 컨트롤러인 경우는 예외입니다. 이러한 경우에는 도메인에 대한 도메인 관리자 계정의 사용자 이름과 암호를 입력합니다.

7.  **예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

    ![컴퓨터의 ID 확인][컴퓨터의 ID 확인]

    이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure 관리 포털]: http://manage.windowsazure.com
  [가상 컴퓨터에 로그온]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [연결 계속]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [컴퓨터의 ID 확인]: ./media/virtual-machines-log-on-windows-server/connectverify.png
