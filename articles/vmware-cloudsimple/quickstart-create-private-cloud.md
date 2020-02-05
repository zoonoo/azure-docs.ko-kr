---
title: Azure VMware 솔루션 (AVS) 빠른 시작-AVS 사설 클라우드 만들기
description: Azure VMware 솔루션 (AVS)을 사용 하 여 AVS 사설 클라우드를 만들고 구성 하는 방법을 알아봅니다.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018570"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>빠른 시작-AVS 사설 클라우드 환경 구성

이 문서에서는 AVS 사설 클라우드를 만들고, AVS 사설 클라우드 환경을 설정 하는 방법에 대해 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[네트워킹 필수 구성 요소](cloudsimple-network-checklist.md)를 검토 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-an-avs-private-cloud"></a>AVS 사설 클라우드 만들기

AVS 사설 클라우드는 ESXi 호스트, vCenter, vSAN 및 NSX를 지 원하는 격리 된 VMware 스택입니다.

Avs 사설 클라우드는 AVS 포털을 통해 관리 됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다. 스택은 전용 노드 및 isolated 운영 체제 미 설치 하드웨어 노드에서 실행 됩니다.

1. **모든 서비스**를 선택합니다.
2. **AVS 서비스**를 검색 합니다.
3. AVS 사설 클라우드를 만들려는 AVS 서비스를 선택 합니다.
4. **개요**에서 **Avs 사설 클라우드 만들기** 를 클릭 하 여 avs 포털에 대 한 새 브라우저 탭을 엽니다. 메시지가 표시 되 면 Azure 로그인 자격 증명을 사용 하 여 로그인 합니다. 

    ![Azure에서 AVS 사설 클라우드 만들기](media/create-private-cloud-from-azure.png)

5. AVS 포털에서 AVS 사설 클라우드의 이름을 제공 합니다.
6. AVS 사설 클라우드의 **위치** 를 선택 합니다.
7. Azure에서 프로 비전 한 것과 일치 하는 **노드 유형**을 선택 합니다.
8. **노드 수**를 지정 합니다. AVS 사설 클라우드를 만들려면 노드가 세 개 이상 필요 합니다.

    ![AVS 사설 클라우드 만들기-기본 정보](media/create-private-cloud-basic-info.png)

9. **다음: 고급 옵션**을 클릭 합니다.
10. VSphere/Vsphere 서브넷의 CIDR 범위를 입력 합니다. CIDR 범위가 온-프레미스 또는 기타 Azure 서브넷 (가상 네트워크) 또는 게이트웨이 서브넷과 겹치지 않는지 확인 합니다.

    **CIDR 범위 옵션:** /24,/23,/22 또는/21. /24 CIDR 범위는 최대 26 개의 노드를 지원 하 고/23 CIDR 범위는 최대 58 노드를 지원 하며,/22 및/21 CIDR 범위는 64 노드 (AVS 사설 클라우드의 최대 노드 수)를 지원 합니다. Vlan 및 서브넷에 대 한 자세한 내용은 [vlan 및 서브넷 개요](cloudsimple-vlans-subnets.md)를 참조 하세요.

      > [!IMPORTANT]
      > VSphere/Vsphere CIDR 범위의 IP 주소는 AVS 사설 클라우드 인프라에 사용 하도록 예약 되어 있습니다. 가상 머신에서는이 범위의 IP 주소를 사용 하지 마세요.

11. **다음: 검토 및 만들기**를 클릭 합니다.
12. 설정을 검토 합니다. 설정을 변경 해야 하는 경우에는 **이전**을 클릭 합니다.
13. **만들기**를 클릭합니다.

AVS 사설 클라우드 프로 비전 프로세스가 시작 됩니다. AVS 사설 클라우드를 프로 비전 하는 데 최대 2 시간이 걸릴 수 있습니다.

## <a name="launch-avs-portal"></a>AVS 포털 시작

Azure Portal에서 AVS 포털에 액세스할 수 있습니다. AVS 포털은 SSO (Single Sign-on)를 사용 하 여 Azure 로그인 자격 증명으로 시작 됩니다. AVS 포털에 액세스 하려면 **Avs 서비스 권한 부여** 응용 프로그램에 권한을 부여 해야 합니다. 권한 부여에 대 한 자세한 내용은 [AVS 서비스 권한 부여 응용 프로그램에 동의](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application)를 참조 하세요.

