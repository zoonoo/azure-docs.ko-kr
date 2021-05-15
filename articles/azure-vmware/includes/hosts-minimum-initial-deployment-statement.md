---
title: 최소 초기 호스트 배포
description: 최소 초기 배포는 세 개의 호스트입니다.
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 49eb2fdd105b02a52d0cba555a78e0d44d133bf6
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108179543"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->

만들어진 프라이빗 클라우드 하나마다 기본적으로 하나의 vSAN 클러스터가 있습니다. 클러스터를 추가, 삭제 및 스케일링할 수 있습니다.  클러스터당 최소 호스트 수 및 초기 배포 수는 3개입니다. 

평가판 클러스터는 평가용으로 제공되며 호스트 3개로 제한됩니다. 평가 기간 중 단일 호스트를 기준으로 스케일링할 수 있는 프라이빗 클라우드당 하나의 평가판 클러스터가 있습니다.

vSphere와 NSX-T Manager를 사용하여 클러스터 구성 또는 작업의 다른 대부분의 측면을 관리합니다. 클러스터의 각 호스트에 있는 모든 로컬 스토리지는 vSAN이 제어합니다.

>[!TIP]
>초기 배포 수를 초과해야 하는 경우 언제든지 나중에 클러스터를 확장하고 클러스터를 추가할 수 있습니다.
