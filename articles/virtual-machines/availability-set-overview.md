---
title: 가용성 집합 개요
description: Azure의 가용성 집합에 대한 자세한 정보
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510542"
---
# <a name="availability-sets-overview"></a>가용성 집합 개요

이 문서에서는 Azure VM(가상 머신)의 가용성 기능에 대한 개요를 제공합니다.

## <a name="what-is-an-availability-set"></a>가용성 집합이란? 

가용성 집합은 중복성과 가용성을 제공하기 위해 Azure에서 애플리케이션이 빌드되는 방식을 이해할 수 있도록 하는 VM의 논리적 그룹입니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 가용성 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 가용성 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다.

## <a name="how-do-availability-sets-work"></a>가용성 집합 작동 방식
기본 Azure 플랫폼에서는 가용성 집합에 포함된 각각의 가상 머신을 **업데이트 도메인** 및 **장애 도메인** 에 할당합니다. 특정 가용성 집합의 경우 기본적으로 사용자가 구성할 수 없는 다섯 개의 업데이트 도메인이 할당되어(그런 다음 최대 20개의 업데이트 도메인을 제공하도록 Resource Manager 배포를 늘릴 수 있음) 동시에 재부팅할 수 있는 가상 머신 및 기본 물리적 하드웨어 그룹을 나타냅니다. 단일 가용성 집합 내에 5개 이상의 가상 머신을 구성한 경우 6번째 가상 머신은 동일한 업데이트 도메인에 첫 번째 가상 머신으로 배치되고, 7번째 가상 머신은 동일한 업데이트 도메인에 두 번째 가상 머신으로 배치되는 식입니다. 재부팅되는 업데이트 도메인의 순서는 계획된 유지 보수 중 순차적으로 진행할 수 없으며 한 번에 하나의 업데이트 도메인만이 재부팅됩니다. 다시 부팅된 업데이트 도메인을 복구할 시간으로 30분이 제공되며 이 시간이 지나면 다른 업데이트 도메인에서 유지 관리가 시작됩니다.

장애 도메인은 공통 전원과 네트워크 스위치를 공유하는 가상 머신 그룹을 정의합니다. 기본적으로 가용성 집합 내에 구성된 가상 머신은 Resource Manager 배포를 위해 최대 3개의 장애 도메인으로 분리됩니다. 가상 머신을 가용성 집합에 배치한다고 해서 애플리케이션이 운영 체제 또는 애플리케이션 고유의 오류로부터 보호되는 것은 아닙니다. 잠재적인 물리적 하드웨어 오류, 네트워크 중단, 전력 차단의 영향이 제한될 뿐입니다.

![가용성 집합](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


[Azure Managed Disks](./faq-for-disks.md)를 사용하는 VM의 경우, 관리 가용성 집합을 사용할 때 VM은 관리 디스크 장애 도메인에 맞춰집니다. 이러한 정렬은 VM에 연결된 모든 관리 디스크가 동일한 관리 디스크 장애 도메인 내에 있도록 합니다. 

관리 디스크의 VM만 관리 가용성 집합에서 만들어질 수 있습니다. 관리 디스크 장애 도메인의 수는 지역에 따라 다릅니다. 즉, 지역당 2개 또는 3개의 관리 디스크 장애 도메인이 있을 수 있습니다. 

![관리 가용성 집합](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>다음 단계
이제 이러한 가용성 및 중복 기능을 사용하여 Azure 환경을 빌드하기 시작할 수 있습니다. 모범 사례 정보는 [Azure 가용성 모범 사례](/azure/architecture/checklist/resiliency-per-service)를 참조하세요.

