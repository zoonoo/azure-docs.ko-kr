---
title: VMware HCX 네트워크 세그먼트
description: VMware HCX에는 4개의 네트워크가 필요합니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173640"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX에는 다음 4개의 네트워크가 필요합니다.

- **관리 네트워크:** 일반적으로 vSphere 클러스터에서 사용되는 것과 동일한 관리 네트워크입니다. 최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. (배포에 따라 더 많은 숫자가 필요할 수 있습니다.)

- **vMotion 네트워크:** 일반적으로 vSphere 클러스터의 vMotion에 대해 사용되는 것과 동일한 네트워크입니다.  최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. (배포에 따라 더 많은 숫자가 필요할 수 있습니다.)  

   분산 가상 스위치 또는 vSwitch0에 vMotion 네트워크를 노출해야 합니다. 그렇지 않으면 환경을 수정합니다.

   > [!NOTE]
   > 이 네트워크가 라우팅되지 않은 경우(프라이빗) 이는 정상입니다.

- **업링크 네트워크:** VMware HCX 업링크에 대한 새 네트워크를 만들고 포트 그룹을 통해 vSphere 클러스터로 확장하려고 합니다. 최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. (배포에 따라 더 많은 숫자가 필요할 수 있습니다.)  

   > [!NOTE]
   > /29 네트워크를 만드는 것이 좋지만 네트워크 크기에는 제한이 없습니다.

- **복제 네트워크:** VMware HCX 복제에 대한 새 네트워크를 만들고 포트 그룹을 통해 vSphere 클러스터로 해당 네트워크를 확장하려고 합니다. 최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. (배포에 따라 더 많은 숫자가 필요할 수 있습니다.)

   > [!NOTE]
   > /29 네트워크를 만드는 것이 좋지만 네트워크 크기에는 제한이 없습니다.