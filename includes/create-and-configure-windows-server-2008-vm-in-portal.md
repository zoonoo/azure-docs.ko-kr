<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**참고**: 이 문서에서는 가상 네트워크에 연결되지 않은 가상 컴퓨터를 만듭니다. 가상 컴퓨터에서 가상 네트워크를 사용하여
호스트 이름으로 가상 컴퓨터에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 **From Gallery**
방법을 대신 사용하고, 가상 컴퓨터를 만들 때 가상 네트워크를 지정합니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상
네트워크 개요][1]를 참조하십시오.

가상 컴퓨터를 만들려면 다음 단계를 따르십시오.

1.  Azure 계정을 사용하여 [Azure 관리 포털][2]에 로그인합니다.

2.  관리 포털에서 웹 페이지 왼쪽 아래에 있는 **+New**를 클릭한 후 **Virtual Machine**, **From
    Gallery**를 차례로 클릭합니다. ![새 가상 컴퓨터
    만들기](./media/create-and-configure-windows-server-2008-vm-in-portal/CreateWinVM.png)

3.  Windows Server 2008 R2 SP1 가상 컴퓨터 이미지를 선택하고 페이지 오른쪽 아래에 있는 다음 화살표를
    클릭합니다.

4.  **Virtual machine configuration** 페이지에서 다음 정보를 제공합니다.

* **Virtual Machine Name**(예: "testwinvm")을 제공합니다.
* **New User Name** 상자에 "Administrator"를 입력합니다.
* **New Password** 상자에 [강력한 암호][3]를 입력합니다.
* **Confirm Password** 상자에 암호를 다시 입력합니다.
* 드롭다운 목록에서 해당 **Size**를 선택합니다.
  
  다음 화살표를 클릭하여 계속합니다.

1.  **Virtual machine mode** 페이지에서 다음 정보를 제공합니다.

* **Standalone Virtual Machine**을 선택합니다.
* **DNS Name** 상자에 유효한 하위 도메인을 **testwinvm.cloudapp.net** 형식으로 입력합니다.
* **Region/Affinity Group/Virtual Network** 상자에서 이 가상 이미지를 호스트할 영역을
  선택합니다.
  
  다음 화살표를 클릭하여 계속합니다.

1.  **Virtual machine options** 페이지의 **Availability Set** 상자에서
    **(none)**을 선택합니다. 확인 표시를 클릭하여 계속합니다.

2.  Azure에서 가상 컴퓨터를 준비하는 동안 기다립니다.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
