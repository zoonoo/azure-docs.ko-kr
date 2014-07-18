<properties  linkid="manage-services-cross-premises-connectivity" urlDisplayName="Cross-premises Connectivity" pageTitle="Create a cross-premises virtual network - Azure" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="" solutions="" manager="" editor="" />

<h1>사이트 간의 프레미스 간 연결용 가상 네트워크 만들기</h1>


이 자습서에서는 프레미스 간 가상 네트워크를 만드는 단계를 안내합니다. 여기서 만들 연결의 유형은 사이트 간 연결입니다. 인증서 및 VPN 클라이언트를 사용하여 지점 및 사이트 간 VPN을 만들려는 경우 [관리 포털 마법사를 사용하여 지점 및 사이트 간 VPN 구성][1]을 참조하십시오.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서는 사이트 간 가상 네트워크를 만드는 데 필요한 단계에 익숙해지도록 돕기 위한 것입니다. 가상 네트워크에 대한 디자인 시나리오 및 고급 정보를 원하는 경우에는 [Azure 가상 네트워크 개요][2]를 참조하십시오.

이 자습서를 완료하면 Azure 서비스 및 가상 컴퓨터를 배포할 수 있는 가상 네트워크가 만들어지게 됩니다. 이 가상 네트워크는 회사의 네트워크와 직접 통신할 수 있습니다.

가상 컴퓨터 추가 및 Azure 가상 네트워크로 온-프레미스 Active Directory 확장에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [사용자 지정 방식으로 가상 컴퓨터를 만드는 방법][3]

* [Azure 가상 네트워크에서 복제 Active Directory 도메인 컨트롤러 설치][4]

Azure 가상 컴퓨터에 AD DS를 배포하는 방법에 대한 지침은 [Azure 가상 컴퓨터에서의 Windows Server Active Directory 배포 지침][5]을 참조하십시오.

추가 가상 네트워크 구성 절차 및 설정은 [Azure 가상 네트워크 구성 작업][6]을 참조하십시오.

## 목표

이 자습서에서는 다음에 대해 알아봅니다.

* Azure 서비스를 추가할 수 있는 기본 Azure 가상 네트워크를 설정하는 방법

* 회사 네트워크와 통신하도록 가상 네트워크를 구성하는 방법

## 필수 구성 요소

* 진행 중인 유효한 구독이 하나 이상 있는 Windows Live 계정

* 가상 네트워크 및 서브넷에 사용할 주소 공간(CIDR 표기법)

* DNS 서버의 이름 및 IP 주소(이름 확인에 온-프레미스 DNS 서버를 사용하려는 경우)

* 공개 IPv4 주소를 사용하는 VPN 장치. 마법사를 완료하려면 IP 주소가 필요합니다. VPN 장치는 NAT 뒤에 배치될 수 없으며 최소 장치 표준을 충족해야 합니다. 자세한 내용은 [가상 네트워크의 VPN 장치 정보][7]를 참조하십시오.
  
  참고: VPN 솔루션의 일부로 RRAS를 사용할 수 있습니다. 그러나 이 자습서에서는 RRAS 구성 단계를 안내하지 않습니다.
  
  RRAS 구성 정보에 대해서는 [라우팅 및 원격 액세스 서비스 템플릿][8]을 참조하십시오.

* 라우터 구성에 대한 경험 또는 이 단계에 도움이 될 수 있는 다른 사람

* 로컬 네트워크(온-프레미스 네트워크)의 주소 공간

## 대략적인 단계

