Azure 포털을 사용하여 위의 시나리오에 따라 VNet을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.

2. **새로 만들기** > **네트워킹** > **가상 네트워크**를 클릭하고 **배포 모델 선택** 목록에서 **리소스 관리자**를 클릭한 다음 **만들기**를 클릭합니다.

3. 아래 그림과 같이 **가상 네트워크 만들기** 블레이드에서 VNet 설정을 구성합니다.

	![가상 네트워크 만들기 블레이드](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. **리소스 그룹**을 클릭하고 VNet을 추가할 리소스 그룹을 클릭하거나 **새로 만들기**를 클릭하여 새 리소스 그룹에 VNet을 추가합니다. 아래 그림은 **TestRG**라는 새 리소스 그룹에 대한 리소스 그룹 설정을 보여 줍니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md/#resource-groups)를 참조하세요.

	![리소스 그룹](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 필요한 경우 VNet에 대한 **구독** 및 **위치** 설정을 변경합니다.

6. **만들기**를 클릭하고 아래 그림과 같이 **가상 네트워크를 만드는 중** 타일이 표시되는지 확인합니다.

	![가상 네트워크 만들기 타일](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. VNet이 만들어질 때까지 기다린 다음 **가상 네트워크** 블레이드에서 **모든 설정** > **서브넷** > **추가**를 클릭합니다.

8. 아래와 같이 *BackEnd* 서브넷에 대한 서브넷 설정을 지정한 후 **확인**을 클릭합니다.

	![서브넷 설정](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. 서브넷의 목록 보기

	![VNet의 서브넷 목록](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->