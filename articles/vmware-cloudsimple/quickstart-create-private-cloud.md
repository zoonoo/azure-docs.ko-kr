---
title: '빠른 시작: 프라이빗 클라우드 만들기'
titleSuffix: Azure VMware Solutions by CloudSimple
description: CloudSimple을 통해 Azure VMware 솔루션으로 프라이빗 클라우드를 만들고 구성하는 방법에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7460490dbd45862f4269d25e3910373700ec9a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564723"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>빠른 시작 - 프라이빗 클라우드 환경 구성

이 문서에서는 CloudSimple 프라이빗 클라우드를 만들고 프라이빗 클라우드 환경을 설정하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[네트워킹 필수 구성 조건](cloudsimple-network-checklist.md)검토 .

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

프라이빗 클라우드는 ESXi 호스트, vCenter, vSAN 및 NSX를 지원하는 격리된 VMware 스택입니다.

프라이빗 클라우드는 CloudSimple 포털을 통해 관리됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다. 스택은 전용 노드 및 격리된 베어 메탈 하드웨어 노드에서 실행됩니다.

1. **모든 서비스를**선택합니다.
2. 클라우드 **간단한 서비스를**검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. **개요에서**사설 **클라우드 만들기를** 클릭하여 CloudSimple 포털에 대한 새 브라우저 탭을 엽니다.  메시지가 표시되면 Azure 로그인 자격 증명으로 로그인합니다.  

    ![Azure에서 사설 클라우드 만들기](media/create-private-cloud-from-azure.png)

5. CloudSimple 포털에서 프라이빗 클라우드의 이름을 입력합니다.
6. 프라이빗 클라우드의 **위치를** 선택합니다.
7. **Azure에서**프로비전한 것과 일치하는 노드 유형을 선택합니다.
8. **노드 수를 지정합니다.**  프라이빗 클라우드를 만들려면 최소 3개의 노드가 필요합니다.

    ![프라이빗 클라우드 만들기 - 기본 정보](media/create-private-cloud-basic-info.png)

9. **다음을 클릭합니다.**
10. vSphere/vSAN 서브넷의 CIDR 범위를 입력합니다. CIDR 범위가 온-프레미스 또는 다른 Azure 서브넷(가상 네트워크) 또는 게이트웨이 서브넷과 겹치지 않도록 합니다.

    **CIDR 범위 옵션:** /24, /23, /22 또는 /21. /24 CIDR 범위는 최대 26개의 노드를 지원하며, /23 CIDR 범위는 최대 58개의 노드를 지원하며, /22 및 /21 CIDR 범위는 64개의 노드(프라이빗 클라우드의 최대 노드 수)를 지원합니다.  자세한 내용은 VLAN 및 서브넷을 [참조하세요.](cloudsimple-vlans-subnets.md)

      > [!IMPORTANT]
      > vSphere/vSAN CIDR 범위의 IP 주소는 프라이빗 클라우드 인프라에서 사용하도록 예약되어 있습니다.  가상 컴퓨터에서 이 범위의 IP 주소를 사용하지 마십시오.

11. **다음을 클릭합니다.**
12. 설정을 검토합니다. 설정을 변경해야 하는 경우 이전 을 **클릭합니다.**
13. **만들기**를 클릭합니다.

프라이빗 클라우드 프로비저닝 프로세스가 시작됩니다.  프라이빗 클라우드를 프로비전하는 데 최대 2시간이 걸릴 수 있습니다.

## <a name="launch-cloudsimple-portal"></a>클라우드심플 포털 출시

Azure 포털에서 CloudSimple 포털에 액세스할 수 있습니다.  CloudSimple 포털은 단일 사인온(SSO)을 사용하여 Azure 로그인 자격 증명으로 시작됩니다.  CloudSimple 포털에 액세스하려면 **CloudSimple 서비스 권한 부여** 응용 프로그램에 권한을 부여해야 합니다.  권한 부여에 대한 자세한 내용은 [CloudSimple 서비스 권한 부여 응용 프로그램에 대한 동의를](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)참조하십시오.

