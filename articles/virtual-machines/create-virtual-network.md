<properties 
	pageTitle="자습서: Azure에서 클라우드 전용 가상 네트워크 만들기" 
	description="이 자습서에서는 클라우드 전용 Azure 가상 네트워크 예제를 만드는 방법에 대해 알아봅니다." 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# 자습서: Azure에서 클라우드 전용 가상 네트워크 만들기

이 자습서에서는 Azure 관리 포털에서 두 개의 서브넷이 포함된 예제 클라우드 전용 Azure 가상 네트워크를 만드는 단계를 연습합니다. 결과 가상 네트워크는 다음과 같습니다.

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

예를 들어 FrontEndSubnet은 웹 서버에 사용되고, BackEndSubnet은 SQL 서버 또는 도메인 컨트롤러에 사용될 수 있습니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이는 단계별 예제 구성 작업을 통해 사용자 고유의 가상 네트워크를 만드는 데 필요한 단계에 익숙해지도록 도와주기 위한 것입니다. 특정 구성에 적합한 클라우드 전용 가상 네트워크를 만들려면 [관리 포털에서 클라우드 전용 가상 네트워크 구성](http://msdn.microsoft.com/library/azure/dn631643.aspx)을 참조하세요. 가상 네트워크에 대한 설계 시나리오 및 고급 정보를 찾는다면 [Azure 가상 네트워크 개요](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)를 참조하세요.


> [AZURE.NOTE]가상 네트워크가 조직 네트워크에 연결된 크로스-프레미스 구성을 만드는 단계는 이 자습서에서 다루지 않습니다. 프레미스 간 연결 및 사이트 간 VPN 연결(즉, 회사에 있는 Active Directory 또는 SharePoint에 연결)로 가상 네트워크를 만드는 방법에 대한 자습서는 [자습서: 사이트 간 연결용 프레미스 간 가상 네트워크 만들기](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md)를 참조하세요.


##  목표

이 자습서에서는 두 개의 서브넷이 있는 기본 Azure 클라우드 전용 가상 네트워크를 설정하는 방법에 대해 알아봅니다.

##  필수 조건

*  진행 중인 유효한 Azure 구독이 하나 이상 있는 Microsoft 계정. 아직 Azure 구독이 없는 경우에는 [Azure 평가판 사용](http://azure.microsoft.com/pricing/free-trial/)에서 무료로 가입할 수 있습니다. MSDN 구독이 있는 경우에는 [Microsoft Azure 특별 가격: MSDN, MPN 및 Bizspark 이점](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

##  이 자습서용 가상 네트워크 만들기

이 예제 클라우드 전용 가상 네트워크를 만들려면 다음을 수행합니다.

1. 관리 포털에 로그인합니다.

2. 화면 왼쪽 아래에서 **새로 만들기** > **네트워크 서비스** > **가상 네트워크**를 클릭한 후 **사용자 지정 만들기**를 클릭하여 구성 마법사를 시작합니다.

	![][Image1]

3. **가상 네트워크 정보** 페이지에서 다음 정보를 입력합니다.

- **이름** - **YourVirtualNetwork**를 입력합니다.

- **지역** - 지정한 지역에 있는 데이터 센터에 가상 네트워크가 만들어집니다. 최상의 성능을 유지하려면 드롭다운 목록에서 현재 속한 지역을 선택합니다.
 

	![][Image2]

4. 오른쪽 아래에 있는 다음 화살표를 클릭합니다. 이 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)에서 가상 네트워크 정보 페이지 섹션을 참조하세요.

5. **DNS 서버 및 VPN 연결** 페이지에서 오른쪽 아래에 있는 다음 화살표를 클릭합니다. Azure에서 이 가상 네트워크에 추가된 새 가상 시스템에 인터넷 기반 Azure DNS 서버를 할당합니다. 이를 통해 인터넷 리소스에 액세스할 수 있습니다. 이 페이지를 설정하는 방법에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)에서 DNS 서버 및 VPN 연결 페이지를 참조하세요.
	
