---
title: CloudSimple의 Azure VMware Solution-온-프레미스와 사설 클라우드 간에 VPN 구성
description: 온-프레미스 네트워크와 CloudSimple 사설 클라우드 간에 사이트 간 또는 지점 및 사이트 간 VPN 연결을 구성 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df13a6638c5ed72532b020ef7074123a2159b1c4
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536314"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드에 대 한 VPN 연결 구성

VPN 게이트웨이를 사용 하면 온-프레미스 네트워크와 클라이언트 컴퓨터에서 원격으로 CloudSimple 네트워크에 연결할 수 있습니다.  이 문서에서는 CloudSimple 포털에서 VPN gateway를 설정 하는 방법에 대 한 정보를 찾을 수 있습니다.  온-프레미스 네트워크와 CloudSimple 네트워크 간의 VPN 연결은 사설 클라우드의 vCenter 및 워크 로드에 대 한 액세스를 제공 합니다. CloudSimple은 지점 및 사이트 간 VPN 및 사이트 간 VPN 게이트웨이를 모두 지원 합니다.

## <a name="vpn-gateway-types"></a>VPN gateway 유형

* **지점 및 사이트 간 VPN** 연결은 컴퓨터에서 사설 클라우드에 연결 하는 가장 간단한 방법입니다. 원격으로 사설 클라우드에 연결 하는 데 지점 및 사이트 간 VPN 연결을 사용 합니다.
* **사이트 간 VPN** 연결을 사용 하 여 온-프레미스 서비스에 액세스 하도록 사설 클라우드 워크 로드를 설정할 수 있습니다. 또한 온-프레미스 Active Directory를 사설 클라우드 vCenter에 인증 하기 위한 id 소스로 사용할 수 있습니다.  현재는 **정책 기반 VPN** 유형이 지원 됩니다.

한 지역에서 사이트 간 VPN 게이트웨이와 지점 및 사이트 간 VPN 게이트웨이 하나를 만들 수 있습니다.

## <a name="point-to-site-vpn"></a>지점 및 사이트 간 VPN