1.  [가상 네트워크 만들기](#CreateVN)

2.  [게이트웨이 시작 및 네트워크 관리자에 대한 정보 수집](#StartGateway)

3.  [VPN 장치 구성](#ConfigVPN)

## <a name="CreateVN">가상 네트워크 만들기</a>

**회사 네트워크에 연결되는 가상 네트워크를 만들려면**

1.  [Azure 관리 포털][9]에 로그인합니다.

2.  화면의 왼쪽 아래에서 **새로 만들기**를 클릭합니다. 탐색 창에서 **네트워크**를 클릭한 다음 **가상 네트워크**를 클릭합니다. **사용자 지정 만들기**를 클릭하여 구성 마법사를 시작합니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.  **가상 네트워크 세부 정보** 페이지에서 다음 정보를 입력한 후 오른쪽 아래에서 다음 화살표를 클릭합니다. 정보 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보][10]의 **가상 네트워크 정보** 섹션을 참조하십시오.

* **이름:**가상 네트워크의 이름. *YourVirtualNetwork*를 입력합니다.

* **선호도 그룹:** 드롭다운 목록에서 **새 선호도 그룹 만들기**를 선택합니다. 선호도 그룹을 사용하면 성능 향상을 위해 동일한 데이터 센터에서 Azure 서비스를 물리적으로 그룹화할 수 있습니다. 하나의 선호도 그룹에는 하나의 가상 네트워크만 할당할 수 있습니다.

* **지역:** 드롭다운 목록에서 원하는 지역을 선택합니다. 지정한 지역에 있는 데이터 센터에 가상 네트워크가 만들어집니다.

* **선호도 그룹 이름:** 새 선호도 그룹의 이름을 지정합니다. *YourAffinityGroup*을 입력합니다.
  
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

1.  **DNS 서버 및 VPN 연결** 페이지에서 다음 정보를 입력한 후 화면 아래의 오른쪽에 있는 정방향 화살표를 클릭합니다.
    
	<div class="dev-callout"> 
	<b>참고</B>
    <P>이 페이지에서 **지점 및 사이트 간**과 **사이트 간** 구성을 동시에 선택할 수는 없습니다. 이 자습서에서는 **사이트 간**만 구성하도록 선택합니다. 이 페이지의 설정에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkID=248092">관리 포털의 가상 네트워크 구성 정보</a> 의 **DNS 서버 및 VPN 연결** 페이지를 참조하십시오.</p> 
	</div>


* **DNS 서버:** 이름 확인에 사용할 DNS 서버 이름 및 IP 주소를 입력합니다. 일반적으로 온-프레미스 이름 확인에
  사용하는 DNS 서버입니다. 이 설정은 DNS 서버를 만들지 않습니다. 이름으로 *YourDNS*를 입력하고 IP 주소로
  *10.1.0.4*를 입력합니다.
* **지점 및 사이트 간 VPN 구성:** 이 필드는 공백으로 둡니다.
* **사이트 간 VPN 구성:** 확인란을 선택합니다.
* **로컬 네트워크:** 드롭다운 목록에서 **새 로컬 네트워크 지정**을 선택합니다.
  
     ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

1.  **사이트 간 연결** 페이지에서 아래 정보를 입력한 후 페이지 아래의 오른쪽에 있는 확인 표시를 클릭합니다. 이 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보][10]의 **사이트 간 연결** 페이지 섹션을 참조하십시오.

* **이름:** *YourCorpHQ*를 입력합니다.

* **VPN 장치 IP 주소:** VPN 장치의 공개 IP 주소를 입력합니다. 이 정보가 없는 경우 마법사의 다음 단계를 진행하기 전에 이 정보를 가져와야 합니다. VPN 장치는 NAT 뒤에 배치될 수 없습니다. VPN 장치에 대한 자세한 내용은 [가상 네트워크의 VPN 장치 정보][11]를 참조하십시오.

* **주소 공간:** *10.1.0.0/16*을 입력합니다.
* **주소 공간 추가:** 이 자습서에서는 추가 주소 공간이 필요하지 않습니다.
  
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

1.  **가상 네트워크 주소 공간** 페이지에서 아래 정보를 입력한 후 페이지 아래의 오른쪽에 있는 확인 표시를 클릭하여 네트워크를 구성합니다.
    
    주소 공간은 CIDR 표기법 10.0.0.0/8, 172.16.0.0/12 또는 192.168.0.0/16(RFC 1918에서 지정하는 대로)에 지정된 개인 주소 범위여야 합니다. 이 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보][10]의 **가상 네트워크 주소 공간** 페이지를 참조하십시오.

* **주소 공간:** 화면 위의 오른쪽 모서리에서 **CIDR**을 클릭한 후 다음을 입력합니다.
  * **시작 IP:** 10.4.0.0
  * **CIDR:** /16

