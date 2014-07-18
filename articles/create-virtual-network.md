<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Create a virtual network" pageTitle="Create a virtual network - Azure service management" metaKeywords="" description="Learn how to create an Azure Virtual Network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Create a Virtual Network in Azure" authors="" solutions="" manager="" editor="" />

Azure에서 가상 네트워크 만들기
==============================

Azure 관리 포털을 사용하여 기본 Azure 가상 네트워크를 만들려면 이 자습서의 단계를 따르십시오. Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)를 참조하십시오.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 가상 네트워크를 만들기 위해 필요한 단계에 익숙해지는 데 도움이 됩니다. 가상 네트워크에 대한 설계 시나리오 및 고급 정보를 찾는다면 [Azure 가상 네트워크 개요](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)를 참조하십시오.

이 자습서를 완료하면 Azure 서비스 및 가상 컴퓨터를 배포할 수 있는 가상 네트워크가 생깁니다.

**참고**

이 자습서에서는 프레미스 간 구성을 만드는 방법에 대해서는 다루지 않습니다. 사이트 간 프레미스 간 연결(즉, 회사에 있는 Active Directory 또는 SharePoint에 연결)로 가상 네트워크를 만드는 방법에 대한 자습서는 [프레미스 간 연결에 대한 가상 네트워크 만들기](/en-us/manage/services/networking/cross-premises-connectivity/)를 참조하십시오.