6.	실제 네트워크와 마찬가지로 가상 네트워크에는 내부에 둘 가상 시스템에 할당할 IP 주소 범위(주소 공간이라고 함)가 필요합니다. 또한 가상 네트워크는 가상 네트워크 주소 공간에서 파생된 고유한 주소 공간이 필요한 서브넷을 지원합니다. 이 자습서에서는 BackEndSubnet과 FrontEndSubnet을 만듭니다. **가상 네트워크 주소 공간** 페이지에서 다음을 구성합니다.

	- 주소 공간에 대해 **CIDR(주소 수)**에서 **/16(65535)**을 선택합니다.

	- 서브넷의 경우 첫째 행에서 기존 이름 위에 **BackEndSubnet**을 입력하고 **10.0.1.0**을 시작 IP로 입력한 후 **CIDR(주소 수)**에서 **/24(256)**를 선택합니다. **서브넷 추가**를 클릭하고 **FrontEndSubnet**을 이름으로, **10.0.2.0**을 시작 IP로 입력합니다.
		
	![][Image4]

 가상 네트워크 다이어그램으로 돌아가면 다음 주소 공간이 구성되어 있습니다.
 
	
- FrontEndSubnet: 10.0.2.0/24
- BackEndSubnet: 10.0.1.0/24

 이 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)에서 가상 네트워크 주소 공간 페이지를 참조하세요.


7. 페이지의 오른쪽 아래에서 확인 표시를 클릭하면 가상 네트워크 만들기가 시작됩니다. 가상 네트워크가 만들어지면 Azure 관리 포털의 **네트워크** 페이지에 있는 상태 아래에 **생성됨**이 나열됩니다.  

	![][Image5]

다음을 사용하여 Azure 인프라 서비스에 대해 계속 알아볼 수 있습니다.

- [사용자 지정 가상 컴퓨터를 만드는 방법](virtual-machines-create-custom.md) 이 항목을 사용하여 가상 네트워크에 가상 컴퓨터를 설치할 수 있습니다. 가상 컴퓨터 및 설치 옵션에 대한 자세한 내용은 [Azure 가상 컴퓨터](http://azure.microsoft.com/documentation/services/virtual-machines/)를 참조하세요.

- [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](../active-directory-new-forest-virtual-machine.md) - 이 항목을 사용하여 다른 네트워크에 연결하지 않고 새 Windows Server AD(Active Directory) 포리스트를 설치할 수 있습니다. 이 자습서에서는 새 포리스트 설치를 위해 VM(가상 컴퓨터)을 만드는 데 필요한 특정 단계를 설명합니다. 이 자습서를 사용할 경우에는 관리 포털을 사용하여 VM을 만들지 마세요. 자세한 내용은 [Azure 가상 컴퓨터에 Windows Server Active Directory를 배포하기 위한 지침](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)을 참조하세요.

이 가상 네트워크를 제거하려면 가상 네트워크를 선택하고 **삭제**를 클릭한 후 **예**를 클릭합니다.

특정 구성에 적합한 클라우드 전용 가상 네트워크를 만들 준비가 완료되었으면 [관리 포털에서 클라우드 전용 가상 네트워크 구성](http://msdn.microsoft.com/library/azure/dn631643.aspx)을 참조하세요.

가상 네트워크에 대한 설계 시나리오 및 고급 정보를 찾는다면 [Azure 가상 네트워크 개요](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)를 참조하세요.

추가 가상 네트워크 구성 절차 및 설정은 [Azure 가상 네트워크 구성 작업](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409)을 참조하세요.


## 참고 항목

-  [Azure 가상 네트워크 FAQ](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Azure 가상 네트워크 구성 작업](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [네트워크 구성 파일을 사용하여 가상 네트워크 구성](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [VM 및 역할 인스턴스에 대한 이름 확인](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=July15_HO3-->