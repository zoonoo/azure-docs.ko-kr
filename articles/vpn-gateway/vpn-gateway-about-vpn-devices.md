<properties 
   pageTitle="Azure 가상 네트워크 연결을 위한 사이트 간 VPN 게이트웨이 연결에 대한 VPN 장치 정보 | Microsoft Azure"
   description="S2S VPN 게이트웨이 연결에 대한 VPN 장치 및 IPsec 매개 변수에 대해 알아봅니다. 사이트 간 연결은 하이브리드 구성에 사용될 수 있습니다. 이 문서에는 VPN 게이트웨이 장치의 구성 지침 및 샘플에 대한 링크가 있습니다."
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
   ms.date="12/14/2015"
   ms.author="cherylmc" />

# 사이트 간 VPN 게이트웨이 연결에 대한 VPN 장치 정보

S2S(사이트 간) VPN 연결을 구성하려면 VPN 장치가 필요합니다. 온-프레미스 네트워크와 가상 네트워크 간의 보안 연결을 만들려고 할 때마다 또는 하이브리드 솔루션을 만드는 데 사이트 간 연결을 사용할 수 있습니다. 이 문서에서는 호환 VPN 장치 및 구성 매개 변수를 설명합니다. 사이트 간 연결을 구성할 때는 공용 IPv4 IP 주소가 VPN 장치에 필요합니다.

확인된 VPN 장치 테이블에 장치가 표시되지 않는 경우 이 문서의 확인되지 않은 VPN 장치 섹션을 참조하세요. 장치가 Azure에서 계속 작동할 수 있습니다. VPN 장치 지원은 장치 제조업체에 문의하세요.

**테이블 확인 시 주의 사항:**

- 정적 및 동적 라우팅에 대한 용어가 변경되었습니다. 두 용어를 모두 사용할 수 있습니다. 기능은 변경되지 않고 이름만 변경됩니다.
	- 정적 라우팅 = 정책 기반
	- 동적 라우팅 = 경로 기반 
- 고성능 VPN 게이트웨이 및 경로 기반 VPN 게이트웨이에 대한 사양은 별도로 언급하지 않는 한 동일합니다. 예를 들어 경로 기반 VPN 게이트웨이와 호환되는 확인된 VPN 장치는 새 Azure 고성능 VPN 게이트웨이와도 호환됩니다. 


## 확인된 VPN 장치 

장치 공급업체와 협력하여 표준 VPN 장치의 유효성을 검사했습니다. 아래 목록에 포함된 장치 패밀리의 모든 장치는 Azure VPN 게이트웨이에서 작동합니다. 구성할 솔루션에 대해 만들어야 하는 게이트웨이 유형을 확인하려면[VPN 게이트웨이](vpn-gateway-about-vpngateways.md) 문서를 참조하세요.

VPN 장치를 구성하려면 적절한 장치 패밀리에 해당하는 링크를 참조하세요.



