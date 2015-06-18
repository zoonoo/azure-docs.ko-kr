<properties 
	pageTitle="Azure에 복제본 도메인 컨트롤러 설치" 
	description="온-프레미스 Active Directory 포리스트에서 Azure 가상 컴퓨터에 도메인 컨트롤러를 설치하는 방법을 설명하는 자습서입니다." 
	services="virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="Justinha"/>


# Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치

이 항목에서는 Azure 가상 네트워크의 Azure VM(가상 컴퓨터)에 온-프레미스 Active Directory 도메인에 대한 추가 도메인 컨트롤러(복제본 DC라고도 함)를 설치하는 방법을 보여 줍니다. 

다음 관련 토픽을 참조할 수도 있습니다.

- 선택적으로 Azure 가상 네트워크에 새 Active Directory 포리스트를 설치할 수 있습니다. 이러한 단계는 [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)를 참조하세요.
-  Azure 가상 네트워크에 AD DS(Active Directory 도메인 서비스)를 설치하는 방법에 대한 개념 지침은 [Azure 가상 컴퓨터에 Windows Server Active Directory를 배포하기 위한  지침](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)을 참조하세요.
-  Azure에 AD DS가 포함된 테스트 랩 환경을 만들기 위한 단계별 지침은 [테스트 랩 가이드: Azure의 기본 구성](http://www.microsoft.com/download/details.aspx?id=41684)을 참조하세요.

## 시나리오 다이어그램

이 시나리오에서는 외부 사용자가 도메인에 가입된 서버에서 실행되는 응용 프로그램에 액세스해야 합니다. 응용 프로그램 서버 및 복제본 DC를 실행하는 VM은 Azure 가상 네트워크에 설치됩니다. 다음 다이어그램과 같이 [사이트 간 VPN](http://msdn.microsoft.com/library/azure/dn133795.aspx) 연결을 통해 가상 네트워크를 온-프레미스 네트워크에 연결할 수 있거나 더 빠른 연결을 위해 [Express 경로](http://azure.microsoft.com/services/expressroute/)를 사용할 수 있습니다. 

응용 프로그램 서버 및 DC는 계산 처리 분산을 위해 별개의 [클라우드 서비스](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/)에 배포되고 내결함성 향상을 위해 [가용성 집합](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/) 내에 배포됩니다. 
DC는 Active Directory 복제를 사용하여 온-프레미스 DC 및 서로 간에 복제됩니다. 동기화 도구가 필요하지 않습니다.

![][1]

## 1단계: Azure 가상 네트워크에 대한 Active Directory 사이트 만들기

가상 네트워크에 해당하는 네트워크 지역을 나타내는 사이트를 Active Directory에 만드는 것이 좋습니다. 그러면 인증, 복제 및 기타 DC 위치 작업을 최적화하는 데 도움이 됩니다. 다음 단계에서는 사이트를 만드는 방법을 설명합니다. 자세한 배경 정보는 [새 사이트 추가](http://technet.microsoft.com/library/cc781496.aspx)를 참조하세요.

1. Active Directory 사이트 및 서비스를 엽니다 **(서버 관리자**, **도구**, **Active Directory 사이트 및 서비스**를 차례로 클릭).
2. Azure 가상 네트워크를 만든 지역을 나타내는 사이트를 만듭니다(**사이트**, **작업**, **새 사이트**를 차례로 클릭하고, 새 사이트의 이름을 입력(예: Azure 미국 서부)한 후에 사이트 링크를 선택하고 **확인** 클릭).
3. 서브넷을 만들고 새 사이트와 연결합니다(**사이트**를 두 번 클릭하고 **서브넷**을 마우스 오른쪽 단추로 클릭한 다음 **새 서브넷**을 클릭하고, 가상 네트워크의 IP 주소 범위 입력(예: 시나리오 다이어그램의 10.1.0.0/16)를 입력한 다음에 새 Azure 사이트 선택하고 **확인** 클릭).

## 2단계: Azure 가상 네트워크 만들기

<ol><li><p>Azure 관리 포털에서 <b>새로 만들기</b> > <b>네트워크 서비스</b> > <b>가상 네트워크</b> > <b>사용자 지정 만들기</b>를 클릭하고 다음 값을 사용하여 마법사를 완료합니다.</p>

<table style="width:100%">
<tr>
<td>마법사 페이지</td>
<td>값 지정</td>
</tr>
<tr>
<td><b>가상 네트워크 세부 정보</b></td>
<td><ul><li>이름: 가상 네트워크의 이름을 입력합니다(예: WestUSVNet).</li><li>Region: 가상 네트워크를 배치할 Azure 위치입니다(예: 미국 서부). 가상 네트워크를 만든 후에는 이 위치를 변경할 수 없습니다.</li></ul>
</td>
</tr>
<tr>
<td><b>DNS 서버 및 VPN 연결</b></td>
<td><ul><li>DNS 서버: 온-프레미스 DNS 서버 하나 이상의 이름과 IP 주소를 지정합니다.</li><li>연결: <b>사이트 간 VPN 구성</b>을 선택합니다.</li><li>로컬 네트워크: 새 로컬 네트워크를 지정합니다.</li></ul>VPN 대신 Express 경로를 사용하는 경우 <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Exchange 공급자를 통해 Express 경로 연결 구성</a>.</td>
</tr>
<tr>
<td><b>사이트 간 연결</b></td>
<td><ul><li>이름: 온-프레미스 네트워크의 이름을 입력합니다.</li><li>VPN 장치 IP 주소: 가상 네트워크에 연결되는 장치의 공용 IP 주소를 지정합니다. VPN 장치는 NAT 뒤에 배치할 수 없습니다.</li><li>주소: 온-프레미스 네트워크에 대한 주소 범위를 지정합니다(예: 시나리오 다이어그램의 192.168.0.0/16).</li></ul></td>
</tr>
<tr>
<td><b>가상 네트워크 주소 공간</b></td>
<td><ul><li>주소 공간: Azure 가상 네트워크에서 실행할 VM에 대한 IP 주소 범위를 지정합니다(예: 시나리오 다이어그램의 10.1.0.0/16). 이 주소 범위는 온-프레미스 네트워크의 주소 범위와 겹칠 수 없습니다.</li><li>서브넷: 응용 프로그램 서버(예: 프런트 엔드, 10.1.1.0/24) 및 DC(예: 백 엔드, 10.1.2.0/24)의 서브넷에 대한 이름과 주소를 지정합니다.</li><li><b>게이트웨이 서브넷 추가</b>를 클릭합니다.</li></ul></td>
</tr>
</table>
</li>
<li><p>다음에는 가상 네트워크 게이트웨이를 구성하여 안전한 사이트 간 VPN 연결을 만들겠습니다. 자세한 내용은 <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">관리 포털에서 가상 네트워크 게이트웨이 구성</a> 을 참조하세요.</p>
</li>
<li><p>새 가상 네트워크와 온-프레미스 VPN 장치 간에 사이트 간 VPN 연결을 만듭니다. 자세한 내용은 <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">관리 포털에서 가상 네트워크 게이트웨이 구성</a> 을 참조하세요.</p>
</li>
</ol>

## 3단계: DC 역할에 대한 Azure VM 만들기

<p>필요에 따라 다음 단계를 반복하여 DC 역할을 호스트할 VM을 만듭니다. 내결함성과 중복성을 제공하려면 가상 DC를 두 개 이상 배포해야 합니다. Azure 가상 네트워크에 구성된 것과 유사한 DC가 둘 이상 있는 경우(즉, 둘 다 GC이고, DNS 서버를 실행하며, FSMO 역할을 수행하지 않는 등) 이러한 DC를 실행하는 VM을 가용성 집합에 배치하여 내결함성을 개선합니다.</p>


<ol><li><p>Azure 관리 포털에서 <b>새로 만들기</b> > <b>계산</b> > <b>가상 컴퓨터</b> > <b>갤러리에서</b>를 클릭합니다. 다음 값을 사용하여 마법사를 완료합니다. 다른 값이 제안되거나 필요하지 않은 한 설정의 기본값을 적용합니다.</p>
<table style="width:100%">
<tr>
<td><b>마법사 페이지</b></td>
<td><b>값 지정</b></td>
</tr>
<tr>
<td><b>이미지 선택</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>가상 컴퓨터 구성</b></td>
<td><ul><li>가상 컴퓨터 이름: 단일 레이블 이름(예: AzureDC2)을 입력합니다.</li><li>새 사용자 이름: 사용자 이름을 입력합니다. 이 사용자는 VM에서 로컬 관리자 그룹의 구성원이 됩니다. VM에 처음 로그인하려면 이 이름이 필요합니다. Administrator라는 기본 제공 계정은 사용할 수 없습니다.</li><li>새 암호/확인: 암호를 입력합니다.</li></ul></td>
</tr>
<tr>
<td><b>가상 컴퓨터 구성</b></td>
<td><ul><li>클라우드 서비스: 첫 번째 VM에 대해 <b>새 클라우드 서비스 만들기</b>를 선택한 다음 DC 역할을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</li><li>클라우드 서비스 DNS 이름: 전역적으로 고유한 이름을 지정합니다.</li><li>지역/선호도 그룹/가상 네트워크: 가상 네트워크 이름을 지정합니다(예: WestUSVNet).</li><li>저장소 계정: 첫 번째 VM에 대해 <b>자동으로 생성된 저장소 계정 사용</b>을 선택한 다음 DC 역할을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</li><li>가용성 집합: <b>가용성 집합 만들기</b>를 선택합니다.</li><li>가용성 집합 이름: 첫 번째 VM을 만들 때 가용성 집합의 이름을 입력한 다음 추가 VM을 만들 때는 동일한 이름을 선택합니다.</li></ul></td>
</tr>
<tr>
<td><b>가상 컴퓨터 구성</b></td>
<td><b>VM 에이전트 설치</b> 및 필요한 다른 확장을 선택합니다.</td>
</tr>
</table>
</li>
<li><p>DC 서버 역할을 실행할 각 VM에 디스크를 연결합니다. AD 데이터베이스, 로그 및 SYSVOL을 저장하려면 추가 디스크가 필요합니다. 디스크 크기(예: 10GB)를 지정하고 <b>호스트 캐시 기본 설정</b>을 <b>없음</b>으로 설정된 채로 둡니다. VM에 처음 로그인한 후 <b>서버 관리자</b> > <b>파일 및 저장소 서비스</b>를 열어 NTFS를 사용하여 이 디스크에 볼륨을 만듭니다.</p></li>
<li><p>DC 역할을 실행할 VM에 대해 고정 IP 주소를 예약합니다. 고정 IP 주소를 예약하려면 Microsoft 웹 플랫폼 설치 관리자를 다운로드하고 <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">Azure PowerShell을 설치</a> 한 다음 <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a> cmdlet을 실행합니다.</p></li>

</ol>

## 4단계: Azure VM에 AD DS 설치

VM에 로그인한 다음 사이트 간 VPN 또는 Express 경로 연결을 통해 온-프레미스 네트워크의 리소스에 연결되었는지 확인합니다. Azure VM에 AD DS를 설치합니다. 온-프레미스 네트워크에 추가 DC(UI, Windows PowerShell 또는 응답 파일)를 설치할 때 사용하는 것과 동일한 프로세스를 사용할 수 있습니다. AD DS를 설치할 때 AD 데이터베이스, 로그 및 SYSVOL의 위치에 대해 새 볼륨을 지정해야 합니다. AD DS 설치에 리프레셔가 필요한 경우 [Active Directory 도메인 서비스 설치(수준 100)](http://technet.microsoft.com/library/hh472162.aspx) 또는 [기존 도메인에 복제본 Windows Server 2012 도메인 컨트롤러 설치(수준 200)](http://technet.microsoft.com/library/jj574134.aspx)를 참조하세요.

## 5단계: Azure 가상 네트워크에 대한 DNS 서버 다시 구성

<ol>
<li><p>Azure 관리 포털에서 가상 네트워크 이름을 클릭한 다음 <b>구성</b> 탭을 클릭하여 <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">가상 네트워크에 대한 DNS 서버 IP 주소에 대해</a> 온-프레미스 DNS 서버의 IP 주소 대신 복제본 DC에 할당된 고정 IP 주소를 사용하도록 다시 구성합니다. </p>
</li>
<li><p>가상 네트워크의 모든 복제 DC VM가 가상 네트워크의 DNS 서버를 사용하도록 구성되었는지 확인하려면 <b>가상 컴퓨터</b>를 클릭하고 각 VM의 상태 열을 클릭한 후 <b>다시 시작</b>을 클릭합니다. VM에 <b>실행 중</b> 상태가 표시될 때까지 기다렸다가 로그인을 시도합니다. 
</p>
</li>
</ol>

## 6단계: 응용 프로그램 서버에 대한 VM 만들기

<ol><li><p>다음 단계를 반복하여 응용 프로그램 서버로 실행할 VM을 만듭니다. 다른 값이 제안되거나 필요하지 않은 한 설정의 기본값을 적용합니다.</p>

<table style="width:100%">
<tr>
<td><b>마법사 페이지</b></td>
<td><b>값 지정</b></td>
</tr>
<tr>
<td><b>이미지 선택</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>가상 컴퓨터 구성</b></td>
<td><ul><li>가상 컴퓨터 이름: 단일 레이블 이름(예: TreyAppServer1)을 입력합니다.</li><li>새 사용자 이름: 사용자 이름을 입력합니다. 이 사용자는 VM에서 로컬 관리자 그룹의 구성원이 됩니다. VM에 처음 로그인하려면 이 이름이 필요합니다. Administrator라는 기본 제공 계정은 사용할 수 없습니다.</li><li>새 암호/확인:  암호를 입력합니다.</li></ul></td>
</tr>
<tr>
<td><b>가상 컴퓨터 구성</b></td>
<td><ul><li>클라우드 서비스: 첫 번째 VM에 대해 새 클라우드 서비스 만들기를 선택한 다음 응용 프로그램을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</li><li>클라우드 서비스 DNS 이름: 전역적으로 고유한 이름을 지정합니다.</li><li>지역/선호도 그룹/가상 네트워크: 가상 네트워크 이름을 지정합니다(예: WestUSVNet).</li><li>저장소 계정: 첫 번째 VM에 대해 <b>자동으로 생성된 저장소 계정 사용</b>을 선택한 다음 DC 역할을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</li><li>가용성 집합: <b>가용성 집합 만들기</b>를 선택합니다.</li><li>가용성 집합 이름: 첫 번째 VM을 만들 때 가용성 집합의 이름을 입력한 다음 추가 VM을 만들 때는 동일한 이름을 선택합니다.</li></ul></td>
</tr>
<tr>
<td><b>가상 컴퓨터 구성</b></td>
<td><b>VM 에이전트 설치</b> 및 필요한 다른 확장을 선택합니다.</td>
</tr>
</table>


</li>
<li><p>각 VM이 프로비전된 후 로그인하고 VM을 도메인에 가입합니다. <b>서버 관리자</b>에서 <b>로컬 서버</b> > <b>작업 그룹</b> > <b>변경...</b>을 클릭하고 <b>도메인</b>을 선택한 후 온-프레미스 도메인의 이름을 입력합니다. 도메인 사용자의 자격 증명을 제공한 다음 VM을 다시 시작하여 도메인 가입을 완료합니다.
</p>
</li>
</ol>
<p>
관리 포털을 사용하여 VM을 프로비전하는 대신 Microsoft Azure용 Windows PowerShell을 사용할 수 있습니다. 여기서 <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> 및 <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig</a> 를 사용하여 처음 부팅 시 VM을 도메인에 가입된 컴퓨터로 프로비전하고 <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM</a> 을 사용하여 VM 자체를 만듭니다. 
</p>

Windows PowerShell 사용에 대한 자세한 내용은 [Azure PowerShell 시작](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx) 및 [Azure 관리 Cmdlet](http://msdn.microsoft.com/library/windowsazure/jj152841)을 참조하세요.


## 추가 리소스

-  [Azure 가상 컴퓨터에 Windows Server Active Directory를 배포하기 위한 지침](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Azure PowerShell을 사용하여 기존 온-프레미스 Hyper-V 도메인 컨트롤러를 Azure에 업로드하는 방법](http://support.microsoft.com/kb/2904015)

-  [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Azure 가상 네트워크](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Microsoft Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS: (05) 가상 네트워크 및 프레미스 간 연결 만들기](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Azure 관리 Cmdlet](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=47-->
 