1. **모든 서비스**를 선택합니다.
2. **AVS 서비스**를 검색 합니다.
3. AVS 사설 클라우드를 만들려는 AVS 서비스를 선택 합니다.
4. 개요에서 **avs 포털로 이동** 을 클릭 하 여 avs 포털에 대 한 새 브라우저 탭을 엽니다. 메시지가 표시 되 면 Azure 로그인 자격 증명을 사용 하 여 로그인 합니다. 

    ![AVS 포털 시작](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>지점 및 사이트 간 VPN 만들기

지점 및 사이트 간 VPN 연결은 컴퓨터에서 AVS 사설 클라우드에 연결 하는 가장 간단한 방법입니다. 원격으로 AVS 사설 클라우드에 연결 하는 경우 지점 및 사이트 간 VPN 연결을 사용 합니다. AVS 사설 클라우드에 빠르게 액세스 하려면 다음 단계를 수행 합니다. 온-프레미스 네트워크에서의 AVS 영역에 대 한 액세스는 [사이트 간 VPN](vpn-gateway.md) 또는 [Azure express](on-premises-connection.md)경로를 사용 하 여 수행할 수 있습니다.

### <a name="create-gateway"></a>게이트웨이 만들기

1. AVS 포털을 시작 하 고 **네트워크**를 선택 합니다.
2. **VPN Gateway**를 선택 합니다.
3. **새 VPN Gateway**을 클릭 합니다.

    ![VPN 게이트웨이 만들기](media/create-vpn-gateway.png)

4. **게이트웨이 구성**의 경우 다음 설정을 지정 하 고 **다음**을 클릭 합니다.

    * **지점 및 사이트 간 VPN** 을 게이트웨이 유형으로 선택 합니다.
    * 게이트웨이를 식별 하는 이름을 입력 합니다.
    * AVS 서비스가 배포 되는 Azure 위치를 선택 합니다.
    * 지점 및 사이트 간 게이트웨이에 대 한 클라이언트 서브넷을 지정 합니다. 연결할 때이 서브넷에서 DHCP 주소가 제공 됩니다.

5. **연결/사용자**에 대해 다음 설정을 지정 하 고 **다음**을 클릭 합니다.

    * 현재 및 미래의 모든 사용자가이 지점 및 사이트 간 게이트웨이를 통해 AVS 사설 클라우드에 액세스 하도록 자동으로 허용 하려면 **모든 사용자를 자동으로 추가**를 선택 합니다. 이 옵션을 선택 하면 사용자 목록의 모든 사용자가 자동으로 선택 됩니다. 목록에서 개별 사용자의 선택을 취소 하 여 자동 옵션을 재정의할 수 있습니다.
    * 개별 사용자만 선택 하려면 사용자 목록에서 확인란을 클릭 합니다.

6. Vlan/서브넷 섹션에서 게이트웨이 및 연결에 대 한 관리 및 사용자 Vlan/서브넷을 지정할 수 있습니다.

    * **자동으로 추가** 옵션은이 게이트웨이에 대 한 전역 정책을 설정 합니다. 설정은 현재 게이트웨이에 적용 됩니다. 설정은 **선택** 영역에서 재정의할 수 있습니다.
    * **AVS 사설 클라우드의 관리 vlan/서브넷 추가**를 선택 합니다.
    * 모든 사용자 정의 Vlan/서브넷을 추가 하려면 **사용자 정의 vlan/서브넷 추가**를 클릭 합니다.
    * **Select** 설정은 **자동 추가**에서 전역 설정을 재정의 합니다.

7. **다음** 을 클릭 하 여 설정을 검토 합니다. 편집 아이콘을 클릭 하 여 변경 합니다.
8. **만들기** 를 클릭 하 여 VPN gateway를 만듭니다.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>지점 및 사이트 간 VPN을 사용 하 여 AVS에 연결

VPN 클라이언트는 컴퓨터에서 AVS에 연결 하는 데 필요 합니다. Windows 용 [Openvpn 클라이언트](https://openvpn.net/community-downloads/) 또는 macos 및 OS X 용 [Viscosity](https://www.sparklabs.com/viscosity/download/) 를 다운로드 합니다.

1. AVS 포털을 시작 하 고 **네트워크**를 선택 합니다.
2. **VPN Gateway**를 선택 합니다.
3. VPN gateway 목록에서 지점 및 사이트 간 VPN gateway를 클릭 합니다.
4. **사용자**를 선택합니다.
5. **내 VPN 구성 다운로드**를 클릭 합니다.

    ![VPN 구성 다운로드](media/download-p2s-vpn-configuration.png)

6. VPN 클라이언트에서 구성을 가져옵니다.

    * [Windows 클라이언트에서 구성 가져오기](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) 에 대 한 지침
    * [Macos 또는 OS X에서 구성을 가져오는](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) 방법에 대 한 지침

7. AVS에 연결 합니다.

## <a name="create-a-vlan-for-your-workload-vms"></a>워크 로드 Vm에 대 한 VLAN 만들기

AVS 사설 클라우드를 만든 후 작업/응용 프로그램 Vm을 배포할 VLAN을 만듭니다.

1. AVS 포털에서 **네트워크**를 선택 합니다.
2. **VLAN/서브넷**을 클릭 합니다.
3. **VLAN/서브넷 만들기**를 클릭 합니다.

    ![VLAN/서브넷 만들기](media/create-new-vlan-subnet.png)

4. 새 VLAN/서브넷에 대해 **AVS 사설 클라우드** 를 선택 합니다.
5. 목록에서 VLAN ID를 선택 합니다. 
6. 서브넷을 식별 하는 서브넷 이름을 입력 합니다.
7. 서브넷 CIDR 범위와 마스크를 지정 하십시오. 이 범위는 기존 서브넷과 겹치지 않아야 합니다.
8. **제출**을 클릭합니다.

    ![VLAN/서브넷 세부 정보 만들기](media/create-new-vlan-subnet-details.png)

V m/서브넷이 생성 됩니다. 이제이 VLAN ID를 사용 하 여 AVS 사설 클라우드 vCenter에 분산 포트 그룹을 만들 수 있습니다.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Azure virtual network에 환경 연결

AVS는 AVS 사설 클라우드에 대 한 Express 경로 회로를 제공 합니다. Azure의 가상 네트워크를 Express 경로 회로에 연결할 수 있습니다. 연결 설정에 대 한 자세한 내용은 Express 경로를 [사용 하 여 Azure Virtual Network 연결](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)의 단계를 따르세요.

## <a name="sign-in-to-vcenter"></a>VCenter에 로그인

이제 vCenter에 로그인 하 여 가상 머신 및 정책을 설정할 수 있습니다.

1. VCenter에 액세스 하려면 AVS 포털에서 시작 합니다. 홈 페이지의 **일반 작업**에서 **Vsphere 클라이언트 시작**을 클릭 합니다. AVS 사설 클라우드를 선택 하 고 AVS 사설 클라우드에서 **vSphere 클라이언트 시작** 을 클릭 합니다.

    ![VSphere 클라이언트 시작](media/launch-vcenter-from-cloudsimple-portal.png)

2. VCenter에 액세스 하 고 사용자 이름 및 암호를 사용 하 여 로그인 할 기본 vSphere 클라이언트를 선택 합니다. 기본값은 다음과 같습니다.
    * 사용자 이름: **CloudOwner@AVS.local**
    * 암호: **AVS123!**  

다음 절차의 vCenter 화면은 vSphere (HTML5) 클라이언트에서 가져온 것입니다.

## <a name="change-your-vcenter-password"></a>VCenter 암호 변경

AVS는 vCenter에 처음 로그인 할 때 암호를 변경 하는 것을 권장 합니다. 사용자가 설정 하는 암호는 다음 요구 사항을 충족 해야 합니다.

* 최대 수명: 365 일 마다 암호를 변경 해야 합니다.
* 다시 사용 제한: 사용자는 이전의 5 개 암호를 다시 사용할 수 없습니다.
* 길이: 8-20 문자
* 특수 문자: 하나 이상의 특수 문자
* 영문자: 하나 이상의 대문자, a-z 및 하나 이상의 소문자, a-z
* 숫자: 하나 이상의 숫자 문자, 0-9
* 최대 동일한 인접 문자: 3

    예: CC 또는 CCC는 암호의 일부로 허용 되지만 CCCC는 허용 되지 않습니다.

요구 사항에 맞지 않는 암호를 설정 하는 경우:

* vSphere 플래시 클라이언트를 사용 하는 경우 오류를 보고 합니다.
* HTML5 클라이언트를 사용 하는 경우 오류를 보고 하지 않습니다. 클라이언트는 변경 내용을 수락 하지 않으며 이전 암호는 계속 작동 합니다.

## <a name="access-nsx-manager"></a>NSX manager 액세스

NSX manager는 기본 암호를 사용 하 여 배포 됩니다. 

* 사용자 이름: **관리자**
* 암호: **AVS123!**

AVS 포털에서 NSX manager의 FQDN (정규화 된 도메인 이름) 및 IP 주소를 찾을 수 있습니다.

1. AVS 포털을 시작 하 고 **리소스**를 선택 합니다.
2. 사용 하려는 AVS 사설 클라우드를 클릭 합니다.
3. **Vsphere 관리 네트워크** 를 선택 합니다.
4. **NSX Manager** 의 FQDN 또는 IP 주소를 사용 하 고 웹 브라우저를 사용 하 여 연결 합니다.

    ![NSX Manager FQDN 찾기](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>포트 그룹 만들기

VSphere 분산 포트 그룹을 만들려면 다음을 수행 합니다.

1. [Vsphere 네트워킹 가이드](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)의 "분산 포트 그룹 추가"의 지침을 따릅니다.
2. 분산 포트 그룹을 설정할 때 [작업 vm에 대 한 Vlan 만들기](#create-a-vlan-for-your-workload-vms)에서 만든 vlan ID를 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure Express 경로를 사용 하 여 온-프레미스 네트워크에 연결](on-premises-connection.md)
* [온-프레미스에서 사이트 간 VPN 설정](vpn-gateway.md)
