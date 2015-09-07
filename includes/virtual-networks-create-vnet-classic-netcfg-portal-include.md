## Azure 포털에서 네트워크 구성 파일을 사용하여 VNet을 만드는 방법

Azure에서는 xml 파일을 사용하여 구독에 사용할 수 있는 모든 VNet을 정의합니다. 이 파일을 다운로드하고, 편집하여 기존 VNet을 수정 또는 삭제하고 새 VNet을 만들 수 있습니다. 이 문서에서는 네트워크 구성(또는 netcgf) 파일이라고 하는 이 파일을 다운로드하고 편집하여 새 VNet을 만드는 방법을 알아봅니다. 네트워크 구성 파일에 대한 자세한 내용은 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.

Azure 포털을 통해 netcfg 파일을 사용하여 VNet을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://manage.windowsazure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 아래와 같이 서비스 목록 아래로 스크롤하여 **네트워크**를 클릭합니다.

	![Azure 가상 네트워크](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. 아래와 같이 페이지 아래쪽에서 **내보내기** 단추를 클릭합니다.

	![내보내기 단추](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. **네트워크 구성 내보내기** 페이지에서 가상 네트워크 구성을 내보낼 구독을 선택하고, 페이지의 왼쪽 아래 구석에 있는 확인 표시 단추를 클릭합니다.
5. 브라우저의 지침에 따라 **NetworkConfig.xml** 파일을 저장합니다. 파일을 저장하는 위치를 적어둡니다.
6. XML 또는 텍스트 편집기 응용 프로그램을 사용하여 위의 2단계에서 저장한 파일을 열고 **<VirtualNetworkSites>** 요소를 찾아봅니다. 이미 만들어진 네트워크가 있으면 각 네트워크는 자체의 **<VirtualNetworkSite>** 요소로 표시됩니다.
7. 이 시나리오에서 설명하는 가상 네트워크를 만들려면 **<VirtualNetworkSites>** 요소 바로 아래에 다음 XML을 추가합니다.

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

8.  네트워크 구성 파일을 저장합니다.
9.  아래 그림과 같이 Azure 포털에서 페이지의 왼쪽 아래 구석에 있는 **새로 만들기**를 클릭하고 **네트워크 서비스**를 클릭한 후 **가상 네트워크**를 클릭하고 **구성 가져오기**를 클릭합니다.

	![구성 가져오기](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  **네트워크 구성 파일 가져오기** 페이지에서 **파일 찾아보기...**를 클릭한 후 위의 8단계에서 파일을 저장한 폴더로 이동한 후 **열기**를 클릭합니다. 웹 페이지는 아래 그림과 비슷합니다. 페이지의 오른쪽 아래 구석에서 화살표 단추를 클릭하여 다음 단계로 이동합니다.

	![네트워크 구성 파일 가져오기 페이지](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   아래 그림과 같이 **네트워크를 빌드하는 중** 페이지에서 새 VNet에 대한 항목을 확인합니다.

	![네트워크를 빌드하는 중 페이지](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   페이지의 오른쪽 아래 구석에 있는 확인 표시 단추를 클릭하여 VNet을 만듭니다. 아래 그림과 같이 잠시 후에 사용 가능한 VNet 목록에 해당 Vnet이 표시됩니다.

	![새 가상 네트워크](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=August15_HO9-->