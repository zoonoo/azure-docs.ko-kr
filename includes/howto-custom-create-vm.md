# <a name="how-to-create-a-custom-virtual-machine"></a>사용자 지정 가상 컴퓨터를 만드는 방법
*사용자 지정* 가상 컴퓨터란 **갤러리에서** 메서드를 사용하여 만드는 가상 컴퓨터를 말하는데, 이는 **빠른 생성** 메서드보다 사용할 수 있는 구성 옵션이 더 많기 때문입니다. 이러한 옵션에는 다음이 포함됩니다.

* 보다 많은 가상 컴퓨터(VM) 제작용 이미지 옵션
* 가상 네트워크에 VM 연결
* 기존 클라우드 서비스에 VM 추가
* 가용성 집합에 VM 추가

> [!IMPORTANT]
> 가상 컴퓨터에서 가상 네트워크를 사용하여 호스트 이름으로 가상 컴퓨터에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만드는 경우에만 가상 네트워크에 가입하도록 가상 컴퓨터를 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.
> 
> 

1. [Azure 포털](http://manage.windowsazure.com)에 로그인합니다.
2. 명령 모음에서 **New**를 클릭합니다.
3. **계산**을 클릭하고 **Virtual Machine**을 클릭한 후 **갤러리에서**를 클릭합니다.
4. 사용하려는 이미지를 클릭한 후 화살표를 클릭하여 계속합니다.
5. 여러 버전의 이미지를 사용할 수 있는 경우 **Version Release Date**에서 사용할 버전을 선택합니다.
6. **Virtual Machine Name**에서 가상 컴퓨터에 사용할 이름을 입력합니다.
7. **계층** 및 **크기**를 사용하여 가상 컴퓨터에 적합한 크기를 선택합니다. 선택하는 크기는 가상 컴퓨터의 최대 구성은 물론 가격에도 영향을 줍니다. 구성 세부 정보를 확인하려면 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://go.microsoft.com/fwlink/p/?LinkID=389844)를 참조하세요.
8. **New User Name**에서 서버 관리에 사용하려는 관리자 계정의 이름을 입력합니다.
9. **New Password**에서 관리자 계정에 대한 강력한 암호를 입력합니다. **Confirm Password**에 동일한 암호를 다시 입력합니다.
10. 화살표를 클릭하여 계속합니다.
11. **Cloud Service**에서 다음 중 하나를 수행합니다.
    
    * 클라우드 서비스의 첫 번째 또는 유일한 가상 컴퓨터인 경우 **Create a New Cloud Service**를 선택합니다. **Cloud Service DNS Name**에 3자에서 24자 사이의 소문자와 숫자를 사용하는 이름을 입력합니다. 이 이름은 클라우드 서비스를 통해 가상 컴퓨터에 연결하는 데 사용되는 URI의 일부가 됩니다.
    * 이 가상 컴퓨터를 클라우드 서비스에 추가하는 경우 목록에서 선택합니다.
    
    > [!NOTE]
    > 동일한 클라우드 서비스에 가상 컴퓨터를 배치하는 방법에 대한 자세한 내용은 [클라우드 서비스에서 가상 컴퓨터를 연결하는 방법](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/)을 참조하세요.
    > 
    > 
12. **Region/Affinity Group/Virtual Network**에서 가상 컴퓨터에 사용할 영역, 선호도 그룹 또는 가상 네트워크를 선택합니다. 선호도 그룹에 대한 자세한 내용은 [가상 네트워크의 선호도 그룹 정보](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md)를 참조하세요.
13. **Storage Account**에서 VHD 파일에 대한 기존 저장소 계정을 선택하거나 자동으로 생성된 저장소 계정을 사용합니다. 영역당 하나의 저장소 계정만 자동으로 만들어집니다. 이 설정으로 만든 다른 모든 가상 컴퓨터는 이 저장소 계정에 있습니다. 20개의 저장소 계정으로 제한됩니다.
14. 가상 컴퓨터를 가용성 집합에 포함하려는 경우 **가용성 집합**에서 **가용성 집합 만들기**를 선택하거나 기존 가용성 집합에 추가합니다.
    
    **참고**: 가용성 집합의 가상 컴퓨터는 다른 장애 도메인에 배포됩니다. 가용성 집합에 여러 가상 컴퓨터를 배치하면 네트워크 오류, 로컬 디스크 하드웨어 오류 및 계획된 가동 중지 시간 중에도 응용 프로그램을 사용할 수 있게 하는 데 도움이 됩니다.
15. **Endpoints**에서 원격 데스크톱 및 SSH(보안 셸) 클라이언트를 통해서 등 가상 컴퓨터에 대한 연결을 허용하기 위해 만들어지는 새 끝점을 검토합니다. 지금 끝점을 추가하거나 나중에 만들 수도 있습니다. 나중에 끝점을 만드는 방법에 대한 지침은 [가상 컴퓨터로 끝점을 설정하는 방법](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.
16. **VM Agent**에서 VM 에이전트를 설치할지 여부를 결정합니다. 이 에이전트는 가상 컴퓨터를 조작하는 데 유용한 확장을 설치하기 위한 환경을 제공합니다. 자세한 내용은 [확장 관리](http://go.microsoft.com/FWLink/p/?LinkID=390493)를 참조하세요.
17. 화살표를 클릭하여 가상 컴퓨터를 만듭니다.
    
    ![사용자 지정 가상 컴퓨터 만들기 성공](./media/howto-custom-create-vm/VMSuccessWindows.png)

## <a name="next-steps"></a>다음 단계
가상 컴퓨터가 만들어진 후에는 자동으로 시작됩니다. 포털에서 상태가 실행 중으로 표시되면 가상 컴퓨터에 로그인할 수 있습니다. 지침을 확인하려면 다음 문서 중 하나를 참조하십시오.

* [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