추가 가상 네트워크 구성 절차 및 설정은 [Azure 가상 네트워크 구성 작업](http://go.microsoft.com/fwlink/?LinkId=296652)을 참조하십시오.

Azure 가상 컴퓨터에 AD DS를 배포하는 방법에 대한 지침은 [Azure 가상 컴퓨터에서의 Windows Server Active Directory 배포 지침](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx)을 참조하십시오.

목표
----

이 자습서에서는 다음에 대해 알아봅니다.

-   Azure 클라우드 서비스 및 가상 컴퓨터를 추가할 수 있는 기본 Azure 가상 네트워크를 설정하는 방법

필수 구성 요소
--------------

-   진행 중인 유효한 구독이 하나 이상 있는 Windows Live 계정

가상 네트워크 만들기
--------------------

**클라우드 전용 가상 네트워크를 만들려면**

1.  [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.

2.  화면의 왼쪽 아래에서 **새로 만들기**를 클릭합니다. 탐색 창에서 **네트워크**를 클릭한 다음 **가상 네트워크**를 클릭합니다. **사용자 지정 만들기**를 클릭하여 구성 마법사를 시작합니다.

    ![](./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png)

3.  **가상 네트워크 세부 정보** 페이지에서 다음 정보를 입력한 후 오른쪽 아래에서 다음 화살표를 클릭합니다. 세부 정보 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보](http://go.microsoft.com/fwlink/?LinkID=248092)에서 **가상 네트워크 세부 정보** 페이지를 참조하십시오.

-   **이름 -** 가상 네트워크의 이름을 지정합니다. *YourVirtualNetwork*를 입력합니다.

-   **선호도 그룹 -** 드롭다운 목록에서 **새 선호도 그룹 만들기**를 선택합니다. 선호도 그룹을 사용하면 성능 향상을 위해 동일한 데이터 센터에서 Azure 서비스를 물리적으로 그룹화할 수 있습니다. 하나의 선호도 그룹에는 하나의 가상 네트워크만 할당할 수 있습니다.

-   **지역 -** 드롭다운 목록에서 원하는 지역을 선택합니다. 지정한 지역에 있는 데이터 센터에 가상 네트워크가 만들어집니다.

-   **선호도 그룹 이름 -** 새 선호도 그룹의 이름을 지정합니다. *YourAffinityGroup*을 입력합니다.

    ![](./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png)

1.  **DNS 서버 및 VPN 연결** 페이지에서 다음 정보를 입력한 후 오른쪽 아래에서 다음 화살표를 클릭합니다. 이 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보](http://go.microsoft.com/fwlink/?LinkID=248092)의 **DNS 서버 및 VPN 연결** 페이지를 참조하십시오.

    -   **DNS 서버(옵션) -** 사용할 DNS 서버 이름 및 IP 주소를 입력합니다. 이 설정은 DNS 서버를 만들지 않고 기존의 DNS 서버를 참조합니다.

        **참고**

        공용 DNS 서비스를 사용하려는 경우 이 화면에서 해당 정보를 입력할 수 있습니다. 그렇지 않으면 이름 확인은 기본적으로 Azure 서비스로 설정됩니다. 자세한 내용은 [Azure 이름 확인 개요](http://go.microsoft.com/fwlink/?linkid=248097)를 참조하십시오.

    -   **지점과 사이트 간 연결 또는 사이트 간 연결에 대한 확인란은 선택하지 마십시오.** 이 자습서에서 만드는 가상 네트워크는 프레미스 간 연결에서는 사용할 수 없습니다.

    ![](./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png)

2.  **가상 네트워크 주소 공간** 페이지에서 다음 정보를 입력한 후 오른쪽 아래의 확인란을 클릭하여 네트워크를 구성합니다. 주소 공간은 CIDR 표기법 10.0.0.0/8, 172.16.0.0/12 또는 192.168.0.0/16(RFC 1918에서 지정하는 대로)에 지정된 개인 주소 범위여야 합니다. 이 페이지의 설정에 대한 자세한 내용은 [관리 포털의 가상 네트워크 구성 정보](http://go.microsoft.com/fwlink/?LinkID=248092)에서 **가상 네트워크 주소 공간** 페이지를 참조하십시오.

    -   **주소 공간:** 화면 위의 오른쪽 모서리에서 CIDR을 클릭한 후 다음을 입력합니다.

        -   **시작 IP:** 10.4.0.0

        -   **CIDR:** /16

    -   **서브넷 추가:** 다음을 입력합니다.

        -   시작 IP가 *10.4.2.0/24*인 *FrontEndSubnet*으로 **서브넷-1 이름 변경** 후 **서브넷 추가**를 클릭합니다.

        -   시작 IP가 *10.4.3.0/24*인 *BackEndSubnet*이라는 **서브넷 만들기**

        -   두 개의 서브넷이 만들어졌는지 확인한 다음 오른쪽 아래의 확인 표시를 클릭하여 가상 네트워크를 만듭니다.

    ![](./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png)

3.  확인 표시를 클릭하면 가상 네트워크가 만들어지기 시작합니다. 가상 네트워크가 만들어지면 관리 포털의 네트워크 페이지에 있는 **상태** 아래에 **Created**가 나열됩니다.

    ![](./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png)

4.  일단 가상 네트워크가 만들어지고 나면 다음 자습서 작업을 계속할 수 있습니다.

    -   [가상 네트워크에 가상 컴퓨터 추가](/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/) - 이 기본 자습서를 사용하여 가상 네트워크에 가상 컴퓨터를 설치합니다.

    -   가상 컴퓨터 및 설치 옵션에 대한 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법](/en-us/manage/windows/how-to-guides/custom-create-a-vm/) 및 [Azure 가상 컴퓨터](/en-us/manage/windows/)를 참조하십시오.

    -   [Azure에 새 Active Directory 포리스트 설치](/en-us/manage/services/networking/active-directory-forest/) - 이 자습서를 사용하여 다른 네트워크에 연결하지 않고 새 Active Directory 포리스트를 설치합니다. 이 자습서에서는 새 포리스트 설치를 위해 VM(가상 컴퓨터)을 만드는 데 필요한 특정 단계를 설명합니다. 이 자습서를 사용할 경우에는 관리 포털을 사용하여 VM을 만들지 마십시오.

참고 항목
---------

-   [Azure 가상 네트워크 개요](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-   [Azure 가상 네트워크 FAQ](http://go.microsoft.com/fwlink/?LinkId=296650)

-   [Azure 가상 네트워크 구성 작업](http://go.microsoft.com/fwlink/?LinkId=296652)

-   [네트워크 구성 파일을 사용하여 가상 네트워크 구성](http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx)

-   [Azure 이름 확인 개요](http://go.microsoft.com/fwlink/?LinkId=248097)


