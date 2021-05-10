---
title: '빠른 시작: 프라이빗 클라우드 만들기'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Azure VMware Solutions by CloudSimple로 프라이빗 클라우드를 만들고 구성하는 방법 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073908"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>빠른 시작 - 프라이빗 클라우드 환경 구성

이 문서에서는 CloudSimple 프라이빗 클라우드를 만들고 프라이빗 클라우드 환경을 설정하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[네트워킹 필수 구성 요소](cloudsimple-network-checklist.md)를 검토합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

프라이빗 클라우드는 ESXi 호스트, vCenter, vSAN 및 NSX를 지원하는 격리된 VMware 스택입니다.

프라이빗 클라우드는 CloudSimple 포털을 통해 관리됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다. 스택은 전용 노드 및 격리된 운영 체제 미설치 하드웨어 노드에서 실행됩니다.

1. **모든 서비스** 를 선택합니다.
2. **CloudSimple 서비스** 를 검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. **개요** 에서 **프라이빗 클라우드 만들기** 를 클릭하여 CloudSimple 포털의 새 브라우저 탭을 엽니다.  메시지가 표시되면 Azure 로그인 자격 증명을 사용하여 로그인합니다.  

    ![Azure에서 프라이빗 클라우드 만들기](media/create-private-cloud-from-azure.png)

5. CloudSimple 포털에서 프라이빗 클라우드의 이름을 제공합니다.
6. 프라이빗 클라우드의 **위치** 를 선택합니다.
7. Azure에서 프로비저닝한 것과 일치하는 **노드 유형** 을 선택합니다.
8. **노드 수** 를 지정합니다.  프라이빗 클라우드를 만들려면 노드가 세 개 이상 필요합니다.

    ![프라이빗 클라우드 만들기 - 기본 정보](media/create-private-cloud-basic-info.png)

9. **다음: 고급 옵션** 을 클릭합니다.
10. vSphere/vSAN 서브넷의 CIDR 범위를 입력합니다. CIDR 범위가 온-프레미스 또는 기타 Azure 서브넷(가상 네트워크) 또는 게이트웨이 서브넷과 겹치지 않는지 확인합니다.

    **CIDR 범위 옵션:**  /24, /23, /22 또는 /21. /24 CIDR 범위는 최대 26개의 노드를 지원하고/23 CIDR 범위는 최대 58개의 노드를 지원하며,/22 및/21 CIDR 범위는 64개의 노드(프라이빗 클라우드의 최대 노드 수)를 지원합니다.  VLAN 및 서브넷에 대한 자세한 내용은 [VLAN 및 서브넷 개요](cloudsimple-vlans-subnets.md)를 참조하세요.

      > [!IMPORTANT]
      > vSphere/vSAN CIDR 범위의 IP 주소는 프라이빗 클라우드 인프라에서 사용하도록 예약되어 있습니다.  가상 머신에서는 이 범위의 IP 주소를 사용하지 마세요.

11. **다음: 검토 및 만들기** 를 클릭합니다.
12. 설정을 검토합니다. 설정을 변경하려면 **이전** 을 클릭합니다.
13. **만들기** 를 클릭합니다.

프라이빗 클라우드 프로비저닝 프로세스가 시작됩니다.  프라이빗 클라우드를 프로비저닝하는 데 최대 2시간이 걸릴 수 있습니다.

## <a name="launch-cloudsimple-portal"></a>CloudSimple 포털 시작

Azure Portal에서 CloudSimple 포털에 액세스할 수 있습니다.  CloudSimple 포털은 SSO(Single Sign-On)를 사용하여 Azure 로그인 자격 증명으로 시작됩니다.  CloudSimple 포털에 액세스하려면 **CloudSimple Service Authorization** 애플리케이션에 권한을 부여해야 합니다.  권한 부여에 대한 자세한 내용은 [CloudSimple Service Authorization 애플리케이션에 동의](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)를 참조하세요.

1. **모든 서비스** 를 선택합니다.
2. **CloudSimple 서비스** 를 검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. 개요에서 **CloudSimple 포털로 이동** 을 클릭하여 CloudSimple 포털에 대한 새 브라우저 탭을 엽니다.  메시지가 표시되면 Azure 로그인 자격 증명을 사용하여 로그인합니다.  

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>지점 및 사이트 간 VPN 만들기