| **공급업체** | **장치 패밀리** | **최소 OS 버전** | **정책 기반** | **경로 기반** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | AR 시리즈 VPN 라우터 | 2\.9.2 | 서비스 예정 | 호환되지 않음 |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| 호환되지 않음 |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | 호환되지 않음 |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [구성 지침](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | 호환되지 않음 |
| Check Point | Security Gateway | R75.40, R75.40VS | [구성 지침](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [구성 지침](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkID=717348) | 호환되지 않음 |
| Cisco | ASR | IOS 15.1(정책 기반), IOS 15.2(경로 기반) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkID=717348) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkID=717348) |
| Cisco | ISR | IOS 15.0(정책 기반), IOS 15.1(경로 기반) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkID=717348) | [Cisco 샘플](http://go.microsoft.com/fwlink/p/?LinkID=717348) |
| Citrix | CloudBridge MPX 어플라이언스 또는 VPX 가상 어플라이언스 | 해당 없음 | [통합 지침](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | 호환되지 않음 |
| Dell SonicWALL | TZ 시리즈, NSA 시리즈, SuperMassive 시리즈 E-Class NSA 시리즈 | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [지침 - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [지침 - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [지침 - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [지침 - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | BIG-IP 시리즈 | 해당 없음 | [구성 지침](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | 호환되지 않음 |
| Fortinet | FortiGate | FortiOS 5.0.7 | [구성 지침](http://docs.fortinet.com/fortigate/admin-guides) | [구성 지침](http://docs.fortinet.com/fortigate/admin-guides) |
| IIJ(Internet Initiative Japan) | SEIL 시리즈 | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [구성 지침](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | 호환되지 않음 |
| Juniper | SRX | JunOS 10.2(정책 기반), JunOS 11.4(경로 기반) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | J 시리즈 | JunOS 10.4r9(정책 기반), JunOS 11.4(경로 기반) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | ISG | ScreenOS 6.3(정책 기반 및 경로 기반) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | SSG | ScreenOS 6.2(정책 기반 및 경로 기반) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Microsoft | 라우팅 및 원격 액세스 서비스 | Windows Server 2012 | 호환되지 않음 | [Microsoft 샘플](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Openswan | Openswan | 2\.6.32 | (출시 예정) | 호환되지 않음 |
| Palo Alto Networks | PAN-OS 5.0 이상을 실행하는 모든 장치 | PAN-OS 5x 이상 | [Palo Alto Networks](https://support.paloaltonetworks.com/) | 호환되지 않음 |
| Watchguard | 모두 | Fireware XTM v11.x | [구성 지침](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | 호환되지 않음 |


## 확인되지 않은 VPN 장치

장치가 확인된 VPN 장치 테이블(위 참조)에 없는 경우 여전히 사이트 간 연결을 사용할 수 있습니다. VPN 장치가 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md#gateway-requirements) 문서의 게이트웨이 요구 사항 섹션에 설명된 최소 요구 사항을 충족하는지 확인하세요. 최소 요구 사항을 충족하는 장치는 VPN 게이트웨이에서도 원활하게 작동합니다. 추가 지원 및 구성 지침은 장치 제조업체에 문의하세요.


## 장치 구성 샘플 편집

제공된 VPN 장치 구성 샘플을 다운로드한 후 환경에 대한 설정을 반영하기 위해 일부 값을 바꿔야 합니다.

**샘플을 편집하려면**

1. 메모장을 사용하여 샘플을 엽니다. 
1. 모든 <*text*> 문자열을 검색하여 환경에 관련된 값으로 바꿉니다. < and >를 포함해야 합니다. 이름을 지정할 때 선택하는 이름은 고유해야 합니다. 명령이 작동하지 않는 경우 해당 장치 제조업체 설명서를 참조하세요.

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

| **속성** | **정책 기반** | **경로 기반 및 표준 또는 고성능 VPN 게이트웨이** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE 버전 | IKEv1 | IKEv2 |
| Diffie-Hellman 그룹 | 그룹 2(1024비트) | 그룹 2(1024비트) |
| 인증 방법 | 미리 공유한 키 | 미리 공유한 키 |
| 암호화 알고리즘 | AES256 AES128 3DES | AES256 3DES |
| 해시 알고리즘 | SHA1(SHA128) | SHA1(SHA128) |
| 1단계 SA(보안 연결) 수명(시간) | 28,800초 | 28,800초 |


### IKE 2단계 설치

| **속성** | **정책 기반** | **경로 기반 및 표준 또는 고성능 VPN 게이트웨이** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE 버전 | IKEv1 | IKEv2 |
| 해시 알고리즘 | SHA1(SHA128) | SHA1(SHA128) |
| 2단계 SA(보안 연결) 수명(시간) | 3,600초 | - |
| 2단계 SA(보안 연결) 수명(처리량) | 102,400,000KB | - |
| IPsec SA 암호화 및 인증 제안(우선 순위 순서로) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. 해당 없음 | *경로 기반 게이트웨이 IPsec SA(보안 연결) 제안 참조*(아래) |
| PFS(Perfect Forward Secrecy) | 아니요 | 예(DH Group1) |
| DPD(Dead Peer Detection) | 지원되지 않음 | 지원됨 |

### 경로 기반 게이트웨이 IPsec SA(보안 연결) 제안

아래 표에는 IPsec SA 암호화 및 인증 제안이 나열되어 있습니다. 제안이 제시되거나 수락되는 기본 설정 순서대로 제안이 나열되어 있습니다.

| **IPsec SA 암호화 및 인증 제안** | **Azure 게이트웨이(초기자)** | **Azure 게이트웨이(응답자)** |
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
| 17 | - | AH SHA, 수명 없음 |
| 18 | - | AH MD5, 수명 없음 |


- 경로 기반 및 고성능 VPN 게이트웨이를 사용하여 IPsec ESP NULL 암호화를 지정할 수 있습니다. 이는 Azure 네트워크 내에서 VNet 간 연결에 사용됩니다. 

- 인터넷을 통한 프레미스 간 연결의 경우 중요한 통신의 보안을 보장하려면 위의 표에 나열된 암호화 및 해시 알고리즘을 사용하는 기본 Azure VPN 게이트웨이 설정을 사용하세요.

<!---HONumber=AcomDC_1217_2015-->
