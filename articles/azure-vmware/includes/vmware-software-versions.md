---
title: VMware 소프트웨어 버전
description: Azure VMware 솔루션에 대해 지원 되는 VMware 소프트웨어 버전입니다.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825093"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware 솔루션 사설 클라우드 클러스터에서 사용 되는 현재 소프트웨어 버전의 VMware 소프트웨어는 다음과 같습니다.

| 소프트웨어              |    버전   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>현재 지원되는 유일한 NSX 버전은 NSX-T입니다.

사설 클라우드의 새 클러스터의 경우 소프트웨어 버전은 현재 실행 중인 항목과 일치 합니다. 구독의 새 사설 클라우드의 경우 소프트웨어 스택의 최신 버전이 설치 됩니다. 자세한 내용은 [VMware software version 요구 사항](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)을 참조 하세요.

사설 클라우드 소프트웨어 번들 업그레이드는 VMware에서 최신 소프트웨어 번들 릴리스의 한 버전에 있는 소프트웨어를 유지 합니다. 사설 클라우드 소프트웨어 버전은 개별 소프트웨어 구성 요소의 최신 버전 (ESXi, NSX, vCenter, vSAN)과 다를 수 있습니다. [사설 클라우드 업데이트 및 업그레이드](../concepts-upgrades.md)에 설명 된 Azure VMware 솔루션 플랫폼 소프트웨어에 대 한 일반적인 업그레이드 정책 및 프로세스를 찾을 수 있습니다.