지점 및 사이트 간 VPN 연결은 컴퓨터에서 프라이빗 클라우드에 연결하는 가장 간단한 방법입니다. 원격으로 프라이빗 클라우드에 연결하는 경우 지점 및 사이트 간 VPN 연결을 사용합니다.  프라이빗 클라우드에 빠르게 액세스하려면 다음 단계를 수행합니다.  온-프레미스 네트워크에서 CloudSimple 지역에 액세스하려면 [사이트 간 VPN](vpn-gateway.md) 또는 [Azure ExpressRoute](on-premises-connection.md)를 사용합니다.

### <a name="create-gateway"></a>게이트웨이 만들기

1. CloudSimple 포털을 시작하고 **네트워크** 를 선택합니다.
2. **VPN Gateway** 를 선택합니다.
3. **새 VPN Gateway** 를 클릭합니다.

    ![VPN 게이트웨이 만들기](media/create-vpn-gateway.png)

4. **게이트웨이 구성** 의 경우 다음 설정을 지정하고 **다음** 을 클릭합니다.

    * 게이트웨이 유형으로 **지점 및 사이트 간 VPN** 을 선택합니다.
    * 게이트웨이를 식별하는 이름을 입력합니다.
    * CloudSimple 서비스가 배포되는 Azure 위치를 선택합니다.
    * 지점 및 사이트 간 게이트웨이에 대한 클라이언트 서브넷을 지정합니다.  연결하면 이 서브넷에서 DHCP 주소가 제공됩니다.

5. **연결/사용자** 에 대해 다음 설정을 지정하고 **다음** 을 클릭합니다.

    * 모든 현재 및 이후 사용자가 지점 및 사이트 간 게이트웨이를 통해 프라이빗 클라우드에 액세스하도록 자동으로 허용하려면 **모든 사용자를 자동으로 추가** 를 선택합니다. 옵션을 선택하면 사용자 목록의 모든 사용자가 자동으로 선택됩니다. 목록에서 개별 사용자의 선택을 취소하여 자동 옵션을 재정의할 수 있습니다.
    * 개별 사용자만 선택하려면 사용자 목록에서 확인란을 클릭합니다.

6. VLAN/서브넷 섹션에서 게이트웨이 및 연결에 대한 관리 및 사용자 VLAN/서브넷을 지정할 수 있습니다.

    * **자동으로 추가** 옵션은 게이트웨이의 글로벌 정책을 설정합니다. 설정은 현재 게이트웨이에 적용됩니다. 설정은 **선택** 영역에서 재정의할 수 있습니다.
    * **프라이빗 클라우드의 관리 VLAN/서브넷 추가** 를 선택합니다.
    * 모든 사용자 정의 VLAN/서브넷을 추가하려면 **사용자 정의 VLAN/서브넷 추가** 를 클릭합니다.
    * **선택** 설정은 **자동 추가** 에서 글로벌 설정을 재정의합니다.

7. **다음** 을 클릭하여 설정을 검토합니다. 편집 아이콘을 클릭하여 변경합니다.
8. **만들기** 를 클릭하여 VPN 게이트웨이를 만듭니다.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>지점 및 사이트 간 VPN을 사용하여 CloudSimple에 연결

