<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] 요구 사항 및 문제 해결 팁에 대해서는 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결][RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](영문)을 참조하세요.

1.  아직 로그인하지 않은 경우 [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

3.  명령 모음에서 **연결**을 클릭합니다.

    ![가상 컴퓨터에 로그온][가상 컴퓨터에 로그온]

4.  **열기**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

5.  **연결**을 클릭하여 연결 프로세스를 진행합니다.

    ![연결 계속][연결 계속]

6.  가상 컴퓨터에서 관리자 계정의 사용자 이름 및 암호를 입력한 후 **확인**을 클릭합니다.

7.  **예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

    ![컴퓨터의 ID 확인][컴퓨터의 ID 확인]

    이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

  [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure 관리 포털]: http://manage.windowsazure.com
  [가상 컴퓨터에 로그온]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [연결 계속]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [컴퓨터의 ID 확인]: ./media/virtual-machines-log-on-win-server/connectverify.png
