---
title: VMware HCX 네트워크 세그먼트
description: VMware HCX에는 4개의 네트워크가 필요합니다.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999634"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX에는 다음 4개의 네트워크가 필요합니다.

- **관리 네트워크:** 일반적으로 vSphere 클러스터에서 사용되는 것과 동일한 관리 네트워크입니다. 최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. 배포에 따라 더 많은 숫자가 필요할 수 있습니다.

   > [!NOTE]
   > 권장하는 방법은 /26 네트워크를 만드는 것입니다. /26 네트워크에서는 최대 10개의 서비스 메시와 60개의 네트워크 확장기(서비스 메시당 -1개)를 사용할 수 있습니다. Azure VMware Solution 프라이빗 클라우드를 사용하여 네트워크 확장기당 8개의 네트워크를 확장할 수 있습니다.
   >
   
- **vMotion 네트워크:** 일반적으로 vSphere 클러스터의 vMotion에 대해 사용되는 것과 동일한 네트워크입니다.  최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. 배포에 따라 더 많은 숫자가 필요할 수 있습니다.  

   분산 가상 스위치 또는 vSwitch0에 vMotion 네트워크를 노출해야 합니다. 그렇지 않으면 환경을 수정합니다.

   > [!NOTE]
   > 이 네트워크는 프라이빗(라우팅되지 않음)일 수 있습니다.

- **업링크 네트워크:** VMware HCX 업링크에 대한 새 네트워크를 만들고 포트 그룹을 통해 vSphere 클러스터로 확장하려고 합니다. 최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. 배포에 따라 더 많은 숫자가 필요할 수 있습니다.  

   > [!NOTE]
   > 권장하는 방법은 /26 네트워크를 만드는 것입니다. /26 네트워크에서는 최대 10개의 서비스 메시와 60개의 네트워크 확장기(서비스 메시당 -1개)를 사용할 수 있습니다. Azure VMware Solution 프라이빗 클라우드를 사용하여 네트워크 확장기당 8개의 네트워크를 확장할 수 있습니다.
   >
   
- **복제 네트워크:** 이 구성 요소는 선택 사항입니다. VMware HCX 복제에 대한 새 네트워크를 만들고 포트 그룹을 통해 vSphere 클러스터로 해당 네트워크를 확장하려고 합니다. 최소한 VMware HCX에 대해 이 네트워크 세그먼트에서 두 개의 IP를 식별합니다. 배포에 따라 더 많은 숫자가 필요할 수 있습니다.

   > [!NOTE]
   > 이 구성은 온-프레미스 클러스터 호스트가 전용 복제 VMkernel 네트워크를 사용하는 경우에만 가능합니다.  온-프레미스 클러스터에 전용 복제 VMkernel 네트워크가 정의되어 있지 않으면 이 네트워크를 만들 필요가 없습니다.