* **서브넷 추가:** 다음을 입력합니다.
  * 시작 IP가 *10.4.2.0/24*인 *FrontEndSubnet*으로 **서브넷-1 이름 변경** 후 **서브넷 추가**를 클릭합니다.
  * 시작 IP가 *10.4.3.0/24*인 *BackEndSubnet*이라는 **서브넷 추가**를 추가합니다.
  * 시작 IP가 *10.4.4.0/24*인 *ADDNSSubnet*이라는 **서브넷 추가**를 추가합니다.
  * 시작 IP가 *10.4.1.0/24*인 **게이트웨이 서브넷 추가**를 추가합니다.
  * 이제 3개의 서브넷과 게이트웨이 서브넷이 만들어졌는지 **Verify**한 후 페이지 아래의 오른쪽에 있는 확인 표시를
    클릭하여 가상 네트워크를 만듭니다.
  
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

1.  확인 표시를 클릭하면 가상 네트워크가 만들어지기 시작합니다. 가상 네트워크가 만들어지면 관리 포털의 네트워크 페이지에 있는
    상태 아래에 Created가 나열됩니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

## <a name="StartGateway">게이트웨이 시작</a>

Azure 가상 네트워크를 만든 후 사이트 간 VPN을 만들려면 다음 절차에 따라 가상 네트워크 게이트웨이를 구성합니다. 이 절차에서는 최소 요구 사항을 충족하는 VPN 장치가 있어야 합니다. VPN 장치 및 장치 구성에 대한 자세한 내용은 [가상 네트워크의 VPN 장치 정보][7]를 참조하십시오.

**게이트웨이를 시작하려면**

1.  가상 네트워크가 만들어지면 **네트워크** 페이지에 가상 네트워크의 상태가 **Created**로 표시됩니다.
    
    **이름** 열에서 **YourVirtualNetwork**를 클릭하여 대시보드를 엽니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.  페이지 위쪽에서 **대시보드**를 클릭합니다. 대시보드 페이지의 아래에 있는 **CREATE GATEWAY**를 클릭합니다. 만들려는 게이트웨이 유형에 대해 **Dynamic Routing** 또는 **Static Routing**을 선택합니다.
    
    사이트 간 연결뿐 아니라 지점 및 사이트 간 연결에도 이 가상 네트워크를 사용하려는 경우 Dynamic Routing을 게이트웨이 유형으로 선택해야 합니다. 게이트웨이를 만들기 전에 VPN 장치에서 만들려는 게이트웨이 유형을 지원하는지 확인합니다. [가상 네트워크의 VPN 장치 정보][7]를 참조하십시오. 시스템에서 게이트웨이 만들 것인지 확인하는 메시지를 표시하면 **예**를 클릭합니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.  게이트웨이 만들기가 시작되면 게이트웨이가 시작되었음을 알려 주는 메시지가 표시됩니다.
    
    게이트웨이를 만드는 데에는 최대 15분이 걸릴 수 있습니다.

4.  게이트웨이가 만들어진 후 VPN 장치를 구성하는 데 사용할 다음 정보를 수집해야 합니다.

* 게이트웨이 IP 주소
* 공유 키
* VPN 장치 구성 스크립트 템플릿
  
    다음 단계에서 이 프로세스를 안내합니다.

1.  게이트웨이 IP 주소 찾기 - 게이트웨이 IP 주소는 가상 네트워크 **대시보드** 페이지에 나와 있습니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

2.  공유 키 수집 - 공유 키는 가상 네트워크 **대시보드** 페이지에 나와 있습니다. 화면 아래에 있는 Manage Key를 클릭한 후 대화 상자에 표시되는 키를 복사합니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

3.  VPN 장치 구성 스크립트 템플릿을 다운로드합니다. 대시보드에서 **Download VPN Device Script**를 클릭합니다.

4.  **Download a VPN Device Configuration Script** 대화 상자에서 회사의 VPN 장치에 대한 공급업체, 플랫폼 및 운영 체제를 선택합니다. 확인 표시 단추를 클릭하고 파일을 저장합니다.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

드롭다운 목록에 VPN 장치가 표시되지 않는 경우 MSDN 라이브러리의 [가상 네트워크의 VPN 장치 정보][7]에서 추가 스크립트 템플릿에 대한 내용을 참조하십시오.

## <a name="ConfigVPN">VPN 장치 구성(네트워크 관리자)</a>

각 VPN 장치가 서로 다르므로 대략적인 절차만 다룹니다. 이 절차는 네트워크 관리자만 수행해야 합니다.

관리 포털 또는 [가상 네트워크의 VPN 장치 정보][12]에서 VPN 구성 스크립트를 가져올 수 있습니다. 이 웹 문서에서는 사용할 라우팅 구성과 호환되는 장치 및 라우팅 유형에 대해서도 설명합니다.

