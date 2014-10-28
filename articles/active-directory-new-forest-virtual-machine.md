<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Azure 가상 네트워크에 새 Active Directory 포리스트 설치

이 토픽에서는 Azure 가상 네트워크 또는 [Azure 가상 네트워크][Azure 가상 네트워크]의 VM(가상 컴퓨터)에 새 Windows Server Active Directory 환경을 만드는 방법을 보여 줍니다. 이 경우 Azure 가상 네트워크는 온-프레미스 네트워크에 연결되지 않습니다.

다음 관련 토픽을 참조할 수도 있습니다.

-   선택적으로 [관리 포털 마법사를 사용하여 사이트 간 VPN을 구성][관리 포털 마법사를 사용하여 사이트 간 VPN을 구성]하고 새 포리스트를 설치하거나 온-프레미스 포리스트를 Azure 가상 네트워크로 확장할 수 있습니다. 이러한 단계는 [Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치][Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치]를 참조하십시오.
-   Azure 가상 네트워크에 AD DS(Active Directory 도메인 서비스)를 설치하는 방법에 대한 개념 지침은 [Azure 가상 컴퓨터에 Windows Server Active Directory 배포에 대한 지침][Azure 가상 컴퓨터에 Windows Server Active Directory 배포에 대한 지침]을 참조하십시오.
-   Azure에 AD DS가 포함된 테스트 랩 환경을 만들기 위한 단계별 지침은 [테스트 랩 가이드: Azure의 Windows Server 2012 R2 기본 구성][테스트 랩 가이드: Azure의 Windows Server 2012 R2 기본 구성]을 참조하십시오.

## 목차

-   [온-프레미스와의 차이점][온-프레미스와의 차이점]
-   [1단계. Azure 가상 네트워크 만들기][1단계. Azure 가상 네트워크 만들기]
-   [2단계: 도메인 컨트롤러 및 DNS 서버 역할을 실행할 VM 만들기][2단계: 도메인 컨트롤러 및 DNS 서버 역할을 실행할 VM 만들기]
-   [3단계: Windows Server Active Directory 설치][3단계: Windows Server Active Directory 설치]
-   [4단계: Azure 가상 네트워크에 대한 DNS 서버 설정][4단계: Azure 가상 네트워크에 대한 DNS 서버 설정]
-   [5단계: 도메인 구성원에 대한 VM 만들기 및 도메인에 가입][5단계: 도메인 구성원에 대한 VM 만들기 및 도메인에 가입]

## <span id="differ"></span></a>온-프레미스와의 차이점

Azure에 도메인 컨트롤러를 설치할 때와 온-프레미스에 설치할 때의 차이점은 크지 않습니다. 주요 차이점은 다음 표에 나와 있습니다.

| 구성                                     | 온-프레미스                                                            | Azure 가상 네트워크                                                                          |
|------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| **도메인 컨트롤러의 IP 주소**            | 네트워크 어댑터 속성에 고정 IP 주소 할당                               | DHCP를 통해 IP 주소 받기 및 선택적으로 Set-AzureStaticVNetIP cmdlet을 실행하여 고정으로 설정 |
| **DNS 클라이언트 확인 프로그램**         | 도메인 구성원의 네트워크 어댑터 속성에 기본 및 대체 DNS 서버 주소 설정 | 가상 네트워크 속성에 DNS 서버 주소 설정                                                      |
| **Active Directory 데이터베이스 저장소** | 선택적으로 기본 저장소 위치를 C:\\에서 변경                            | 기본 저장소 위치를 C:\\에서 변경해야 함                                                      |

## <span id="createvnet"></span></a>1단계: Azure 가상 네트워크 만들기

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  가상 네트워크를 만듭니다. **네트워크** \> **가상 네트워크 만들기**를 클릭합니다. 다음 표의 값을 사용하여 마법사를 완료합니다.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">마법사 페이지</th>
    <th align="left">값 지정</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>가상 네트워크 세부 정보</strong></td>
    <td align="left"><p>Name: 가상 네트워크의 이름 입력</p>
    <p>Region: 가장 가까운 지역 선택</p>
    <p>선호도 그룹: <strong>새 선호도 그룹 만들기</strong></p>
    <p>선호도 그룹 이름: 선호도 그룹의 이름 입력</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS 및 VPN</strong></td>
    <td align="left"><p>DNS 서버는 비워 둠</p>
    <p>VPN 옵션 선택 안 함</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>가상 네트워크 주소 공간</strong></td>
    <td align="left"><p>서브넷 이름: 서브넷의 이름 입력</p>
    <p>시작 IP: <strong>10.0.0.0</strong></p>
    <p>CIDR: <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>2단계: 도메인 컨트롤러 및 DNS 서버 역할을 실행할 VM 만들기

