---
title: Azure VMware 솔루션에 대 한 vRealize 작업 설정
description: Azure VMware 솔루션 사설 클라우드에 대해 vRealize 작업을 설정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580400"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 vRealize 작업 설정


vRealize Operations Manager는 VMware infrastructure 관리자가 시스템 리소스를 모니터링할 수 있도록 하는 운영 관리 플랫폼입니다. 이러한 시스템 리소스는 응용 프로그램 수준 또는 인프라 수준 (실제 및 가상) 개체 일 수 있습니다. 지금까지 대부분의 VMware 관리자는 vRealize 작업을 사용 하 여 vCenter, ESXi, NSX, Vrealize 및 HCX (하이브리드 클라우드 확장)와 같은 VMware 사설 클라우드 구성 요소를 모니터링 하 고 관리 했습니다. 각 Azure VMware 솔루션 사설 클라우드는 전용 vCenter, NSX, vSAN 및 HCX 배포를 사용 하 여 프로 비전 됩니다. 

먼저 검토 하 고 [필수 구성 요소](#prerequisites) 를 [시작 하기 전에](#before-you-begin) 철저 하 게 검토 합니다. 그런 다음 Azure VMware 솔루션을 사용 하 여 vRealize Operations Manager에 대 한 두 가지 일반적인 배포 토폴로지를 안내 합니다.

> [!div class="checklist"]
> * [Azure VMware 솔루션 배포를 관리 하는 온-프레미스 vRealize 작업](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Azure VMware 솔루션 배포에서 실행 되는 vRealize 작업](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>시작하기 전에
* Vrealize 작업을 배포 하는 방법에 대 한 자세한 내용은 [vrealize Operations Manager 제품 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 를 검토 하세요. 
* 기본 Azure VMware 솔루션 SDDC (소프트웨어 정의 데이터 센터) [자습서 시리즈](tutorial-network-checklist.md)를 검토 합니다.
* 필요에 따라 온-프레미스 vRealize 작업 Azure VMware 솔루션 배포 관리 옵션에 대 한 [Vrealize 작업 원격 컨트롤러](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 제품 설명서를 검토 합니다. 



## <a name="prerequisites"></a>사전 요구 사항
* 온-프레미스와 Azure VMware 솔루션 SDDC 간에 VPN 또는 Azure Express 경로를 구성 해야 합니다.
* Azure에 배포 된 Azure VMware 솔루션 사설 클라우드.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware 솔루션 배포를 관리 하는 온-프레미스 vRealize 작업
대부분의 고객에 게 하나 이상의 온-프레미스 vCenters를 관리 하는 데 사용 되는 vRealize 작업의 기존 온-프레미스 배포가 있습니다. 고객이 Azure에서 새로운 Azure VMware 솔루션 사설 클라우드를 프로 비전 하는 경우 일반적으로 아래와 같이 Azure Express 경로 또는 계층 3 VPN 솔루션을 사용 하 여 온-프레미스 환경을 Azure VMware 솔루션과 연결 합니다.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware 솔루션 배포를 관리 하는 온-프레미스 vRealize 작업"  border="false":::

VRealize 작업 기능을 Azure VMware 솔루션 사설 클라우드로 확장 하려면 azure vmware 솔루션 사설 클라우드 [리소스에 대 한 어댑터 인스턴스](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) 를 만들어 Azure vmware 솔루션 사설 클라우드에서 데이터를 수집 하 고 온-프레미스 Vrealize 작업으로 가져옵니다. 온-프레미스 vRealize Operations Manager 인스턴스는 Azure VMware 솔루션의 vCenter 및 NSX Manager에 직접 연결할 수 있으며, 필요에 따라 Azure VMware 솔루션 사설 클라우드에 vRealize 작업 원격 수집기를 배포할 수도 있습니다. VRealize 작업 원격 수집기는 Express 경로 또는 VPN 네트워크를 통해 온-프레미스에서 실행 되는 vRealize Operations Manager으로 전송 되기 전에 Azure VMware 솔루션 사설 클라우드에서 수집 된 데이터를 압축 하 고 암호화 합니다. 

> [!TIP]
> VRealize Operations Manager를 설치 하는 단계별 가이드는 [VMware 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 를 참조 하세요. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Azure VMware 솔루션 배포에서 실행 되는 vRealize 작업

또 다른 배포 옵션은 아래와 같이 Azure VMware 솔루션 사설 클라우드의 Vrealize 클러스터 중 하나에서 vRealize Operations Manager 인스턴스를 배포 하는 것입니다. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware 솔루션 배포를 관리 하는 온-프레미스 vRealize 작업" border="false":::

VRealize 작업의 Azure VMware 솔루션 인스턴스를 배포한 후 vCenter, ESXi, NSX, Vrealize 및 HCX에서 데이터를 수집 하도록 vRealize 작업을 구성할 수 있습니다. 

> [!TIP]
> VRealize Operations Manager를 설치 하는 단계별 가이드는 [VMware 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 를 참조 하세요.


## <a name="known-limitations"></a>알려진 제한 사항

- **cloudadmin@vsphere.local**Azure VMware 솔루션의 사용자에 게는 [제한 된 권한이](concepts-role-based-access-control.md)있습니다. VMware 도구를 사용 하는 게스트 내 메모리 수집은 Azure VMware 솔루션의 Vm (가상 머신)에서 지원 되지 않습니다. 이 경우 활성 및 사용 된 메모리 사용률은 계속 작동 합니다.
- Azure VMware 솔루션은 DRS 설정을 포함 하 여 클러스터 구성을 관리 하므로 호스트 기반 비즈니스 의도에 대 한 워크 로드 최적화는 작동 하지 않습니다.
- 클러스터 기반 비즈니스 의도를 사용 하 여 SDDC 내 클러스터 간 배치에 대 한 워크 로드 최적화는 vRealize Operations Manager 8.0 및 그 이후부터 완벽 하 게 지원 됩니다. 그러나 워크 로드 최적화는 리소스 풀을 인식 하지 못하므로 가상 컴퓨터를 클러스터 수준에 배치 합니다. 사용자는 Azure VMware 솔루션 vCenter Server 인터페이스에서 수동으로이 문제를 해결할 수 있습니다.
- Azure VMware 솔루션 vCenter Server 자격 증명을 사용 하 여 vRealize Operations Manager에 로그인 할 수 없습니다. 
- Azure VMware 솔루션은 vRealize Operations Manager 플러그 인을 지원 하지 않습니다.

VCenter Server 클라우드 계정을 사용 하 여 Azure VMware Solution vCenter를 vRealize Operations Manager에 연결 하는 경우 다음과 같은 경고가 발생 합니다.

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Azure VMware 솔루션 배포를 관리 하는 온-프레미스 vRealize 작업":::

이 경고는 **cloudadmin@vsphere.local** Azure VMware 솔루션의 사용자에 게 등록에 필요한 모든 vCenter Server 작업을 수행할 수 있는 충분 한 권한이 없기 때문에 발생 합니다. 그러나 아래와 같이 어댑터 인스턴스에서 데이터 수집을 수행 하는 데에는 권한이 충분 합니다.

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Azure VMware 솔루션 배포를 관리 하는 온-프레미스 vRealize 작업":::

자세한 내용은 [VCenter 어댑터 인스턴스를 구성 하는 데 필요한 권한](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)을 참조 하세요.

<!-- LINKS - external -->


<!-- LINKS - internal -->




