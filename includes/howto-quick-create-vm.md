<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# 가상 컴퓨터를 빨리 만드는 방법

관리 포털에서 가상 컴퓨터를 빨리 만들려면 **Quick Create** 방법을 사용합니다. 이 컴퓨터를 만드는 경우 하나의 대화 상자를 사용하여 구성 세부 정보를 제공합니다.

**참고**: 이 문서에서는 가상 네트워크에 연결되지 않은 가상 컴퓨터를 만듭니다. 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 **From Gallery** 방법을 대신 사용하고 가상 컴퓨터를 만들 때 가상 네트워크를 지정합니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요][]를 참조하세요.

1.  [Azure 관리 포털][]에 로그인합니다.

2.  명령 모음에서 **새로 만들기**를 클릭합니다.

    ![새 가상 컴퓨터 만들기][]

3.  **Virtual Machines**를 클릭한 후 **Quick Create**를 클릭합니다.

    ![새 가상 컴퓨터 빨리 만들기][]

    **Create a New Virtual Machine** 대화 상자가 나타납니다.

4.  새 가상 컴퓨터에 대한 다음 정보를 입력합니다.

    -   **DNS Name** - 만든 가상 컴퓨터와 가상 컴퓨터가 포함된 클라우드 서비스 둘 다에 사용되는 이름입니다.
    -   **Image** - 가상 컴퓨터를 만드는 데 사용되는 플랫폼 이미지입니다.
    -   **User Name** - 가상 컴퓨터를 관리하는 데 사용할 계정 이름입니다.
    -   **Account Password** - 계정에 대한 강력한 암호를 입력하고 확인합니다.
    -   **Location** - 가상 컴퓨터가 포함된 영역입니다.

5.  확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.

    **참고:** 이 가상 컴퓨터를 포함할 저장소 계정이 자동으로 만들어집니다.

    새 가상 컴퓨터가 **Virtual Machines** 페이지에 표시됩니다.

    ![가상 컴퓨터 만들기 성공][]

  [Azure 가상 네트워크 개요]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Azure 관리 포털]: http://manage.windowsazure.com
  [새 가상 컴퓨터 만들기]: ./media/howto-quick-create-vm/create.png
  [새 가상 컴퓨터 빨리 만들기]: ./media/howto-quick-create-vm/createquick.png
  [가상 컴퓨터 만들기 성공]: ./media/howto-quick-create-vm/vmsuccesswindows.png
