<properties writer="kathydav" editor="tysonn" manager="timlt" /> 

**중요**: 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만드는 경우에만 가상 네트워크에 가입하도록 가상 컴퓨터를 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.


1. Azure 계정을 사용하여 [Azure 관리 포털][AzurePreviewPortal]에 로그인합니다.

2. 관리 포털에서 웹 페이지 왼쪽 아래에 있는 **+새로 만들기**를 클릭한 후 **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.

	![Create a New Virtual Machine][Image1]

3. **SUSE** 그룹에서 OpenSUSE 가상 컴퓨터 이미지를 선택하고 페이지 오른쪽 아래에 있는 앞으로 화살표를 클릭합니다.


4. 첫 번째 **가상 컴퓨터 구성** 페이지에서 설정을 입력하거나 확인합니다.

	- **가상 컴퓨터 이름**을 입력합니다(예: "testlinuxvm").
	- **계층**을 확인하고 **크기**를 선택합니다. 계층에 따라 선택할 수 있는 크기가 결정됩니다.
	- Sudoers 목록 파일에 추가될 **새 사용자 이름**(예: "newuser")을 입력합니다.
	- 사용할 **인증** 유형을 결정합니다. 일반적인 암호 지침은 [강력한 암호](http://msdn.microsoft.com/ko-kr/library/ms161962.aspx)를 참조하세요.


5. 다음  **가상 컴퓨터 구성** 페이지에서 설정을 입력하거나 확인합니다.
	- 기본 **새 클라우드 서비스 만들기**를 사용합니다.
	- **DNS 이름** 상자에 주소 부분으로 사용할 유효한 DNS 이름을 입력합니다(예: "testlinuxvm").
	- **지역/선호도 그룹/가상 네트워크** 상자에서 이 가상 이미지를 호스트할 영역을 선택합니다.

6.	다음 화살표를 클릭하여 작업을 마치고 Azure가 가상 컴퓨터를 준비하고 시작하는 동안 기다립니다.

##가상 컴퓨터에 연결
컴퓨터에서 실행 중인 운영 체제에 따라 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결합니다.

- VM에 연결하는 데 Linux를 사용하고 있으면 SSH를 사용하세요. 명령 프롬프트에서 다음을 실행합니다. 

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
	
	Type the user's password.

- VM에 연결하는 데 Windows를 사용하고 있으면 PuTTY를 사용하세요. [PuTTY 다운로드 페이지][PuTTYDownLoad](영문)에서 PuTTY를 다운로드할 수 있습니다. 

	**putty.exe**를 다운로드하고 컴퓨터의 디렉터리에 저장합니다. 명령 프롬프트에서 해당 폴더로 이동한 다음 **putty.exe**를 실행합니다.

	호스트 이름(예: "testlinuxvm.cloudapp.net")을 입력하고 **포트**로 "22"를 입력합니다.

	![PuTTY Screen][Image6]  

##가상 컴퓨터 업데이트(옵션)
1. 가상 컴퓨터에 연결한 후 선택적으로 시스템 업데이트와 패치를 설치할 수 있습니다. 업데이트를 실행하려면 다음을 입력합니다.

	`$ sudo zypper update`

2. **소프트웨어**, **온라인 업데이트**를 차례로 선택하여 사용 가능한 업데이트를 나열합니다. **동의**를 선택하여 설치를 시작하고 사용 가능한 새 패치를 모두 적용합니다(선택적 패치 제외). 

3. 설치가 완료되면 **마침**을 선택합니다.  이제 시스템이 최신 상태입니다.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!--HONumber=35.1-->
