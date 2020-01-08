---
title: Azure FarmBeats 아키텍처
description: Azure FarmBeats의 아키텍처에 대해 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482375"
---
# <a name="integration-patterns"></a>통합 패턴

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 b2b 제품입니다. FarmBeats를 사용 하면 공급자 간에 농업 데이터 집합을 집계 하 고, 데이터 집합을 융합 하 여 AI (인공 지능) 또는 ML (Machine Learning) 모델을 빌드하여 실행 가능한 정보를 생성할 수 있습니다.

![프로젝트 팜 비트](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

다음 섹션에서는 Azure FarmBeats의 통합 패턴에 대해 설명 합니다.

## <a name="why-integrate-with-azure-farmbeats"></a>Azure FarmBeats와 통합 하는 이유

이 섹션은 데이터 시스템 (예: 센서, 드 론, 날씨 스테이션)을 Azure FarmBeats에 통합 하려는 파트너를 중심으로 설명 합니다.

Azure FarmBeats는 agricultural 기업은 다양 한 기록 및 실시간 agricultural 데이터 집합을 단일 플랫폼에 추가할 수 있는 확장 가능한 제품입니다. Azure FarmBeats를 사용 하면 agricultural 비즈니스에서 팜의 컨텍스트에서 데이터를 표준화, 상황 및 집계할 수 있습니다.

Azure FarmBeats를 사용 하 여 데이터 파트너가 되기 때문에 시스템을 열어서 더 광범위 한 채택을 제공 하 고 데이터 제공을 통해 더 많은 고객에 게 연락할 수 있습니다. Azure FarmBeats는 Datahub 라는 확장 가능한 API 계층을 제공 하므로 장치에서 표준화 된 스키마로 데이터를 수집 하는 데 도움이 됩니다.

고객의 Azure FarmBeats 인스턴스 내에서 데이터를 사용할 수 있게 되 면 고객은 데이터를 기반으로 다양 한 분석 및 도구를 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

센서 데이터 통합에 대 한 자세한 내용은 [센서 데이터 통합](sensor-partner-integration-in-azure-farmbeats.md) 및 이미지 파트너 통합을 참조 하세요. [이미지 파트너 통합](imagery-partner-integration-in-azure-farmbeats.md)을 참조 하세요.
