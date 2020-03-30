---
title: Azure 팜비츠 아키텍처
description: Azure FarmBeats의 아키텍처에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482375"
---
# <a name="integration-patterns"></a>통합 패턴

Azure FarmBeats는 Azure 마켓플레이스에서 사용할 수 있는 비즈니스 간 제품입니다. FarmBeats를 사용하면 데이터 집합을 융합하여 인공 지능(AI) 또는 기계 학습(ML) 모델을 구축하여 공급자 간에 농업 데이터 집합을 집계하고 실행 가능한 통찰력을 생성할 수 있습니다.

![Farm Beats 프로젝트](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

다음 섹션에서는 Azure FarmBeats의 통합 패턴을 설명합니다.

## <a name="why-integrate-with-azure-farmbeats"></a>Azure FarmBeats와 통합해야 하는 이유

이 섹션에서는 데이터 시스템(예: 센서, 드론, 기상 관측소)을 Azure FarmBeats에 통합하려는 파트너에 중점을 두었습니다.

Azure FarmBeats는 확장 가능한 제품으로 농업 기업이 서로 다른 과거 및 실시간 농업 데이터 집합을 단일 플랫폼에 추가할 수 있습니다. Azure FarmBeats는 농업 비즈니스가 팜의 컨텍스트에서 데이터를 정규화, 컨텍스트화 및 집계하는 데 도움이 됩니다.

Azure FarmBeats와 데이터 파트너가 됨으로써 시스템을 열어 더 폭넓은 채택을 할 수 있으며 데이터 오퍼링을 통해 더 많은 고객에게 다가갈 수 있습니다. Azure FarmBeats는 Datahub라는 확장 가능한 API 계층을 제공하여 장치에서 체계적으로 표준화된 스키마로 데이터를 수집할 수 있도록 도와줍니다.

고객의 Azure FarmBeats 인스턴스 내에서 데이터를 사용할 수 있게 되면 고객은 데이터 위에 보다 풍부한 분석 및 도구를 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

센서 데이터 통합에 대한 자세한 내용은 [센서 데이터 통합](sensor-partner-integration-in-azure-farmbeats.md) 및 이미지 파트너 통합을 참조하세요. [imagery partner integration](imagery-partner-integration-in-azure-farmbeats.md)
