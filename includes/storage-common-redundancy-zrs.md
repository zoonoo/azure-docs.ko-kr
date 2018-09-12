---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027450"
---
ZRS(영역 중복 저장소)는 단일 지역에 있는 3개의 저장소 클러스터에서 데이터를 동기적으로 복제합니다. 각 저장소 클러스터는 다른 저장소 클러스터와 물리적으로 분리되어 있으며 자체 AZ(가용성 영역)에 상주합니다. 각 가용성 영역과 그 안에 포함된 ZRS 클러스터는 자율적으로 사용되며, 별도의 유틸리티와 네트워킹 기능을 갖추고 있습니다.

ZRS 계정에 데이터를 저장하면 영역을 사용할 수 없는 경우 데이터에 액세스하여 관리할 수 있습니다. ZRS는 뛰어난 성능과 짧은 대기 시간을 제공하며, [LRS(로컬 중복 저장소)](../articles/storage/common/storage-redundancy-lrs.md)와 동일한 [확장성 목표](../articles/storage/common/storage-scalability-targets.md)를 제공합니다.

가동 중단 또는 자연 재해로 인해 영역 데이터 센터를 사용할 수 없는 경우에도 강력한 일관성, 강력한 내구성 및 고가용성이 필요한 시나리오의 경우 ZRS를 사용하는 것이 좋습니다. ZRS는 지정된 연도 동안 99.9999999999% 이상의 내구성을 저장소 개체에 제공합니다(12 9의 경우).

가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요.