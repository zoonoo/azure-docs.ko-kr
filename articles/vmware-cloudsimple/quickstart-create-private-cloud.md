---
title: CloudSimple 빠른 시작-azure의 VMware 솔루션 사설 클라우드 만들기
description: 만들기 및 Azure CloudSimple VMware 솔루션을 사용 하 여 사설 클라우드를 구성 하는 방법을 알아봅니다
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e1fc7809ad94d589483b87c638d027a39098164e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209549"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>빠른 시작-사설 클라우드 환경 구성

이 문서에서는 CloudSimple 사설 클라우드 만들기 및 사설 클라우드 환경을 설정 하는 방법에 알아봅니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

1. **모든 서비스**를 선택합니다.
2. 검색할 **CloudSimple 서비스**합니다.
3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.
4. 개요에서 클릭 **사설 클라우드 만들기** 를 CloudSimple 포털에 대 한 새 브라우저 탭을 엽니다.  메시지가 표시 되 면 Azure 로그인 자격 증명에 로그인 합니다.  

    ![Azure에서 사설 클라우드 만들기](media/create-private-cloud-from-azure.png)

5. CloudSimple 포털에서 사설 클라우드 이름과
6. 선택 된 **위치** 사설 클라우드
7. 선택 합니다 **노드 형식** azure 구입 합니다.  선택할 수 있습니다 합니다 [CS28 또는 CS36 옵션](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku)합니다. 후자는 최대 계산 및 메모리 용량을 포함합니다.
8. 지정 된 **노드 수**입니다.  사설 클라우드를 만드는 데 필요한 최소 3 개 노드

    ![기본 정보-사설 클라우드 만들기](media/create-private-cloud-basic-info.png)

9. **다음: 고급 옵션**합니다.
10. VSphere/vSAN 서브넷의 CIDR 범위를 입력 합니다. 온-프레미스 또는 다른 Azure 서브넷 중 하나를 사용 하 여 CIDR 범위가 겹치지 않는지 확인 합니다.

    ![고급 옵션-사설 클라우드 만들기](media/create-private-cloud-advanced-options.png)

11. 완료되면 **다음: 검토 및 만들기**합니다.
12. 설정을 검토 합니다. 설정을 변경 해야 할 경우 클릭 **이전**합니다.
13. **만들기**를 클릭합니다.

사설 클라우드를 프로 비전 프로세스 시작 됩니다.  사설 클라우드를 프로 비전에 대 한 2 시간 까지를 걸릴 수 있습니다.

## <a name="launch-cloudsimple-portal"></a>CloudSimple 포털 시작

