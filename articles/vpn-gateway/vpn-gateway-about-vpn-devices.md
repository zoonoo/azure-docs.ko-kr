<properties 
   pageTitle="사이트 간 Azure 가상 네트워크 연결에 대한 VPN 장치 정보 | Microsoft Azure"
   description="Azure 가상 네트워크 사이트 간 VPN 게이트웨이 연결에 대한 IPsec 매개 변수 및 VPN 장치에 대해 알아봅니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2015"
   ms.author="cherylmc" />

# 사이트 간 가상 네트워크 연결에 대한 VPN 장치 정보

사이트 간 VPN 연결을 구성하려면 VPN 장치가 필요합니다. 온-프레미스 네트워크와 가상 네트워크 간의 보안 연결을 만들려고 할 때마다 또는 하이브리드 클라우드 솔루션을 만드는 데 사이트 간 연결을 사용할 수 있습니다. 이 문서에서는 호환 VPN 장치 및 구성 매개 변수를 설명합니다.

호환 VPN 장치 외에 사이트 간 연결을 사용하려면 공용 IPv4 IP 주소가 필요합니다. 또한 솔루션을 지원할 게이트웨이 형식을 선택할 수 있습니다. 모든 VPN 장치가 모든 게이트웨이 형식을 지원하는 것은 아닙니다. 솔루션을 만드는 데 필요한 게이트웨이 형식을 확인하려면 [VPN 게이트웨이](vpn-gateway-about-vpngateways.md)를 참조하세요.



## VPN 장치

