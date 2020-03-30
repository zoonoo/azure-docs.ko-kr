---
title: 클라우드에 의한 Azure VMware 솔루션간이 - 온-프레미스와 프라이빗 클라우드 간에 VPN 구성
description: 온-프레미스 네트워크와 CloudSimple 프라이빗 클라우드 간에 사이트 간 또는 사이트 간 또는 지점 간 VPN 연결을 구성하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087137"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>클라우드단순 프라이빗 클라우드에 VPN 연결 구성

VPN 게이트웨이를 사용하면 온-프레미스 네트워크와 클라이언트 컴퓨터에서 원격으로 CloudSimple 네트워크에 연결할 수 있습니다.  이 문서에서는 CloudSimple 포털에서 VPN 게이트웨이 설정에 대한 정보를 찾을 수 있습니다.  온-프레미스 네트워크와 CloudSimple 네트워크 간의 VPN 연결을 통해 프라이빗 클라우드의 vCenter 및 워크로드에 액세스할 수 있습니다. CloudSimple은 지점 간 VPN과 사이트 간 VPN 게이트웨이를 모두 지원합니다.

## <a name="vpn-gateway-types"></a>VPN 게이트웨이 유형

* **지점 간 VPN** 연결은 컴퓨터에서 프라이빗 클라우드에 연결하는 가장 간단한 방법입니다. 프라이빗 클라우드에 원격으로 연결하려면 지점 간 VPN 연결을 사용합니다.
* **사이트 간 VPN** 연결을 사용하면 사설 클라우드 워크로드를 설정하여 온-프레미스 서비스에 액세스할 수 있습니다. 또한 온-프레미스 Active Directory를 프라이빗 클라우드 vCenter인증을 위한 ID 소스로 사용할 수도 있습니다.  현재 **정책 기반 VPN** 유형이 지원됩니다.

리전에서 하나의 사이트 간 VPN 게이트웨이와 하나의 Point-To-Site VPN 게이트웨이를 만들 수 있습니다.

## <a name="point-to-site-vpn"></a>지점 및 사이트 간 VPN

