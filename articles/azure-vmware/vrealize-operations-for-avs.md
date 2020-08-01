---
title: Azure VMware 솔루션에 대 한 vRealize 작업 설정 (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476071"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Azure VMware 솔루션에 대 한 vRealize 작업 설정 (AVS)


vRealize Operations Manager는 VMware infrastructure 관리자가 시스템 리소스를 모니터링할 수 있도록 하는 운영 관리 플랫폼입니다. 이러한 시스템 리소스는 응용 프로그램 수준 또는 인프라 수준 (실제 및 가상) 개체 일 수 있습니다. 지금까지 대부분의 VMware 관리자는 vRealize 작업을 사용 하 여 vCenter, ESXi, NSX, Vrealize 및 HCX (하이브리드 클라우드 확장)와 같은 VMware 사설 클라우드 구성 요소를 모니터링 하 고 관리 했습니다. 각 AVS 사설 클라우드는 전용 vCenter, NSX, vSAN 및 HCX 배포를 사용 하 여 프로 비전 됩니다. 

먼저 검토 하 고 [필수 구성 요소](#prerequisites) 를 [시작 하기 전에](#before-you-begin) 철저 하 게 검토 합니다. 그런 다음, AVS를 사용 하 여 vRealize Operations Manager에 대 한 두 가지 일반적인 배포 토폴로지를 안내 합니다.

> [!div class="checklist"]
> * [AVS 배포를 관리 하는 온-프레미스 vRealize 작업](#on-premises-vrealize-operations-managing-avs-deployment)
> * [AVS 배포에서 실행 되는 vRealize 작업](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>시작하기 전에
* Vrealize 작업을 배포 하는 방법에 대 한 자세한 내용은 [vrealize Operations Manager 제품 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 를 검토 하세요. 
* 기본 AVS (소프트웨어 정의 데이터 센터) [자습서 시리즈](tutorial-network-checklist.md)를 검토 합니다.
* 필요에 따라 온-프레미스 vRealize 작업 AVS 배포 관리 옵션에 대 한 [Vrealize 작업 원격 컨트롤러](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 제품 설명서를 검토 합니다. 



## <a name="prerequisites"></a>사전 요구 사항
* 온-프레미스와 AVS SDDC 간에 VPN 또는 Azure Express 경로를 구성 해야 합니다.
* Azure에서 AVS 사설 클라우드를 배포 했습니다.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>AVS 배포를 관리 하는 온-프레미스 vRealize 작업
대부분의 고객에 게 하나 이상의 온-프레미스 vCenters를 관리 하는 데 사용 되는 vRealize 작업의 기존 온-프레미스 배포가 있습니다. 고객이 Azure에서 새 AVS 사설 클라우드를 프로 비전 할 때 일반적으로 다음과 같이 Azure Express 경로를 사용 하거나 계층 3 VPN 솔루션을 사용 하 여 온-프레미스 환경을 AVS와 연결 합니다.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="AVS 배포를 관리 하는 온-프레미스 vRealize 작업"  border="false":::

VRealize 작업 기능을 AVS 사설 클라우드로 확장 하려면 avs 사설 클라우드[ 리소스에 대 한 어댑터 인스턴스](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) 를 만들어 avs 사설 클라우드에서 데이터를 수집 하 고 온-프레미스 Vrealize 작업으로 가져옵니다. 온-프레미스 vRealize Operations Manager 인스턴스는 AVS의 vCenter 및 NSX Manager에 직접 연결할 수 있으며, 필요에 따라 AVS 사설 클라우드에 vRealize 작업 원격 수집기를 배포할 수 있습니다. VRealize 작업 원격 수집기는 Express 경로 또는 VPN 네트워크를 통해 온-프레미스에서 실행 되는 vRealize Operations Manager으로 전송 되기 전에 AVS 사설 클라우드에서 수집 된 데이터를 압축 하 고 암호화 합니다. 

> [!TIP]
> VRealize Operations Manager를 설치 하는 단계별 가이드는 [VMware 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 를 참조 하세요. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>AVS 배포에서 실행 되는 vRealize 작업

다른 배포 옵션은 아래와 같이 AVS 사설 클라우드의 Vrealize 클러스터 중 하나에서 vRealize Operations Manager 인스턴스를 배포 하는 것입니다. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="AVS에서 실행 되는 vRealize 작업" border="false":::

VRealize 작업의 AVS 인스턴스를 배포한 후 vCenter, ESXi, NSX, Vrealize 및 HCX에서 데이터를 수집 하도록 vRealize 작업을 구성할 수 있습니다. 

> [!TIP]
> VRealize Operations Manager를 설치 하는 단계별 가이드는 [VMware 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 를 참조 하세요.




<!-- LINKS - external -->


<!-- LINKS - internal -->




