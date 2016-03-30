## Azure 포털에서 VNet을 만드는 방법

Azure Preview 포털을 사용하여 위의 시나리오에 따라 VNet을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 아래 그림과 같이 **새로 만들기** > **네트워킹** > **가상 네트워크**를 클릭하고 **배포 모델 선택** 목록에서 **리소스 관리자**를 클릭한 후 **만들기**를 클릭합니다.

	![Azure 포털에서 VNet 만들기](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. 아래 그림과 같이 **가상 네트워크 만들기** 블레이드에서 VNet 설정을 구성합니다.

	![가상 네트워크 만들기 블레이드](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. **리소스 그룹**을 클릭하고 VNet을 추가할 리소스 그룹을 클릭하거나 **새로 만들기**를 클릭하여 새 리소스 그룹에 VNet을 추가합니다. 아래 그림은 **TestRG**라는 새 리소스 그룹에 대한 리소스 그룹 설정을 보여 줍니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../articles/resource-group-overview.md/#resource-groups)를 참조하세요.

	![리소스 그룹](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 필요한 경우 VNet에 대한 **구독** 및 **위치** 설정을 변경합니다.

6. VNet을 **시작 보드**에 타일로 표시하지 않으려면 **시작 보드에 고정**을 사용하지 않도록 설정합니다.

7. **만들기**를 클릭하고 아래 그림과 같이 **가상 네트워크를 만드는 중** 타일이 표시되는지 확인합니다.

	![가상 네트워크 만들기 타일](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. VNet이 만들어질 때까지 기다린 후 아래와 같이 **가상 네트워크** 블레이드에서 **모든 설정** > **서브넷** > **추가**를 클릭합니다.

	![Azure 포털에서 서브넷 추가](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. 아래와 같이 *BackEnd* 서브넷에 대한 서브넷 설정을 지정한 후 **확인**을 클릭합니다.

	![서브넷 설정](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. 아래 그림과 같이 서브넷 목록을 확인합니다.

	![VNet의 서브넷 목록](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0323_2016-->