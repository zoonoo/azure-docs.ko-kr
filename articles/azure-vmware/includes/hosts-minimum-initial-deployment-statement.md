---
title: 최소 초기 호스트 배포
description: 최소 초기 배포는 세 개의 호스트입니다.
ms.topic: include
ms.date: 04/27/2021
ms.openlocfilehash: 72f0ad6c02c7ef24301276f1c6f68b300c86c0a4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126317"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->


기본적으로 생성된 프라이빗 클라우드마다 vSAN 클러스터가 하나 있습니다. 클러스터를 추가, 삭제 및 스케일링할 수 있습니다.  클러스터당 최소 호스트 수는 3개입니다. 클러스터당 최대 16개까지 호스트를 한 번에 하나씩 추가할 수 있습니다. 프라이빗 클라우드당 최대 클러스터 수는 12개입니다.  Azure VMware Solution의 초기 배포에는 호스트가 3개 있습니다. 

평가판 클러스터는 평가용으로 제공되며 호스트 3개로 제한됩니다. 프라이빗 클라우드당 평가판 클러스터가 하나 있습니다. 평가 기간 중에 단일 호스트별로 평가판 클러스터를 스케일링할 수 있습니다.

vSphere와 NSX-T Manager를 사용하여 클러스터 구성 또는 작업의 다른 대부분의 측면을 관리합니다. vSAN에서 클러스터의 각 호스트에 있는 모든 로컬 스토리지를 제어합니다.

>[!TIP]
>초기 배포 수를 초과해야 하는 경우 언제든지 나중에 클러스터를 확장하고 클러스터를 추가할 수 있습니다.