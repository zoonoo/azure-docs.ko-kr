Azure 포털을 사용하여 VNet을 만들려면 다음 단계를 수행합니다. 스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다.

가상 네트워크 작업에 대한 자세한 내용은 [가상 네트워크 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.

1. 브라우저에서 [Azure 포털](http://portal.azure.com)로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.

2. **새로 만들기** **>** **네트워킹** **>** **가상 네트워크**를 클릭합니다.

	![VNetBlade](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. 가상 네트워크 블레이드의 아래쪽 근처의 **배포 모델 선택** 목록에서 **리소스 관리자**를 선택한 다음 **만들기**를 클릭합니다.


	![리소스 관리자 선택](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. **가상 네트워크 만들기** 블레이드에서 VNet 설정을 구성합니다. 이 블레이드에서 첫 번째 주소 공간과 단일 서브넷 주소 범위를 추가합니다. VNet 만들기를 완료한 후에 다시 돌아와서 추가 서브넷 및 주소 공간을 추가합니다. 이 포털의 현재 제한 사항입니다. 포털에서 VNet 속성을 편집하거나 PowerShell을 사용하여 이러한 값을 업데이트하기 위해 언제든 돌아올 수 있습니다. 사용하는 값은 만들려는 구성에 따라 달라집니다. 계획된 구성 값을 참조해야 합니다.

	![가상 네트워크 만들기 블레이드](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. **구독**이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.

6. **리소스 그룹**을 클릭하고 기존 리소스 그룹을 선택하거나 새 리소스 그룹에 대한 이름을 입력하여 새로 만듭니다. 새 그룹을 만드는 경우 계획된 구성 값에 따라 리소스 그룹의 이름을 지정합니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md/#resource-groups)를 참조하세요.

7. 다음으로 VNet에 대한 **위치** 설정을 선택합니다. 이 위치는 VNet에 배포하는 리소스가 배치될 곳을 결정합니다. 리소스를 다시 배포하지 않고 이 작업을 나중에 변경할 수 없습니다.

8. 대시보드에서 VNet을 쉽게 찾을 수 있으려면 **대시보드에 고정**을 선택한 다음 **만들기**를 클릭합니다.
	
	![대시보드에 고정](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. 만들기를 클릭한 후에 VNet의 진행 상황을 반영하는 대시보드에 타일이 표시됩니다. 타일은 VNet 생성될 때 변경됩니다.

	![가상 네트워크 만들기 타일](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)

<!---HONumber=AcomDC_0406_2016-->