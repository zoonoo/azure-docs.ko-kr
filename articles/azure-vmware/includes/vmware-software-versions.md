---
title: VMware 소프트웨어 버전
description: Azure VMware Solution에 지원되는 VMware 소프트웨어 버전입니다.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097044"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware Solution 프라이빗 클라우드 클러스터의 새 배포에 사용되는 VMware 소프트웨어 버전은 다음과 같습니다.

| 소프트웨어              |    버전   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3l    | 
| ESXi                  |    6.7 U3l    | 
| vSAN                  |    6.7 U3l    |
| NSX-T <br />**참고:** 지원되는 유일한 NSX 버전은 NSX-T입니다.               |      3.1.1     |


기존 프라이빗 클라우드에 추가된 새 클러스터는 현재 실행 중인 소프트웨어 버전이 적용됩니다. 자세한 내용은 [VMware 소프트웨어 버전 요구 사항](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)을 참조하세요.

프라이빗 클라우드 소프트웨어 번들 업그레이드는 VMware의 최신 소프트웨어 번들 릴리스의 한 버전 내에서 소프트웨어를 유지합니다. 프라이빗 클라우드 소프트웨어 버전은 개별 소프트웨어 구성 요소(ESXi, NSX-T, vCenter, vSAN)의 최신 버전과 다를 수 있습니다. [프라이빗 클라우드 업데이트 및 업그레이드](../concepts-upgrades.md)에 설명된 Azure VMware Solution 플랫폼 소프트웨어에 대한 일반적인 업그레이드 정책 및 프로세스를 찾을 수 있습니다.

