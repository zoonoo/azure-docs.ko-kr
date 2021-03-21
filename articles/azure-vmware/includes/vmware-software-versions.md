---
title: VMware 소프트웨어 버전
description: Azure VMware 솔루션에 대해 지원 되는 VMware 소프트웨어 버전입니다.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462520"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware 솔루션 사설 클라우드 클러스터의 새 배포에 사용 되는 VMware 소프트웨어 버전은 다음과 같습니다.

| 소프트웨어              |    버전   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T <br />**참고:** NSX은 유일 하 게 지원 되는 NSX 버전입니다.               |      2.5     |


새 클러스터가 기존 사설 클라우드에 추가 되 면 현재 실행 중인 소프트웨어 버전이 적용 됩니다. 자세한 내용은 [VMware software version 요구 사항](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)을 참조 하세요.

사설 클라우드 소프트웨어 번들 업그레이드는 VMware에서 최신 소프트웨어 번들 릴리스의 한 버전에 있는 소프트웨어를 유지 합니다. 사설 클라우드 소프트웨어 버전은 개별 소프트웨어 구성 요소의 최신 버전 (ESXi, NSX, vCenter, vSAN)과 다를 수 있습니다. [사설 클라우드 업데이트 및 업그레이드](../concepts-upgrades.md)에 설명 된 Azure VMware 솔루션 플랫폼 소프트웨어에 대 한 일반적인 업그레이드 정책 및 프로세스를 찾을 수 있습니다.