1.  **새로 만들기** \> **계산** \> **가상 컴퓨터** \> **갤러리에서**를 클릭합니다.
2.  다음 표의 값을 사용하여 마법사를 완료합니다.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">마법사 페이지</th>
    <th align="left">값 지정</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>운영 체제</strong></td>
    <td align="left"><strong>Windows Server 2012 R2 Datacenter</strong> 선택</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>가상 컴퓨터 구성</strong></td>
    <td align="left"><p>릴리스 날짜: 오늘 날짜</p>
    <p>컴퓨터 이름: 고유한 값 지정</p>
    <p>계층: Standard</p>
    <p>크기: 임의 크기 선택</p>
    <p>사용자 이름: 이름 입력. 이 사용자 계정은 기본 제공 관리자 그룹의 구성원이 됩니다.</p>
    <p>암호: 8자 이상이어야 하며, 다음 문자 유형 중 3개를 포함해야 합니다.</p>
    <ul>
    <li>대문자</li>
    <li>소문자</li>
    <li>숫자</li>
    <li>특수 문자</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>클라우드 서비스</strong></td>
    <td align="left"><p>클라우드 서비스: <strong>새 클라우드 서비스 만들기</strong></p>
    <p>클라우드 서비스 이름: 기본값 적용</p>
    <p>지역/선호도 그룹/가상 네트워크: 만든 가상 네트워크 선택</p>
    <p>가상 네트워크 서브넷: 만든 서브넷 선택</p>
    <p>저장소 계정: <strong>자동으로 생성된 저장소 계정 사용</strong></p>
    <p>가용성 집합: <strong>None</strong></p>
    <p>Endpoints: 기본값 적용</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>VM 에이전트</strong></td>
    <td align="left"><strong>VM 에이전트 설치</strong> 선택</td>
    </tr>
    </tbody>
    </table>

3.  VM에 기본적으로 할당되는 동적 IP 주소는 클라우드 서비스 기간 동안 유효합니다. 그러나 VM을 종료하면 변경됩니다. VM을 종료해야 하는 경우 IP 주소가 지속되도록 [Set-AzureStaticVNetIP Azure PowerShell cmdlet을 실행][Set-AzureStaticVNetIP Azure PowerShell cmdlet을 실행](영문)하여 고정 IP 주소를 할당할 수 있습니다.
4.  VM에 추가 디스크를 연결하여 Active Directory 데이터베이스, 로그 및 SYSVOL을 저장합니다.
  5.  **VM** \> **연결** \> **빈 디스크 연결**을 클릭합니다.
  6.  크기(예: 10GB)를 지정하고 다른 모든 기본값을 적용합니다.
7.  VM에 로그온하고 추가 디스크를 포맷합니다.
  8.  **연결**을 클릭하여 VM에 로그온하고 **열기**를 클릭하여 RDP 세션을 만든 후 **연결**을 다시 클릭합니다.
  9.  지정한 새 사용자 이름과 암호로 자격 증명을 변경합니다.
  10. 서버 관리자에서 **도구** \> **컴퓨터 관리**를 클릭합니다.
  11. **디스크 관리**를 클릭한 후 **확인**을 클릭하여 새 디스크를 초기화합니다.
  12. 디스크 이름을 마우스 오른쪽 단추로 클릭한 후 **새 단순 볼륨**을 클릭합니다. 마법사를 완료하여 새 드라이브를 포맷합니다.

## <span id="installad"></span></a>3단계: Windows Server Active Directory 설치

온-프레미스와 동일한 루틴을 사용하여 [AD DS를 설치][AD DS를 설치](영문)합니다(즉, UI, 응답 파일 또는 Windows PowerShell을 사용할 수 있음). 새 포리스트를 설치하려면 관리자 자격 증명을 제공해야 합니다. Active Directory 데이터베이스, 로그 및 SYSVOL에 대한 위치를 지정하려면 기본 저장소 위치를 운영 체제 드라이브에서 VM에 연결한 추가 데이터 디스크로 변경합니다.

DC 설치가 완료된 후 VM에 다시 연결하고 DC에 로그온합니다. 도메인 자격 증명을 지정해야 합니다.

</p>
## <span id="dns"></span></a>4단계: Azure 가상 네트워크에 대한 DNS 서버 설정

1.  **가상 네트워크**를 클릭하고 만든 가상 네트워크를 두 번 클릭한 후 **구성**을 클릭합니다.
2.  **DNS 서버** 아래에 DC의 DIP와 이름을 입력하고 **저장**을 클릭합니다.
3.  VM을 선택하고 **다시 시작**을 클릭하여 새 DNS 서버의 IP 주소로 DNS 확인 프로그램 설정을 구성하도록 VM을 트리거합니다.

## <span id="domainmembers"></span></a>5단계: 도메인 구성원에 대한 VM 만들기 및 도메인에 가입

추가 VM을 만들어 도메인 구성원 컴퓨터를 프로비전합니다. UI 또는 Azure PowerShell을 사용할 수 있습니다. UI를 사용하는 경우 첫 번째 VM을 만들 때와 동일한 단계를 따르십시오. 그런 다음 온-프레미스와 마찬가지로 VM을 도메인에 가입시킵니다. Azure PowerShell을 사용하는 경우 VM을 프로비전하고 처음 시작할 때 도메인에 가입되도록 할 수 있습니다.