지점 간 VPN 게이트웨이를 만들려면 [지점 간 VPN 게이트웨이 만들기를](vpn-gateway.md#create-point-to-site-vpn-gateway)참조하십시오.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>지점 간 VPN을 사용하여 클라우드간이에 연결

컴퓨터에서 CloudSimple에 연결하려면 VPN 클라이언트가 필요합니다.  macOS 및 OS X용 Windows용 [OpenVPN 클라이언트](https://openvpn.net/community-downloads/) 또는 [점도를](https://www.sparklabs.com/viscosity/download/) 다운로드합니다.

1. CloudSimple 포털을 실행하고 **네트워크를**선택합니다.
2. **VPN 게이트웨이를**선택합니다.
3. VPN 게이트웨이 목록에서 지점 간 VPN 게이트웨이를 클릭합니다.
4. **사용자**선택 .
5. 내 **VPN 구성 다운로드를** 클릭합니다.

    ![VPN 구성 다운로드](media/download-p2s-vpn-configuration.png)

6. VPN 클라이언트에서 구성 가져오기

    * Windows [클라이언트에서 구성 가져오기에](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) 대한 지침
    * [macOS 또는 OS X에서 구성 가져오기 에](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) 대한 지침

7. 클라우드심플 VPN 게이트웨이에 연결합니다.

아래 예제는 **점도 클라이언트를**사용하여 연결을 가져오는 것을 보여 주며.

#### <a name="import-connection-on-viscosity-client"></a>점도 클라이언트에서 연결 가져오기

1. 다운로드한 .zip 파일에서 VPN 구성의 내용을 추출합니다.

2. 컴퓨터에서 점도를 엽니다.

3. 아이콘을 **+** 클릭하고 > **파일에서** **연결 가져오기를**선택합니다.

    ![파일에서 VPN 구성 가져오기](media/import-p2s-vpn-config.png)

4. 사용하려는 프로토콜에 대해 OpenVPN 구성 파일(.ovpn)을 선택하고 **열기를**클릭합니다.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

이제 점도 메뉴에 연결이 나타납니다.

#### <a name="connect-to-the-vpn"></a>VPN에 연결

Viscosity OpenVPN 클라이언트를 사용하여 VPN에 연결하려면 메뉴에서 연결을 선택합니다. 메뉴 아이콘이 업데이트되어 연결이 설정되었음을 나타냅니다.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>여러 개인 클라우드에 연결

지점 간 VPN 연결은 사용자가 만든 첫 번째 프라이빗 클라우드의 DNS 이름을 확인합니다. 다른 프라이빗 클라우드에 액세스하려면 VPN 클라이언트에서 DNS 서버를 업데이트해야 합니다.

1. [클라우드 간단한 포털을](access-cloudsimple-portal.md)시작합니다.

2. **리소스** > **프라이빗 클라우드로** 이동하여 연결할 프라이빗 클라우드를 선택합니다.

3. 프라이빗 클라우드의 **요약** 페이지에서 **기본 정보**아래에 사설 클라우드 DNS 서버 IP 주소를 복사합니다.

    ![프라이빗 클라우드 DNS 서버](media/private-cloud-dns-server.png)

4. 컴퓨터의 시스템 트레이에서 점도 아이콘을 마우스 오른쪽 단추로 클릭하고 **환경 설정을 선택합니다.**

    ![VPN](media/vis00.png)

5. CloudSimple VPN 연결을 선택합니다.

    ![VPN 연결](media/viscosity-client.png)

6. **편집을** 클릭하여 연결 속성을 변경합니다.

    ![VPN 연결 편집](media/viscosity-edit-connection.png)

7. **네트워킹** 탭을 클릭하고 쉼표 또는 공간으로 구분된 프라이빗 클라우드 DNS 서버 IP ```cloudsimple.io```주소를 입력하고 도메인을 로 입력합니다.  **VPN 서버에서 전송한 DNS 설정 무시를**선택합니다.

    ![VPN 네트워킹](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 첫 번째 프라이빗 클라우드에 연결하려면 이러한 설정을 제거하고 VPN 서버에 연결합니다.

## <a name="site-to-site-vpn"></a>사이트 간 VPN

사이트 간 VPN 게이트웨이를 만들려면 [사이트 간 VPN 게이트웨이 만들기를](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)참조하십시오.  온-프레미스 네트워크에서 프라이빗 클라우드로의 사이트 간 VPN 연결은 이러한 이점을 제공합니다.  

* 온-프레미스 네트워크의 모든 워크스테이션에서 프라이빗 클라우드 vCenter의 접근성
* 온-프레미스 Active Directory를 vCenter ID 원본으로 사용
* 온-프레미스 리소스에서 프라이빗 클라우드 vCenter로 VM 템플릿, ISO 및 기타 파일을 편리하게 전송
* 온-프레미스 네트워크에서 프라이빗 클라우드에서 실행되는 워크로드의 접근성

고가용성 모드에서 온-프레미스 VPN 게이트웨이를 설정하려면 [고가용성 VPN 연결 구성을](high-availability-vpn-connection.md)참조하십시오.

> [!IMPORTANT]
>    1. VPN 장치에서 TCP MSS 클램핑을 1200으로 설정합니다. 또는 VPN 장치가 MSS 클램핑을 지원하지 않는 경우 터널 인터페이스에서 MTU를 대신 1240바이트로 설정할 수 있습니다.
> 2. 사이트 간 VPN이 설정되면 *.cloudsimple.io 대한 DNS 요청을 사설 클라우드 DNS 서버로 전달합니다.  [온-프레미스 DNS 설정의](on-premises-dns-setup.md)지침을 따릅니다.