장치 공급업체와 협력하여 표준 S2S(사이트 간) VPN 장치의 유효성을 검사했습니다. 가상 네트워크와 호환되는 것으로 알려진 VPN 장치 목록은 아래 [호환 VPN 장치](#compatible-vpn-devices) 섹션을 참조하세요. 이 목록에 포함된 장치 패밀리의 모든 장치는 Azure VPN 게이트웨이에서 작동합니다. VPN 장치를 구성하려면 적절한 장치 패밀리에 해당하는 장치 구성 샘플을 참조하세요.

고성능 VPN 게이트웨이 및 동적 라우팅 VPN 게이트웨이에 대한 사양은 별도로 언급하지 않는 한 동일합니다. 예를 들어 Azure 동적 라우팅 VPN 게이트웨이와 호환되는 유효성이 검사된 VPN 장치는 새 Azure 고성능 VPN 게이트웨이와도 호환됩니다.


### 호환 VPN 장치

특정 VPN 장치 패밀리를 적합하게 만들기 위해 VPN 장치 공급업체와 공동으로 노력해 왔습니다. 아래 섹션에서는 VPN 게이트웨이에서 작동한다고 알려진 모든 장치 패밀리의 목록을 제공합니다. 나열된 장치 패밀리의 구성원인 모든 장치는 예외가 언급되어 있지 않는 한 작동한다고 알려져 있습니다. 장치가 목록에 표시되지 않으면 [호환 목록에 없는 장치](#devices-not-on-the-compatible-list)를 참조하세요.



VPN 장치 지원은 장치 제조업체에 문의하세요.



| **공급업체** | **장치 패밀리** | **최소 OS 버전** | **고정 라우팅** | **동적 라우팅** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | AR 시리즈 VPN 라우터 | 2\.9.2 | 서비스 예정 | 호환되지 않음 |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| 호환되지 않음 |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | 호환되지 않음 |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [구성 지침](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | 호환되지 않음 |
| Check Point | Security Gateway | R75.40, R75.40VS | [구성 지침](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [구성 지침](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | 호환되지 않음 |
| Cisco | ASR | IOS 15.1(고정), IOS 15.2(동적) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Cisco | ISR | IOS 15.0(고정), IOS 15.1(동적) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Citrix | CloudBridge MPX 어플라이언스 또는 VPX 가상 어플라이언스 | 해당 없음 | [통합 지침](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | 호환되지 않음 |
| Dell SonicWALL | TZ 시리즈, NSA 시리즈, SuperMassive 시리즈 E-Class NSA 시리즈 | SonicOS 5.8.x, SonicOS 5.9.x, SonicOS 6.x | [구성 지침](https://www.sonicwall.com/app/projects/file_downloader/document_lib.php?t=TN&id=348) | 호환되지 않음 |
| F5 | BIG-IP 시리즈 | 해당 없음 | [구성 지침](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | 호환되지 않음 |
| Fortinet | FortiGate | FortiOS 5.0.7 | [구성 지침](http://docs.fortinet.com/fortigate/admin-guides) | [구성 지침](http://docs.fortinet.com/fortigate/admin-guides) |
| IIJ(Internet Initiative Japan) | SEIL 시리즈 | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [구성 지침](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | 호환되지 않음 |
| Juniper | SRX | JunOS 10.2(고정), JunOS 11.4(동적) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | J 시리즈 | JunOS 10.4r9(고정), JunOS 11.4(동적) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | ISG | ScreenOS 6.3(고정 및 동적) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | SSG | ScreenOS 6.2(고정 및 동적) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Microsoft | 라우팅 및 원격 액세스 서비스 | Windows Server 2012 | 호환되지 않음 | [Microsoft 샘플](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Openswan | Openswan | 2\.6.32 | (출시 예정) | 호환되지 않음 |
| Palo Alto Networks | PAN-OS 5.0 이상을 실행하는 모든 장치 | PAN-OS 5x 이상 | [Palo Alto Networks](https://support.paloaltonetworks.com/) | 호환되지 않음 |
| Watchguard | 모두 | Fireware XTM v11.x | [구성 지침](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | 호환되지 않음 |


### 호환 목록에 없는 장치


알려진 호환 VPN 장치 목록에 장치가 없지만 VPN 연결에 해당 장치를 사용하려는 경우 해당 장치가 [게이트웨이 요구 사항](vpn-gateway-about-vpngateways.md#gateway-requirements) 표에 나와 있는 최소 요구 사항을 충족하는지 확인해야 합니다. 최소 요구 사항을 충족하는 장치는 가상 네트워크에서도 잘 작동합니다. 추가 지원 및 구성 지침은 장치 제조업체에 문의하세요.


## 장치 구성 샘플 편집

제공된 VPN 장치 구성 샘플을 다운로드한 후 환경에 대한 설정을 반영하기 위해 일부 값을 바꿔야 합니다.

**샘플을 편집하려면:**

1. 메모장을 사용하여 샘플을 엽니다. 
1. 모든 <*텍스트*> 문자열을 검색해서 환경에 관련된 값으로 바꿉니다. < and >을 포함해야 합니다. 이름을 지정할 때 선택하는 이름은 고유해야 합니다. 명령이 작동하지 않는 경우 해당 장치 제조업체 설명서를 참조하세요.

| **샘플 텍스트** | **변경** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | 이 개체에 대해 선택한 이름입니다. 예: myOnPremisesNetwork |
| &lt;RP\_AzureNetwork&gt; | 이 개체에 대해 선택한 이름입니다. 예: myAzureNetwork |
| &lt;RP\_AccessList&gt; | 이 개체에 대해 선택한 이름입니다. 예: myAzureAccessList |
| &lt;RP\_IPSecTransformSet&gt; | 이 개체에 대해 선택한 이름입니다. 예: myIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | 이 개체에 대해 선택한 이름입니다. 예: myIPSecCryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | 범위를 지정합니다. 예: 192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | 서브넷 마스크를 지정합니다. 예: 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | 온-프레미스 범위를 지정합니다. 예: 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | 온-프레미스 서브넷 마스크를 지정합니다. 예: 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | 이 정보는 가상 네트워크와 관련이 있으며 **게이트웨이 IP 주소**인 관리 포털에 있습니다. |
| &lt;SP\_PresharedKey&gt; | 이 정보는 가상 네트워크와 관련이 있으며 키 관리인 관리 포털에 있습니다. |



## IPsec 매개 변수

### IKE 1단계 설정

| **속성** | **고정 라우팅 VPN 게이트웨이** | **동적 라우팅 VPN 게이트웨이 및 표준 또는 고성능 VPN 게이트웨이** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE 버전 | IKEv1 | IKEv2 |
| Diffie-Hellman 그룹 | 그룹 2(1024비트) | 그룹 2(1024비트) |
| 인증 방법 | 미리 공유한 키 | 미리 공유한 키 |
| 암호화 알고리즘 | AES256 AES128 3DES | AES256 3DES |
| 해시 알고리즘 | SHA1(SHA128) | SHA1(SHA128) |
| 1단계 SA(보안 연결) 수명(시간) | 28,800초 | 28,800초 |


### IKE 2단계 설치

| **속성** | **고정 라우팅 VPN 게이트웨이** | **동적 라우팅 VPN 게이트웨이 및 표준 또는 고성능 VPN 게이트웨이** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE 버전 | IKEv1 | IKEv2 |
| 해시 알고리즘 | SHA1(SHA128) | SHA1(SHA128) |
| 2단계 SA(보안 연결) 수명(시간) | 3,600초 | - | | 2단계 SA(보안 연결) 수명(처리량) | 102,400,000KB | - | | IPsec SA 암호화 및 인증 제안(우선 순위 순서로) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. 해당 없음 | 동적 라우팅 게이트웨이 IPsec SA(보안 연결) 제안 | | PFS(Perfect Forward Secrecy) | 아니요 | 예(DH Group1) | | DPD(Dead Peer Detection) | 지원되지 않음 | 지원됨 |

### 동적 라우팅 게이트웨이 IPsec SA(보안 연결) 제안

아래 표에는 IPsec SA 암호화 및 인증 제안이 나열되어 있습니다. 제안이 제시되거나 수락되는 기본 설정 순서대로 제안이 나열되어 있습니다.

| **IPsec SA 암호화 및 인증 제안** | **Azure 게이트웨이(초기자)** | Azure 게이트웨이(응답자) |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1(ESP AES\_128, null HMAC 사용) |
| 5 | AH SHA1(ESP AES\_256, null HMAC 사용) | AH SHA1(ESP 3\_DES, null HMAC 사용) |
| 6 | AH SHA1(ESP AES\_128, null HMAC 사용) | AH MD5(ESP 3\_DES, null HMAC 사용), 제안된 수명 없음 |
| 7 | AH SHA1(ESP 3\_DES, null HMAC 사용) | AH SHA1(ESP 3\_DES SHA1 사용), 수명 없음 |
| 8 | AH MD5(ESP 3\_DES, null HMAC 사용), 제안된 수명 없음 | AH MD5(ESP 3\_DES MD5 사용), 수명 없음 |
| 9 | AH SHA1(ESP 3\_DES SHA1 사용), 수명 없음 | ESP DES MD5 |
| 10 | AH MD5(ESP 3\_DES MD5 사용), 수명 없음 | ESP DES SHA1, 수명 없음 |
| 11 | ESP DES MD5 | AH SHA1(ESP DES null HMAC 사용), 제안된 수명 없음 |
| 12 | ESP DES SHA1, 수명 없음 | AH MD5(ESP DES null HMAC 사용), 제안된 수명 없음 |
| 13 | AH SHA1(ESP DES null HMAC 사용), 제안된 수명 없음 | AH SHA1(ESP DES SHA1 사용), 수명 없음 |
| 14 | AH MD5(ESP DES null HMAC 사용), 제안된 수명 없음 | AH MD5(ESP DES MD5 사용), 수명 없음 |
| 15 | AH SHA1(ESP DES SHA1 사용), 수명 없음 | ESP SHA, 수명 없음 |
| 16 | AH MD5(ESP DES MD5 사용), 수명 없음 | ESP MD5, 수명 없음 |
| 17 | - | AH SHA, 수명 없음 | | 18 | - | AH MD5, 수명 없음 |




- Azure 네트워크 내의 VNet 간 연결을 위해 동적 라우팅 및 고성능 VPN 게이트웨이를 사용하여 IPsec ESP NULL 암호화를 지정할 수 있습니다. 

- 인터넷을 통한 프레미스 간 연결의 경우 중요한 통신의 보안을 보장하려면 위의 표에 나열된 암호화 및 해시 알고리즘을 사용하는 기본 Azure VPN 게이트웨이 설정을 사용하세요.

## 다음 단계


VPN 게이트웨이에 대한 자세한 내용은 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

사이트 간 VPN을 구성하려면 [사이트 간 VPN 연결을 사용하여 가상 네트워크 만들기](vpn-gateway-site-to-site-create.md)를 참조하세요.

<!---HONumber=Nov15_HO1-->