## Azure 포털에서 VNet 피어링를 만드는 방법
Azure 포털을 사용하여 위의 시나리오에 따라 VNet 피어링을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. VNET 피어링을 설정하려면 두 개의 VNet 간에 각 방향에 하나씩 두 개의 링크를 만들어야 합니다. 먼저 VNET1-VNet2에 VNET 피어링 링크를 만들 수 있습니다. 포털에서 **찾아보기**를 클릭하고 **가상 네트워크**를 선택합니다.
   
    ![Azure 포털에서 VNet 피어링 만들기](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)
3. 가상 네트워크 블레이드에서 VNET1을 선택하고 피어링을 클릭한 다음 추가를 클릭합니다.
   
    ![피어링 선택](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)
4. 피어링 추가 블레이드에서 피어링 링크 이름을 LinkToVnet2로 지정하고 구독 및 피어링 가상 네트워크 VNET2를 선택한 다음 확인을 클릭합니다.
   
    ![VNet에 링크](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)
5. 일단 이 VNET 피어링 링크를 만듭니다. 다음과 같은 링크 상태를 확인할 수 있습니다.
   
    ![링크 상태](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)
6. 다음으로 VNET2-VNet1에 VNET 피어링 링크를 만듭니다. 가상 네트워크 블레이드에서 VNET2를 선택하고 피어링을 클릭한 다음 추가를 클릭합니다.
   
    ![다른 VNet에서 피어링](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)
7. 피어링 추가 블레이드에서 피어링 링크 이름을 LinkToVnet1으로 지정하고 구독 및 피어링 가상 네트워크를 선택한 다음 확인을 클릭합니다.
   
    ![가상 네트워크 만들기 타일](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)
8. 일단 이 VNET 피어링 링크를 만듭니다. 다음과 같은 링크 상태를 확인할 수 있습니다.
   
    ![마지막 링크 상태](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)
9. LinkToVnet2에 대한 상태를 확인하면 이제 연결됨으로 변경되었습니다.
   
    ![마지막 링크 상태 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)
10. 참고: 링크를 모두 연결해야만 VNET 피어링이 설정됩니다.

<!---HONumber=AcomDC_0803_2016-->