<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="" solutions="" manager="" editor="" />

Linux를 실행하는 가상 컴퓨터에 로그온하는 방법
==============================================

Linux 운영 체제를 실행하는 가상 컴퓨터의 경우 SSH(보안 셸) 클라이언트를 사용하여 로그온합니다.

가상 컴퓨터에 로그온하는 데 사용할 컴퓨터에 SSH 클라이언트를 설치해야 합니다. 선택할 수 있는 많은 SSH 클라이언트 프로그램이 있습니다. 가능한 선택 항목은 다음과 같습니다.

-   Windows 운영 체제를 실행하는 컴퓨터를 사용하는 경우 PuTTY 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 참조하십시오.
-   Linux 운영 체제를 실행하는 컴퓨터를 사용하는 경우 OpenSSH 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [OpenSSH](http://www.openssh.org/)를 참조하십시오.

다음 절차는 PuTTY 프로그램을 사용하여 가상 컴퓨터에 액세스하는 방법을 보여 줍니다.

1.  [관리 포털](http://manage.windowsazure.com)에서 **호스트 이름** 및 **포트 정보**를 찾습니다. 가상 컴퓨터 대시보드에서 필요한 정보를 찾을 수 있습니다. 가상 컴퓨터 이름을 클릭하고 대시보드의 **간략 상태** 섹션에서 **SSH 세부 정보**를 찾습니다.

    ![SSH 세부 정보 가져오기](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2.  PuTTY 프로그램을 엽니다.

3.  대시보드에서 수집한 호스트 이름 및 포트 정보를 입력하고 **열기**를 클릭합니다.

    ![PuTTY 열기](./media/virtual-machines-linux-how-to-log-on/putty.png)

4.  컴퓨터를 만들 때 지정한 계정을 사용하여 가상 컴퓨터에 로그온합니다.

    ![가상 컴퓨터에 로그온](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

    이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.


