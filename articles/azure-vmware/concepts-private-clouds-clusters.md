---
title: 개념 - 프라이빗 클라우드와 클러스터
description: Azure VMware Solution 소프트웨어 정의 데이터 센터 및 vSphere 클러스터의 주요 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: aff66e01ae4b056eb082d2000611718b1a5cf66a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773926"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution 프라이빗 클라우드 및 클러스터 개념

Azure VMware Solution은 Azure에서 VMware 기반 프라이빗 클라우드를 제공합니다. 프라이빗 클라우드에는 운영 체제 미설치 전용 Azure 호스트로 빌드된 클러스터가 포함됩니다. 이는 Azure Portal, CLI 또는 PowerShell을 통해 배포되고 관리됩니다.  프라이빗 클라우드에 프로비저닝된 클러스터에는 VMware vSphere, vCenter, vSAN 및 NSX 소프트웨어가 포함됩니다. Azure에서 Azure VMware Solution 프라이빗 클라우드 하드웨어 및 소프트웨어 배포를 완벽하게 통합하고 자동화할 수 있습니다.

Azure 구독, Azure VMware Solution 프라이빗 클라우드, vSAN 클러스터 및 호스트 간에는 논리적 관계가 있습니다. 이 다이어그램은 개발 및 프로덕션 환경을 나타내는 두 프라이빗 클라우드를 포함하는 단일 Azure 구독에 대해 보여 줍니다.  각 프라이빗 클라우드에서는 두 개의 클러스터가 있습니다. 

이 문서에서는 이러한 개념에 대해 모두 설명합니다.

![고객 구독의 두 프라이빗 클라우드 이미지](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>프라이빗 클라우드

프라이빗 클라우드에는 운영 체제 미설치 상태인 전용 Azure 호스트로 빌드된 vSAN 클러스터가 포함됩니다. 각 프라이빗 클라우드에는 동일한 vCenter 서버 및 NSX-T Manager에서 관리하는 여러 클러스터가 있을 수 있습니다. 포털, CLI 또는 PowerShell에서 프라이빗 클라우드를 배포하고 관리할 수 있습니다. 

다른 리소스와 마찬가지로 프라이빗 클라우드는 Azure 구독 내에서 설치되고 관리됩니다. 구독 내의 프라이빗 클라우드 수는 스케일링 가능합니다. 처음에는 구독 당 하나의 프라이빗 클라우드로 제한됩니다.

## <a name="clusters"></a>클러스터
만들어진 프라이빗 클라우드 하나마다 기본적으로 하나의 vSAN 클러스터가 있습니다. Azure Portal을 사용하거나 API를 통해 클러스터를 추가, 삭제 및 스케일링할 수 있습니다.  모든 클러스터의 기본 크기는 세 개의 호스트로 구성되며 최대 16개의 호스트로 스케일 업할 수 있습니다. 프라이빗 클라우드 당 최대 4개의 클러스터를 사용할 수 있습니다.

평가판 클러스터는 평가용으로 제공되며 호스트 수는 3개로 제한됩니다. 프라이빗 클라우드 당 하나의 평가판 클러스터가 있습니다. 평가 기간 중 단일 호스트를 기준으로 평가판 클러스터를 스케일링할 수 있습니다.

vSphere와 NSX-T Manager를 사용하여 클러스터 구성 또는 작업의 다른 대부분의 측면을 관리합니다. 클러스터의 각 호스트에 있는 모든 로컬 스토리지는 vSAN이 제어합니다.

## <a name="hosts"></a>호스트

Azure VMware Solution 클러스터는 운영 체제 미설치 상태의 초수렴형 인프라를 기반으로 합니다. 다음 표에서는 호스트의 RAM, CPU 및 디스크 용량을 보여 줍니다.

| 호스트 유형              |             CPU             |   RAM(GB)   |  vSAN NVMe cache Tier (TB, raw)  |  vSAN SSD capacity tier (TB, raw)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  dual Intel 18 core 2.3 GHz  |     576      |                3.2               |                15.20               |

클러스터를 빌드하거나 스케일링하는 데 사용되는 호스트는 격리된 호스트 풀에서 제공합니다. 해당 호스트는 하드웨어 테스트를 통과하고 모든 데이터를 안전하게 삭제한 상태입니다. 

## <a name="vmware-software-versions"></a>VMware 소프트웨어 버전

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="update-frequency"></a>업데이트 빈도

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>호스트 유지 관리 및 수명 주기 관리

호스트 유지 관리 및 수명 주기 관리는 프라이빗 클라우드 클러스터의 용량 또는 성능에 영향을 주지 않습니다.  자동화된 호스트 유지 관리의 예로는 펌웨어 업그레이드, 하드웨어 수리 또는 교체 등이 있습니다.

Microsoft는 NSX-T Manager 및 NSX-T Edge와 같은 NSX-T 어플라이언스의 수명 주기 관리를 책임집니다. 0계층 게이트웨이 만들기 및 북-남 라우팅 활성화 같은 네트워크 구성 부트스트래핑도 Microsoft가 책임집니다. NSX-T SDN 구성은 고객의 책임입니다. 예를 들어 네트워크 세그먼트, 분산 방화벽 규칙, 1계층 게이트웨이 및 부하 분산 장치 등이 있습니다.

## <a name="backup-and-restoration"></a>백업 및 복원

프라이빗 클라우드 vCenter 및 NSX-T 구성은 Microsoft가 시간별 일정에 따라 백업합니다.  백업은 3일 동안 보관됩니다. 백업에서 복원해야 하는 경우 Azure Portal에서 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 열어 복원을 요청합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution 프라이빗 클라우드의 개념을 살펴 보았습니다. 다음에 대해 알아볼 수 있습니다. 

- [Azure VMware Solution 네트워킹 및 상호 연결 개념](concepts-networking.md).
- [Azure VMware Solution 스토리지 개념](concepts-storage.md).
- [Azure VMware Solution 리소스를 사용하도록 설정하는 방법](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

