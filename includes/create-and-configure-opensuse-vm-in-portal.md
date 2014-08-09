<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**중요**: 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만드는 경우에만 가상 네트워크에 가입하도록 가상 컴퓨터를 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은  [Azure 가상 네트워크 개요][1]를 참조하십시오.

1.  Azure 계정을 사용하여 [Azure 관리 포털][2]에 로그인합니다.

2.  관리 포털에서 웹 페이지 왼쪽 아래에 있는 **+New**를 클릭한 후 **Virtual Machine**, **From
    Gallery**를 차례로 클릭합니다.
    
    ![새 가상 컴퓨터
    만들기](./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png)

3.  **Platform Images**에서 OpenSUSE 가상 컴퓨터 이미지를 선택하고 페이지 오른쪽 아래에 있는 다음 화살표를 클릭합니다.

4.  **Virtual machine configuration** 페이지에서 다음 정보를 제공합니다.
    
    * **Virtual Machine Name**(예: "testlinuxvm")을 제공합니다.
    * Sudoers 목록 파일에 추가될 **New User Name**(예: "newuser")을 지정합니다.
    * **New Password** 상자에 [강력한 암호][3]를 입력합니다.
    * **Confirm Password** 상자에 암호를 다시 입력합니다.
    * 드롭다운 목록에서 해당 **Size**를 선택합니다.
    
    다음 화살표를 클릭하여 계속합니다.

5.  **Virtual machine mode** 페이지에서 다음 정보를 제공합니다.
    * **Standalone Virtual Machine**을 선택합니다.
    * **DNS Name** 상자에 유효한 DNS 주소를 입력합니다. 예를 들어 "testlinuxvm"입니다.
    * **Region/Affinity Group/Virtual Network** 상자에서 이 가상 이미지를 호스트할 영역을
      선택합니다.
    
   다음 화살표를 클릭하여 계속합니다.

6.  **Virtual machine options** 페이지의 **Availability Set** 상자에서
    **(none)**을 선택합니다. 확인 표시를 클릭하여 계속합니다.

7.  Azure에서 가상 컴퓨터를 준비하는 동안 기다립니다.

## 끝점 구성

가상 컴퓨터가 만들어진 후 원격으로 연결하려면 끝점을 구성해야 합니다.

1.  관리 포털에서 **Virtual Machines**를 클릭한 후 새로운 가상 컴퓨터의 이름과 **Endpoints**를 차례로 클릭합니다.

2.  페이지 맨 아래에 있는 **Edit Endpoint**를 클릭하고 해당 **Public Port**가 22가 되도록 SSH 끝점을 편집합니다.

## 가상 컴퓨터에 연결

가상 컴퓨터가 프로비전되고 끝점이 구성된 경우 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결할 수 있습니다.

### SSH를 사용하여 연결

Linux 컴퓨터를 사용하는 경우 SSH를 사용하여 VM에 연결합니다. 명령 프롬프트에서 다음을 실행합니다.

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

사용자 암호를 입력합니다.

### PuTTY를 사용하여 연결

Windows 컴퓨터를 사용하는 경우 PuTTY를 사용하여 VM에 연결합니다. PuTTY는 [PuTTY 다운로드 페이지][4]에서 다운로드할 수 있습니다.

1.  **putty.exe**를 다운로드하고 컴퓨터의 디렉터리에 저장합니다. 명령 프롬프트에서 해당 폴더로 이동한 다음
    **putty.exe**를 실행합니다.

2.  **Host Name**에 "testlinuxvm.cloudapp.net"을 입력하고 **Port**에 "22"를
    입력합니다. ![PuTTY
    화면](./media/create-and-configure-opensuse-vm-in-portal/putty.png)

## 가상 컴퓨터 업데이트(옵션)

1.  가상 컴퓨터에 연결한 후 선택적으로 시스템 업데이트와 패치를 설치할 수 있습니다. 다음을 실행합니다.
    
    `$ sudo zypper update`

2.  **소프트웨어**를 선택한 후 **온라인 업데이트**를 선택합니다. 업데이트 목록이 표시됩니다. **동의함**을 선택하여 설치를 시작하고 현재 시스템에 사용 가능한 모든 새 패치(선택적 패치 제외)를 적용합니다.

3.  설치가 완료된 후 **마침**을 선택합니다. 이제 시스템이 최신 상태입니다.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/ko-kr/library/ms161962.aspx
[4]: http://www.puttyssh.org/download.html