1. **모든 서비스를**선택합니다.
2. 클라우드 **간단한 서비스를**검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. 개요에서 **CloudSimple 포털로 이동을** 클릭하여 CloudSimple 포털에 대한 새 브라우저 탭을 엽니다.  메시지가 표시되면 Azure 로그인 자격 증명으로 로그인합니다.  

    ![클라우드심플 포털 출시](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>지점 간 VPN 만들기

지점 간 VPN 연결은 컴퓨터에서 프라이빗 클라우드에 연결하는 가장 간단한 방법입니다. 프라이빗 클라우드에 원격으로 연결하는 경우 지점 간 VPN 연결을 사용합니다.  프라이빗 클라우드에 빠르게 액세스하려면 아래 단계를 따르세요.  온-프레미스 네트워크에서 CloudSimple 리전에 대한 액세스는 [사이트 간 VPN](vpn-gateway.md) 또는 Azure [ExpressRoute](on-premises-connection.md)를 사용하여 수행할 수 있습니다.

### <a name="create-gateway"></a>게이트웨이 만들기

1. CloudSimple 포털을 실행하고 **네트워크를**선택합니다.
2. **VPN 게이트웨이를**선택합니다.
3. **새 VPN 게이트웨이를**클릭합니다.

    ![VPN 게이트웨이 만들기](media/create-vpn-gateway.png)

4. **게이트웨이 구성의**경우 다음 설정을 지정하고 다음 을 **클릭합니다.**

    * 지점 간 VPN을 게이트웨이 유형으로 **선택합니다.**
    * 이름을 입력하여 게이트웨이를 식별합니다.
    * CloudSimple 서비스가 배포되는 Azure 위치를 선택합니다.
    * 사이트 간 게이트웨이에 대한 클라이언트 서브넷을 지정합니다.  DHCP 주소는 연결할 때 이 서브넷에서 제공됩니다.

5. **연결/사용자의**경우 다음 설정을 지정하고 다음 을 **클릭합니다.**

    * 현재 및 미래의 모든 사용자가 이 사이트 간 게이트웨이를 통해 프라이빗 클라우드에 액세스할 수 있도록 자동으로 허용하려면 **모든 사용자를 자동으로 추가를**선택합니다. 이 옵션을 선택하면 사용자 목록의 모든 사용자가 자동으로 선택됩니다. 목록에서 개별 사용자를 선택 해제하여 자동 옵션을 재정의할 수 있습니다.
    * 개별 사용자만 선택하려면 사용자 목록에서 확인란을 클릭합니다.

6. VLAN/서브넷 섹션을 사용하면 게이트웨이 및 연결에 대한 관리 및 사용자 VLAN/서브넷을 지정할 수 있습니다.

    * **자동 추가** 옵션은 이 게이트웨이에 대한 전역 정책을 설정합니다. 설정은 현재 게이트웨이에 적용됩니다. **선택** 영역에서 설정을 재정의할 수 있습니다.
    * **프라이빗 클라우드의 관리 VLAN/서브넷 추가를 선택합니다.**
    * 모든 사용자 정의 VLAN/서브넷을 추가하려면 **사용자 정의 VLAN/서브넷 추가를 클릭합니다.**
    * **선택** 설정은 자동으로 추가 에서 전역 설정을 **재정의합니다.**

7. 설정을 검토하려면 **다음을** 클릭합니다. 편집 아이콘을 클릭하여 변경합니다.
8. VPN 게이트웨이를 만들려면 **만들기를 클릭합니다.**

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>지점 간 VPN을 사용하여 클라우드간이에 연결

컴퓨터에서 CloudSimple에 연결하려면 VPN 클라이언트가 필요합니다.  macOS 및 OS X용 Windows용 [OpenVPN 클라이언트](https://openvpn.net/community-downloads/) 또는 [점도를](https://www.sparklabs.com/viscosity/download/) 다운로드합니다.

1. CloudSimple 포털을 실행하고 **네트워크를**선택합니다.
2. **VPN 게이트웨이를**선택합니다.
3. VPN 게이트웨이 목록에서 Point-To-Site VPN 게이트웨이를 클릭합니다.
4. **사용자**선택 .
5. **VPN 구성 다운로드를 클릭합니다.**

    ![VPN 구성 다운로드](media/download-p2s-vpn-configuration.png)

6. VPN 클라이언트에서 구성을 가져옵니다.

    * Windows [클라이언트에서 구성 가져오기에](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) 대한 지침
    * [macOS 또는 OS X에서 구성 가져오기 에](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) 대한 지침

7. 클라우드에 연결간단합니다.

## <a name="create-a-vlan-for-your-workload-vms"></a>워크로드 VM을 위한 VLAN 만들기

프라이빗 클라우드를 만든 후 워크로드/애플리케이션 VM을 배포할 VLAN을 만듭니다.

1. CloudSimple 포털에서 **네트워크를**선택합니다.
2. **VLAN/서브넷을 클릭합니다.**
3. **VLAN/서브넷 만들기를 클릭합니다.**

    ![VLAN/서브넷 만들기](media/create-new-vlan-subnet.png)

4. 새 VLAN/서브넷에 대한 **프라이빗 클라우드를** 선택합니다.
5. 목록에서 VLAN ID를 선택합니다.  
6. 서브넷 이름을 입력하여 서브넷을 식별합니다.
7. 서브넷 CIDR 범위와 마스크를 지정합니다.  이 범위는 기존 서브넷과 겹치지 않아야 합니다.
8. **제출**을 클릭합니다.

    ![VLAN/서브넷 세부 정보 만들기](media/create-new-vlan-subnet-details.png)

VLAN/서브넷이 생성됩니다.  이제 이 VLAN ID를 사용하여 프라이빗 클라우드 vCenter에서 분산 포트 그룹을 만들 수 있습니다.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>환경을 Azure 가상 네트워크에 연결

CloudSimple은 프라이빗 클라우드를 위한 익스프레스루트 회로를 제공합니다. Azure에서 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다. 연결 설정에 대한 자세한 내용은 ExpressRoute 를 [사용하여 Azure 가상 네트워크 연결의](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)단계를 따릅니다.

## <a name="sign-in-to-vcenter"></a>vCenter에 로그인

이제 vCenter에 로그인하여 가상 컴퓨터 및 정책을 설정할 수 있습니다.

1. vCenter에 액세스하려면 CloudSimple 포털에서 시작합니다. 홈 페이지에서 일반적인 **작업**에서 **vSphere 클라이언트 를 클릭합니다.**  프라이빗 클라우드를 선택한 다음 프라이빗 클라우드에서 **vSphere 클라이언트 시작을** 클릭합니다.

    ![vSphere 클라이언트 시작](media/launch-vcenter-from-cloudsimple-portal.png)

2. 기본 vSphere 클라이언트를 선택하여 vCenter에 액세스하고 사용자 이름과 암호로 로그인합니다.  기본값은 다음과 같습니다.
    * 사용자 이름: **클라우드 소유자\@클라우드 simple.local**
    * 암호: **CloudSimple123!**  

다음 절차의 vCenter 화면은 html5(vSphere) 클라이언트에서 제공합니다.

## <a name="change-your-vcenter-password"></a>vCenter 암호 변경

CloudSimple은 vCenter에 처음 로그인할 때 암호를 변경하는 것이 좋습니다.  
설정한 암호는 다음 요구 사항을 충족해야 합니다.

* 최대 수명: 365일마다 암호를 변경해야 합니다.
* 재사용 제한: 사용자는 이전 5개의 암호 중 어느 한 개도 재사용할 수 없습니다.
* 길이: 8 - 20자
* 특수 문자: 하나 이상의 특수 문자
* 알파벳 문자: 하나 이상의 대문자, A-Z 및 하나 이상의 소문자 문자 a-z
* 숫자: 숫자 문자가 하나 이상, 0-9
* 최대 동일한 인접 문자: 3개

    예: CC 또는 CCC는 암호의 일부로 허용되지만 CCCC는 허용되지 않습니다.

요구 사항을 충족하지 않는 암호를 설정한 경우 다음 을 수행하십시오.

* vSphere 플래시 클라이언트를 사용하는 경우 오류가 보고됩니다.
* HTML5 클라이언트를 사용하는 경우 오류가 보고되지 않습니다. 클라이언트는 변경을 수락하지 않으며 이전 암호는 계속 작동합니다.

## <a name="access-nsx-manager"></a>액세스 NSX 관리자

NSX 관리자는 기본 암호와 함께 배포됩니다. 

* 사용자 이름: **관리자**
* 암호: **CloudSimple123!**

CloudSimple 포털에서 NSX 관리자의 정규화된 도메인 이름(FQDN) 및 IP 주소를 찾을 수 있습니다.

1. CloudSimple 포털을 실행하고 **리소스를**선택합니다.
2. 사용하려는 프라이빗 클라우드를 클릭합니다.
3. **vSphere 관리 네트워크** 선택
4. **NSX 관리자의** FQDN 또는 IP 주소를 사용하고 웹 브라우저를 사용하여 연결합니다.

    ![NSX 관리자 FQDN 찾기](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>포트 그룹 만들기

vSphere에서 분산 포트 그룹을 만들려면 다음을 수행합니다.

1. [vSphere 네트워킹 가이드에서](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)"분산 포트 그룹 추가"의 지침을 따릅니다.
2. 분산 포트 그룹을 설정할 때 [워크로드 VM에 대한 VLAN 만들기에서 만든 VLAN ID를](#create-a-vlan-for-your-workload-vms)제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](on-premises-connection.md)
* [온-프레미스에서 사이트 간 VPN 설정](vpn-gateway.md)
