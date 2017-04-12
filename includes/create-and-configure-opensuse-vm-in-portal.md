1. [Azure 클래식 포털](http://manage.windowsazure.com)에 로그인합니다.  
2. 창 맨 아래의 명령 모음에서 **새로 만들기**를 클릭합니다.
3. **계산**에서 **가상 컴퓨터**를 클릭한 후 **갤러리에서**를 클릭합니다.
   
    ![새 가상 컴퓨터 만들기][Image1]
4. **SUSE** 그룹에서 OpenSUSE 가상 컴퓨터 이미지를 선택하고 화살표를 클릭하여 계속합니다.
5. 첫 번째 **가상 컴퓨터 구성** 페이지에서:
   
   * **가상 컴퓨터 이름**(예: "testlinuxvm")을 입력합니다. 이름은 3~15자를 포함해야 하며, 문자/숫자/하이픈만 포함할 수 있습니다. 또한 문자로 시작해서 문자나 숫자로 끝나야 합니다.
   * **계층**을 확인하고 **크기**를 선택합니다. 계층에 따라 선택할 수 있는 크기가 결정됩니다. 크기는 사용 비용과 구성 옵션(예: 연결할 수 있는 데이터 디스크 수)에 영향을 미칩니다. 자세한 내용은 [가상 컴퓨터의 크기](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
   * **새 사용자 이름**을 입력하거나 기본값 **azureuser**를 적용합니다. 이 이름은 Sudoers 목록 파일에 추가 됩니다.
   * 사용할 **인증** 형식을 결정합니다. 일반적인 암호 지침은 [강력한 암호](http://msdn.microsoft.com/library/ms161962.aspx)를 참조하세요.
6. 다음 **가상 컴퓨터 구성** 페이지에서:
   
   * 기본 **새 클라우드 서비스 만들기**를 사용합니다.
   * **DNS 이름** 상자에 주소의 일부로 사용할 고유한 DNS 이름(예: "testlinuxvm")을 입력합니다.
   * **Region/Affinity Group/Virtual Network** 상자에서 이 가상 이미지를 호스트할 영역을 선택합니다.
   * **끝점**아래에서 SSH 끝점을 유지합니다. 지금 다른 사용자를 추가하거나 가상 컴퓨터를 만든 후 추가, 변경 또는 삭제할 수 있습니다.
     
     > [!NOTE]
     > 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 **반드시** 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만든 후에는 가상 컴퓨터를 가상 네트워크를 추가할 수 없습니다. 자세한 내용은 [가상 네트워크 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.
     > 
     > 
7. 마지막 **가상 컴퓨터 구성** 페이지에서 기본 설정을 유지하고 확인 표시를 클릭하여 완료합니다.

포털에서 **가상 컴퓨터**아래에 새 가상 컴퓨터를 나열합니다. 상태가 **(프로비전 중)**으로 보고되는 동안 가상 컴퓨터가 설정되고 있습니다. 상태가 **실행 중**으로 보고되는 경우 다음 단계로 이동할 수 있습니다.

## <a name="connect-to-the-virtual-machine"></a>가상 컴퓨터에 연결
연결할 컴퓨터의 운영 체제에 따라 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결합니다.

* Linux를 실행하는 컴퓨터에서 SSH를 사용합니다. 명령 프롬프트에 다음을 입력합니다.
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    사용자의 암호를 입력합니다.
* Windows를 실행하는 컴퓨터에서 PuTTY를 사용합니다. 설치하지 않은 경우 [PuTTY 다운로드 페이지][PuTTYDownload]에서 다운로드합니다.
  
    **putty.exe** 를 컴퓨터의 디렉터리에 저장합니다. 명령 프롬프트에서 해당 폴더로 이동한 다음 **putty.exe**를 실행합니다.
  
    호스트 이름(예: "testlinuxvm.cloudapp.net")을 입력하고 **포트**에 대해 "22"를 입력합니다.
  
    ![PuTTY 화면][Image6]  

## <a name="update-the-virtual-machine-optional"></a>가상 컴퓨터 업데이트(옵션)
1. 가상 컴퓨터에 연결한 후 선택적으로 시스템 업데이트와 패치를 설치할 수 있습니다. 업데이트를 실행하려면 다음을 입력합니다.
   
    `$ sudo zypper update`
2. **소프트웨어**, **온라인 업데이트**를 차례로 선택하여 사용 가능한 업데이트를 나열합니다. **동의함** 을 선택하여 설치를 시작하고 사용 가능한 새 패치를 모두 적용합니다(선택적 패치 제외).
3. 설치가 완료되면 **마침**을 선택합니다.  이제 시스템이 최신 상태입니다.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
