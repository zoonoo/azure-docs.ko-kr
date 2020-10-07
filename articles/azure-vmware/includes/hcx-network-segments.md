---
title: VMware HCX 네트워크 세그먼트
description: VMware HCX에는 4개의 네트워크가 필요합니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578871"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX에는 다음 4개의 네트워크가 필요합니다.

- **관리 네트워크:** 일반적으로 vSphere 클러스터에서 사용되는 것과 동일한 관리 네트워크입니다.  최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다(배포에 따라 더 많은 숫자가 필요할 수 있음).

- **vMotion 네트워크:** 일반적으로 vSphere 클러스터의 vMotion에 대해 사용되는 것과 동일한 네트워크입니다.  최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다(배포에 따라 더 많은 숫자가 필요할 수 있음).  

   분산 가상 스위치 또는 vSwitch0에 vMotion 네트워크를 노출해야 합니다. 그렇지 않으면 환경을 수정합니다.

   > [!NOTE]
   > 이 네트워크가 라우팅되지 않은 경우(프라이빗) 이는 정상입니다.

- **업링크 네트워크:** VMware HCX 업링크에 대한 새 네트워크를 만들고 포트 그룹을 통해 vSphere 클러스터로 확장하려고 합니다.  최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다(배포에 따라 더 많은 숫자가 필요할 수 있음).  

   > [!NOTE]
   > /29 네트워크를 만드는 것이 좋지만 네트워크 크기에는 제한이 없습니다.

- **복제 네트워크:** VMware HCX 복제에 대한 새 네트워크를 만들고 포트 그룹을 통해 vSphere 클러스터로 해당 네트워크를 확장하려고 합니다.  최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다(배포에 따라 더 많은 숫자가 필요할 수 있음).

   > [!NOTE]
   > /29 네트워크를 만드는 것이 좋지만 네트워크 크기에는 제한이 없습니다.