Azure portal에서 CloudSimple 포털에 액세스할 수 있습니다.  Azure를 사용 하 여 CloudSimple 포털을 시작할에서 Single sign-on (SSO)를 사용 하 여 자격 증명에 로그인 합니다.  에서는 CloudSimple 포털에 액세스 권한을 부여 해야 합니다 **CloudSimple 서비스 권한 부여** 응용 프로그램입니다.  권한 부여에 대 한 자세한 내용은 참조 하세요. [CloudSimple 서비스 권한 부여 응용 프로그램에 동의](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. **모든 서비스**를 선택합니다.
2. 검색할 **CloudSimple 서비스**합니다.
3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.
4. 개요에서 클릭 **CloudSimple 포털로 이동** 를 CloudSimple 포털에 대 한 새 브라우저 탭을 엽니다.  메시지가 표시 되 면 Azure 로그인 자격 증명에 로그인 합니다.  

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>지점-사이트 간 VPN 만들기

지점-사이트 간 VPN 연결을 간단 하 게 컴퓨터에서 사설 클라우드를 연결할 경우 사설 클라우드를 원격으로 연결 하는 경우 지점-사이트 간 VPN 연결을 사용 합니다.  사설 클라우드를 빠른 액세스를 위해 다음 단계를 수행 합니다.  온-프레미스 네트워크에서 CloudSimple 지역에 대 한 액세스를 수행할 수 있습니다 사용 하 여 [사이트 간 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/) 하거나 [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)합니다.

### <a name="create-gateway"></a>게이트웨이 만들기

1. 포털 및 선택 CloudSimple 시작 **네트워크**합니다.
2. 선택 **VPN Gateway**합니다.
3. 클릭 **새 VPN Gateway**합니다.

    ![VPN 게이트웨이 만들기](media/create-vpn-gateway.png)

4. 에 대 한 **게이트웨이 구성**, 다음 설정을 지정 하 고 클릭 **다음**합니다.

    * 선택 **지점 및 사이트 간 VPN** 를 게이트웨이 유형으로 합니다.
    * 게이트웨이 식별 하는 이름을 입력 합니다.
    * CloudSimple 서비스를 배포할 Azure 위치를 선택 합니다.
    * 지점-사이트 간 게이트웨이에 대 한 클라이언트 서브넷을 지정 합니다.  연결할 때이 서브넷의 DHCP 주소가 제공 됩니다.

5. 에 대 한 **연결/사용자**, 다음 설정을 지정 하 고 클릭 **다음**합니다.

    * 이 지점-사이트 간 게이트웨이 통해 사설 클라우드에 액세스 하려면 모든 현재 및 미래의 사용자를 자동으로 허용 하려면 선택 **모든 사용자를 자동으로 추가**합니다. 이 옵션을 선택 하면 모든 사용자가 사용자 목록에 자동으로 선택 됩니다. 목록에서 개별 사용자를 선택 취소 하 여 자동 옵션을 재정의할 수 있습니다.
    * 개별 사용자만을 선택 하려면 사용자 목록에 있는 확인란을 클릭 합니다.

6. Vlan/서브넷 섹션을 사용 하면 관리 및 게이트웨이 및 연결에 대 한 서브넷 Vlan/사용자를 지정할 수 있습니다.

    * 합니다 **자동으로 추가** 옵션이이 게이트웨이에 대 한 전역 정책을 설정 합니다. 현재 게이트웨이에 적용 됩니다. 설정을 재정의할 수 있습니다 합니다 **선택** 영역입니다.
    * 선택 **Vlan/서브넷 사설 클라우드의 관리 추가**합니다. 
    * 모든 사용자 정의 Vlan/서브넷을 추가 하려면 클릭 **사용자 정의 Vlan/서브넷 추가**합니다. 
    * 합니다 **선택** 아래에 있는 전역 설정은 설정 **자동으로 추가**합니다. 

7. 클릭 **다음** 설정을 검토 합니다. 변경 하려면 편집 아이콘을 클릭 합니다.
8. 클릭 **만들기** VPN 게이트웨이를 만듭니다.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>지점-사이트 간 VPN을 사용 하 여 CloudSimple에 연결

VPN 클라이언트 컴퓨터에서 CloudSimple에 연결 하기 위한 필요 합니다.  다운로드 [OpenVPN 클라이언트](https://openvpn.net/community-downloads/) Windows에 대 한 나 [마찰력](https://www.sparklabs.com/viscosity/download/) macOS 및 OS X에 대 한 합니다.

1. 포털 및 선택 CloudSimple 시작 **네트워크**합니다.
2. 선택 **VPN Gateway**합니다.
3. VPN gateway의 목록에서 지점-사이트 간 VPN gateway를 클릭 합니다.
4. **사용자**를 선택합니다.
5. 클릭 **내 VPN 구성 다운로드**

    ![VPN 구성 다운로드](media/download-p2s-vpn-configuration.png)

6. VPN 클라이언트 구성을 가져옵니다.

    * 에 대 한 지침 [Windows 클라이언트에서 구성 가져오기](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * 에 대 한 지침 [macOS 또는 OS X 구성 가져오기](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. CloudSimple에 연결

## <a name="create-a-vlan-for-your-workload-vms"></a>Vm 워크 로드에 대 한 VLAN 만들기

사설 클라우드를 만든 후 워크 로드/응용 프로그램 Vm을 배포할 VLAN을 만듭니다.

1. CloudSimple 포털에서 선택 **네트워크**합니다.
2. 클릭 **VLAN/서브넷**합니다.
3. 클릭 **VLAN/서브넷 만들기**

    ![VLAN/서브넷 만들기](media/create-new-vlan-subnet.png)

4. 선택 된 **사설 클라우드** 새 VLAN/서브넷에 대 한 합니다.
5. 목록에서 VLAN ID를 선택 합니다.  
6. 서브넷을 식별 하는 서브넷 이름을 입력 합니다.
7. 서브넷 CIDR 범위 및 마스크를 지정 합니다.  이 범위는 모든 기존 서브넷으로 겹치면 안 됩니다.
8. **제출**을 클릭합니다.

    ![VLAN/서브넷 세부 정보](media/create-new-vlan-subnet-details.png)

VLAN/서브넷이 생성 됩니다.  이제 사설 클라우드 vCenter에서 분산된 포트 그룹을 만들려면이 VLAN ID를 사용할 수 있습니다. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>환경의 Azure virtual network에 연결

CloudSimple 사설 클라우드를 위한 ExpressRoute 회로 제공합니다. Azure에서 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다. 단계에 따라 전체에 대 한 내용은 연결 설정 [ExpressRoute를 사용 하 여 Azure 가상 네트워크 연결](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>VCenter에 로그인

수 이제 로그인 할 vcenter 가상 머신과 정책을 설정 하려면.

1. VCenter에 액세스 하려면 CloudSimple 포털에서 시작 합니다. 홈 페이지에서 아래 **일반적인**, 클릭 **vSphere 클라이언트를 시작**합니다.  사설 클라우드를 선택 하 고 클릭 **vSphere 클라이언트를 시작** 사설 클라우드에서 합니다.

    ![VSphere 클라이언트를 시작 합니다.](media/launch-vcenter-from-cloudsimple-portal.png)

2. VCenter 액세스 하 여 사용자 이름 및 암호를 로그인 하 여 기본 vSphere 클라이언트를 선택 합니다.  기본값은 다음과 같습니다.
    * 사용자 이름: **CloudOwner@cloudsimple.local**
    * 암호: **CloudSimple123!**  

VSphere (HTML5) 클라이언트에서 다음 절차에서 vCenter 화면입니다.

## <a name="change-your-vcenter-password"></a>VCenter 암호 변경

CloudSimple는 암호를 변경 하면 처음 vCenter에 로그인 하는 것이 좋습니다.  
설정 하는 암호에는 다음 요구 사항을 충족 해야 합니다.

* 최대 수명: 암호를 변경 해야 365 일 마다
* 재사용을 제한 합니다. 사용자가 이전 5 개의 암호를 다시 사용할 수 없습니다.
* 소요 시간: 8-20 자
* 특수 문자: 하나 이상의 특수 문자
* 알파벳 문자: 하나 이상의 대문자, A-Z, 및 하나 이상의 소문자 문자: a-z
* 숫자: 하나 이상의 숫자 문자, 0-9
* 최대 동일한 인접 문자: 3

    예제: CC 또는 CCC, 암호의 일부로 허용 되었지만 CCCC 되지 않습니다.

암호를 설정 하는 경우에 요구 사항을 충족 하지 않는:

* 오류를 보고 vSphere 플래시 클라이언트를 사용 하는 경우
* HTML5 클라이언트를 사용 하는 경우에 오류를 보고 하지 않습니다. 클라이언트에는 변경 내용을 적용 하지 않습니다 하 고 이전 암호를 계속 작동 합니다.

## <a name="change-nsx-administrator-password"></a>NSX 관리자 암호 변경

NSX 관리자는 기본 암호를 사용 하 여 배포 됩니다.  사설 클라우드를 만든 후 암호를 변경 하는 것이 좋습니다.

   * 사용자 이름: **관리자**
   * 암호: **CloudSimple123!**

CloudSimple 포털에서 정규화 된 도메인 이름 (FQDN) 및 NSX 관리자의 IP 주소를 찾을 수 있습니다.

1. 포털 및 선택 CloudSimple 시작 **리소스**합니다.
2. 사용 하려는 사설 클라우드를 클릭 합니다.
3. 선택 **vSphere 관리 네트워크**
4. FQDN 또는 IP 주소를 사용 하 여 **NSX Manager** 웹 브라우저를 사용 하 여 연결 합니다. 

    ![NSX 관리자 FQDN을 찾으려면](media/private-cloud-nsx-manager-fqdn.png)

암호를 변경 하려면의 지침을 따릅니다 [NSX Manager 설치](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html)합니다.

## <a name="create-a-port-group"></a>포트 그룹 만들기

Vsphere에서 분산된 포트 그룹을 만들려면

1. "분산된 포트 그룹을 추가"의 지침에 따라 합니다 [vSphere 네트워킹 가이드](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)합니다.
2. 만든 VLAN ID를 제공 하는 분산된 포트 그룹을 설정할 때 [워크 로드 Vm에 대 한 VLAN 만들기](#create-a-vlan-for-your-workload-vms)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute를 사용 하 여 온-프레미스 네트워크에 연결](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [온-프레미스에서 사이트 간 VPN 설정](https://docs.azure.cloudsimple.com/vpn-gateway/)