지점 및 사이트 간 VPN gateway를 만들려면 지점 및 사이트 간 [vpn Gateway 만들기](vpn-gateway.md#create-point-to-site-vpn-gateway)를 참조 하세요.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>지점 및 사이트 간 VPN을 사용 하 여 CloudSimple에 연결

VPN 클라이언트는 컴퓨터에서 CloudSimple에 연결 하는 데 필요 합니다.  Windows 용 [Openvpn 클라이언트](https://openvpn.net/community-downloads/) 또는 macos 및 OS X 용 [Viscosity](https://www.sparklabs.com/viscosity/download/) 를 다운로드 합니다.

1. CloudSimple 포털을 시작 하 고 **네트워크**를 선택 합니다.
2. **VPN Gateway**를 선택 합니다.
3. VPN gateway 목록에서 지점 및 사이트 간 VPN gateway를 클릭 합니다.
4. **사용자**를 선택합니다.
5. **내 VPN 구성 다운로드** 를 클릭 합니다.

    ![VPN 구성 다운로드](media/download-p2s-vpn-configuration.png)

6. VPN 클라이언트에서 구성 가져오기

    * [Windows 클라이언트에서 구성 가져오기](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) 에 대 한 지침
    * [Macos 또는 OS X에서 구성을 가져오는](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) 방법에 대 한 지침

7. CloudSimple VPN gateway에 연결 합니다.

아래 예제에서는 **Viscosity Client**를 사용 하 여 연결을 가져오는 방법을 보여 줍니다.

#### <a name="import-connection-on-viscosity-client"></a>Viscosity client에서 연결 가져오기

1. 다운로드 한 .zip 파일에서 VPN 구성의 콘텐츠를 추출 합니다.

2. 컴퓨터에서 Viscosity를 엽니다.

3. 아이콘을 클릭 하 고**파일에서** **연결** > 가져오기를 선택 합니다. **+**

    ![파일에서 VPN 구성 가져오기](media/import-p2s-vpn-config.png)

4. 사용 하려는 프로토콜에 대 한 OpenVPN 구성 파일 (. ovpn)을 선택 하 고 **열기**를 클릭 합니다.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

이제 연결이 Viscosity 메뉴에 표시 됩니다.

#### <a name="connect-to-the-vpn"></a>VPN에 연결

Viscosity OpenVPN 클라이언트를 사용 하 여 VPN에 연결 하려면 메뉴에서 연결을 선택 합니다. 메뉴 아이콘이 업데이트 되어 연결이 설정 되었음을 표시 합니다.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>여러 사설 클라우드에 연결

지점 및 사이트 간 VPN 연결은 만든 첫 번째 사설 클라우드의 DNS 이름을 확인 합니다. 다른 사설 클라우드에 액세스 하려는 경우 VPN 클라이언트에서 DNS 서버를 업데이트 해야 합니다.

1. [Cloudsimple 포털](access-cloudsimple-portal.md)을 시작 합니다.

2. **리소스** > **사설** 클라우드로 이동 하 여 연결 하려는 사설 클라우드를 선택 합니다.

3. 사설 클라우드의 **요약** 페이지에서 사설 클라우드 DNS 서버 IP 주소를 **기본 정보**로 복사 합니다.

    ![사설 클라우드 DNS 서버](media/private-cloud-dns-server.png)

4. 컴퓨터의 시스템 트레이에서 Viscosity 아이콘을 마우스 오른쪽 단추로 클릭 하 고 **기본 설정**을 선택 합니다.

    ![VPN](media/vis00.png)

5. CloudSimple VPN 연결을 선택 합니다.

    ![VPN 연결](media/viscosity-client.png)

6. **편집** 을 클릭 하 여 연결 속성을 변경 합니다.

    ![VPN 연결 편집](media/viscosity-edit-connection.png)

7. **네트워킹** 탭을 클릭 하 고 사설 클라우드 DNS 서버 IP 주소를 쉼표나 공백으로 구분 하 고 도메인을로 ```cloudsimple.io```구분 하 여 입력 합니다.  **VPN 서버에서 보낸 DNS 설정 무시**를 선택 합니다.

    ![VPN 네트워킹](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 첫 번째 사설 클라우드에 연결 하려면 이러한 설정을 제거 하 고 VPN 서버에 연결 합니다.

## <a name="site-to-site-vpn"></a>사이트 간 VPN

사이트 간 VPN gateway를 만들려면 [사이트 간 vpn Gateway 만들기](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)를 참조 하세요.  온-프레미스 네트워크에서 사설 클라우드로의 사이트 간 VPN 연결은 다음과 같은 이점을 제공 합니다.  

* 온-프레미스 네트워크의 모든 워크스테이션에서 사설 클라우드 vCenter의 접근성
* 온-프레미스 Active Directory를 vCenter id 원본으로 사용
* 온-프레미스 리소스에서 사설 클라우드 vCenter로 VM 템플릿, Iso 및 기타 파일의 편리한 전송
* 온-프레미스 네트워크에서 사설 클라우드에서 실행 되는 워크 로드의 접근성

고가용성 모드에서 온-프레미스 VPN gateway를 설정 하려면 [고가용성 vpn 연결 구성](high-availability-vpn-connection.md)을 참조 하세요.

> [!IMPORTANT]
>    1. VPN 장치에서 TCP MSS 고정 (1200)를 설정 합니다. 또는 VPN 장치에서 MSS 고정을 지원 하지 않는 경우 터널 인터페이스의 MTU를 1240 바이트로 설정할 수 있습니다.
> 2. 사이트 간 VPN을 설정한 후에는 *. cloudsimple.io에 대 한 DNS 요청을 사설 클라우드 DNS 서버로 전달 합니다.  [온-프레미스 DNS 설정](on-premises-dns-setup.md)의 지침을 따릅니다.