이 예제에서는 Windows Server 2012 R2 Datacenter를 실행하는 DC2라는 도메인에 가입된 VM을 만듭니다. DC2가 프로비전된 후에는 도메인 관리자로 DC2에 로그인하고 복제 DC가 되도록 수준을 높입니다.

Get-AzureVMImage를 실행하여 이미지 이름을 가져올 수 있습니다. 예를 들어 Windows Server 2012 R2용 이미지 목록을 반환하려면 Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}를 실행합니다.

    '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"

    #Deploy a new VM and join it to the domain
    #-------------------------------------------
    #Specify my DC's DNS IP (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'

    # OS Image to Use
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
    $service = 'DC2'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

스크립트를 다시 실행하는 경우 $service에 고유한 값을 제공해야 합니다. 이름이 이미 사용되었는지 여부를 반환하는 Test-AzureName -Service *service name*을 실행할 수 있습니다.

## 참고 항목

-   [Azure 가상 컴퓨터에 Windows Server Active Directory 배포에 대한 지침][1]

-   [관리 포털에서 클라우드 전용 가상 네트워크 구성][관리 포털에서 클라우드 전용 가상 네트워크 구성]

-   [관리 포털에서 사이트 간 VPN 구성][관리 포털에서 사이트 간 VPN 구성]

-   [Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치(영문)][Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치(영문)]

-   [Windows Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항][Windows Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항]

-   [Windows Azure IT Pro IaaS: (05) 가상 네트워크 및 프레미스 간 연결 만들기][Windows Azure IT Pro IaaS: (05) 가상 네트워크 및 프레미스 간 연결 만들기]

-   [Azure 가상 네트워크][Azure 가상 네트워크]

-   [Azure PowerShell을 설치 및 구성하는 방법(영문)][Azure PowerShell을 설치 및 구성하는 방법(영문)]

-   [Azure PowerShell][Azure PowerShell]

-   [Azure 관리 Cmdlet(영문)][Azure 관리 Cmdlet(영문)]

-   [Azure VM 고정 IP 주소 설정(영문)][Azure VM 고정 IP 주소 설정(영문)]

-   [Azure VM에 고정 IP를 할당하는 방법][Azure VM에 고정 IP를 할당하는 방법]

-   [새 Active Directory 포리스트 설치(영문)][AD DS를 설치]

-   [AD DS(Active Directory 도메인 서비스) 가상화(수준 100) 소개][AD DS(Active Directory 도메인 서비스) 가상화(수준 100) 소개]

-   [테스트 랩 가이드: Azure의 Windows Server 2012 R2 기본 구성][테스트 랩 가이드: Azure의 Windows Server 2012 R2 기본 구성]

  [Azure 가상 네트워크]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156007.aspx
  [관리 포털 마법사를 사용하여 사이트 간 VPN을 구성]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn133795.aspx
  [Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치]: http://www.windowsazure.com/ko-kr/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Azure 가상 컴퓨터에 Windows Server Active Directory 배포에 대한 지침]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156090.aspx
  [테스트 랩 가이드: Azure의 Windows Server 2012 R2 기본 구성]: http://www.microsoft.com/ko-kr/download/details.aspx?id=41684
  [온-프레미스와의 차이점]: #differ
  [1단계. Azure 가상 네트워크 만들기]: #createvnet
  [2단계: 도메인 컨트롤러 및 DNS 서버 역할을 실행할 VM 만들기]: #createvm
  [3단계: Windows Server Active Directory 설치]: #installad
  [4단계: Azure 가상 네트워크에 대한 DNS 서버 설정]: #dns
  [5단계: 도메인 구성원에 대한 VM 만들기 및 도메인에 가입]: #domainmembers
  [Azure 관리 포털]: https://manage.windowsazure.com
  [Set-AzureStaticVNetIP Azure PowerShell cmdlet을 실행]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn630228.aspx
  [AD DS를 설치]: http://technet.microsoft.com/library/jj574166.aspx
  [1]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [관리 포털에서 클라우드 전용 가상 네트워크 구성]: http://msdn.microsoft.com/ko-kr/library/dn631643.aspx
  [관리 포털에서 사이트 간 VPN 구성]: http://msdn.microsoft.com/ko-kr/library/dn133795.aspx
  [Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치(영문)]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT Pro IaaS: (05) 가상 네트워크 및 프레미스 간 연결 만들기]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Azure PowerShell을 설치 및 구성하는 방법(영문)]: http://www.windowsazure.com/ko-kr/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156055.aspx
  [Azure 관리 Cmdlet(영문)]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj152841
  [Azure VM 고정 IP 주소 설정(영문)]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [Azure VM에 고정 IP를 할당하는 방법]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [AD DS(Active Directory 도메인 서비스) 가상화(수준 100) 소개]: http://technet.microsoft.com/ko-kr/library/hh831734.aspx