가상 네트워크 게이트웨이 구성에 대한 추가 정보는 [관리 포털의 가상 네트워크 게이트웨이 구성][13]을 참조하고 VPN 장치 설명서를 참조하십시오.

이 절차에서는 다음을 가정합니다.

* VPN 장치를 구성하는 사람은 선택된 장치 구성에 능숙해야 합니다. 가상 네트워크와 호환되는 장치 수 및 각 장치 제품군과   관련된 구성으로 인해 다음 단계에서는 세부적인 수준의 장치 구성을 안내하지 않습니다. 따라서 장치를 구성하는 사람이 장치 및 구성 설정에 익숙해야 합니다.

* 사용할 장치가 가상 네트워크와 호환됩니다. 장치 호환성에 대해서는 [여기][7]를 참조하십시오.

**VPN 장치를 구성하려면**

1.  VPN 구성 스크립트를 수정합니다. 다음을 구성합니다.
    
    a. 보안 정책
    
    b. 들어오는 터널
    
    c. 나가는 터널

2.  수정된 VPN 구성 스크립트를 실행하여 VPN 장치를 구성합니다.

3.  다음 명령 중 하나를 실행하여 연결을 테스트합니다.
    
    <table  border="1">
    <tr>
    <th>-</th>
    
    <th>Cisco ASA</th>
    
    <th>Cisco ISR/ASR</th>
    
    <th>Juniper SSG/ISG</th>
    
    <th>Juniper SRX/J</th>
    
    </tr>
    
    	
    <tr>
    <td><b>기본 모드 SA 확인</b>
    </td>
    
    <td><font  face="courier" size="-1">show crypto isakmp sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show crypto isakmp sa</font>
    </td>
    
    <td><font  face="courier" size="-1">get ike cookie</font>
    </td>
    
    <td><font  face="courier" size="-1">show security ike security-association</font>
    </td>
    
    </tr>
    
    	
    <tr>
    <td><b>빠른 모드 SA 확인</b>
    </td>
    
    <td><font  face="courier" size="-1">show crypto ipsec sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show crypto ipsec sa</font>
    </td>
    
    <td><font  face="courier" size="-1">get sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show security ipsec security-association</font>
    </td>
    
    </tr>
    
    </table>

## 다음 단계

온-프레미스 Active Directory를 방금 만든 가상 네트워크로 확장하려면 다음 자습서를 진행하십시오.

* [사용자 지정 방식으로 가상 컴퓨터를 만드는 방법][3]

* [Azure 가상 네트워크에서 복제 Active Directory 도메인 컨트롤러 설치][4]

구성을 백업하거나 템플릿으로 사용하기 위해 가상 네트워크 설정을 네트워크 구성 파일로 내보내려는 경우 [가상 네트워크 설정을 네트워크 구성 파일로 내보내기][14]를 참조하십시오.

## 참고 항목

* [Azure 가상 네트워크][2]

* [가상 네트워크 FAQ][15]

* [네트워크 구성 파일을 사용하여 가상 네트워크 구성][16]

* [가상 네트워크에 가상 컴퓨터 추가][17]

* [가상 네트워크의 VPN 장치 정보][11]

* [Azure 이름 확인 개요][18]



[1]: http://go.microsoft.com/fwlink/?LinkId=296653
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[3]: http://go.microsoft.com/fwlink/?LinkID=294356
[4]: http://go.microsoft.com/fwlink/?LinkId=299877
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx
[6]: http://go.microsoft.com/fwlink/?LinkId=296652
[7]: http://go.microsoft.com/fwlink/?LinkID=248098
[8]: http://msdn.microsoft.com/library/windowsazure/dn133801.aspx
[9]: http://manage.windowsazure.com/
[10]: http://go.microsoft.com/fwlink/?LinkID=248092
[11]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx
[12]: http://go.microsoft.com/fwlink/?LinkId=248098
[13]: http://go.microsoft.com/fwlink/?LinkId=299878
[14]: http://go.microsoft.com/fwlink/?LinkID=299880
[15]: http://msdn.microsoft.com/library/windowsazure/dn133803.aspx
[16]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx
[17]: http://www.windowsazure.com/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/
[18]: http://go.microsoft.com/fwlink/?LinkId=248097