VPN 클라이언트는 컴퓨터에서 CloudSimple에 연결하는 데 필요합니다.  Windows용 [OpenVPN 클라이언트](https://openvpn.net/community-downloads/) 또는 macOS 및 OS X용 [Viscosity](https://www.sparklabs.com/viscosity/download/)를 다운로드합니다.

1. CloudSimple 포털을 시작하고 **네트워크** 를 선택합니다.
2. **VPN Gateway** 를 선택합니다.
3. VPN Gateway 목록에서 지점 및 사이트 간 VPN Gateway를 클릭합니다.
4. **사용자** 를 선택합니다.
5. **VPN 구성 다운로드** 를 클릭합니다.

    ![VPN 구성 다운로드](media/download-p2s-vpn-configuration.png)

6. VPN 클라이언트에서 구성을 가져옵니다.

    * [Windows 클라이언트에서 구성 가져오기](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)에 대한 지침
    * [ 또는 OS X에서 구성을 가져오는 방법](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)에 대한 지침

7. CloudSimple에 연결합니다.

## <a name="create-a-vlan-for-your-workload-vms"></a>워크로드 VM에 대한 VLAN 만들기

프라이빗 클라우드를 만든 후 워크로드/애플리케이션 VM을 배포할 VLAN을 만듭니다.

1. CloudSimple 포털에서 **네트워크** 를 선택합니다.
2. **VLAN/서브넷** 을 클릭합니다.
3. **VLAN/서브넷 만들기** 를 클릭합니다.

    ![VLAN/서브넷 만들기](media/create-new-vlan-subnet.png)

4. 새 VLAN/서브넷에 대한 **프라이빗 클라우드** 를 선택합니다.
5. 목록에서 VLAN ID를 선택합니다.  
6. 서브넷을 식별하는 서브넷 이름을 입력합니다.
7. 서브넷 CIDR 범위와 마스크를 지정합니다.  이 범위는 기존 서브넷과 겹치지 않아야 합니다.
8. **제출** 을 클릭합니다.

    ![VLAN/서브넷 만들기 세부 정보](media/create-new-vlan-subnet-details.png)

VLAN/서브넷이 생성됩니다.  이제 이 VLAN ID를 사용하여 프라이빗 클라우드 vCenter에 분산 포트 그룹을 만들 수 있습니다.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>환경을 Azure 가상 네트워크 연결

CloudSimple은 프라이빗 클라우드에 대한 ExpressRoute 회로를 제공합니다. Azure의 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다. 연결 설정에 대한 자세한 내용은 [ExpressRoute를 통해 Azure Virtual Network 연결](./cloudsimple-azure-network-connection.md)의 단계를 따르세요.

## <a name="sign-in-to-vcenter"></a>vCenter에 로그인

이제 vCenter에 로그인하여 가상 머신 및 정책을 설정할 수 있습니다.

1. vCenter에 액세스하려면 CloudSimple 포털에서 시작합니다. 홈페이지의 **일반 작업** 에서  **시작** 을 클릭합니다.  프라이빗 클라우드를 선택하고 프라이빗 클라우드에서 **vSphere Client 시작** 을 클릭합니다.

    ![vSphere Client 시작](media/launch-vcenter-from-cloudsimple-portal.png)

2. 기본 vSphere Client를 선택하여 vCenter에 액세스하고 사용자 이름 및 암호를 사용하여 로그인합니다.  기본값은 다음과 같습니다.
    * 사용자 이름: `CloudOwner@cloudsimple.local`
    * 암호: `CloudSimple123!`

다음 절차의 vCenter 화면은 vSphere(HTML5) Client에서 가져온 것입니다.

## <a name="change-your-vcenter-password"></a>vCenter 암호 변경

CloudSimple은 vCenter에 처음 로그인할 때 암호를 변경하도록 권장합니다.  
설정하는 암호는 다음 요구 사항을 충족해야 합니다.

* 최대 수명: 365일마다 암호를 변경해야 합니다.
* 다시 사용 제한: 이전 5개 암호를 다시 사용할 수 없습니다.
* 길이: 8-20자
* 특수 문자: 하나 이상의 특수 문자
* 영문자: 하나 이상의 대문자, A-Z 및 하나 이상의 소문자, a-z
* 숫자: 하나 이상의 숫자, 0-9
* 동일한 인접 문자의 최대 개수: 3

    예: CC 또는 CCC는 암호의 일부로 허용되지만 CCCC는 허용되지 않습니다.

요구 사항에 맞지 않는 암호를 설정하는 경우:

* vSphere Flash Client를 사용하는 경우 오류가 보고됩니다.
* HTML5 클라이언트를 사용하는 경우 오류가 보고되지 않습니다. 클라이언트는 변경 내용을 수락하지 않으며 이전 암호가 계속 작동합니다.

## <a name="access-nsx-manager"></a>NSX Manager 액세스

NSX Manager는 기본 암호를 사용하여 배포됩니다. 

* 사용자 이름: **admin**
* 암호: **CloudSimple123!**

CloudSimple 포털에서 NSX Manager의 FQDN(정규화된 도메인 이름) 및 IP 주소를 찾을 수 있습니다.

1. CloudSimple 포털을 시작하고 **Resources(리소스)** 를 선택합니다.
2. 사용하려는 프라이빗 클라우드를 클릭합니다.
3. **vSphere 관리 네트워크** 를 선택합니다.
4. **NSX Manager** 의 FQDN 또는 IP 주소를 사용하고 웹 브라우저를 사용하여 연결합니다.

    ![NSX Manager FQDN 찾기](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>포트 그룹 만들기

vSphere에서 분산 포트 그룹을 만들려면

1. [vSphere 네트워킹 가이드](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)의 "분산 포트 그룹 추가"에 포함된 지침을 따릅니다.
2. 분산 포트 그룹을 설정할 때 [워크로드 VM에 대한 VLAN 만들기](#create-a-vlan-for-your-workload-vms)에서 만든 VLAN ID를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](on-premises-connection.md)
* [온-프레미스에서 사이트 간 VPN 설정](vpn-gateway.md)
