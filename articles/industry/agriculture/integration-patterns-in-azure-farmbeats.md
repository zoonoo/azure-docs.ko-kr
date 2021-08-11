---
title: Azure FarmBeats 아키텍처
description: Azure FarmBeats의 아키텍처를 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 64c76c9dd92aa8e39225a5e4a2789ad2b6bdf350
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108768880"
---
# <a name="integration-patterns"></a>통합 패턴

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 B2B(Business-to-Business) 제품입니다. FarmBeats를 사용하면 데이터 세트를 융합하여 AI(인공 지능) 또는 ML(Machine Learning) 모델을 빌드하여 공급자 간에 농업 데이터 세트를 집계하고, 실행 가능한 인사이트를 생성할 수 있습니다.

![Farm Beats 프로젝트](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

다음 섹션에서는 Azure FarmBeats 통합 패턴에 대해 설명합니다.

## <a name="why-integrate-with-azure-farmbeats"></a>Azure FarmBeats와 통합하는 이유는 무엇인가요?

이 섹션은 데이터 시스템(예: 센서, 드론, 기상 관측소)을 Azure FarmBeats로 통합하려는 파트너를 중심으로 합니다.

Azure FarmBeats는 농업 비즈니스에서 다양한 기록 및 실시간 농업 데이터 세트를 단일 플랫폼에 추가할 수 있도록 해주는 연장 가능한 제품입니다. Azure FarmBeats를 통해 농업 비즈니스는 팜의 컨텍스트에서 데이터를 정규화, 컨텍스트화 및 집계할 수 있습니다.

Azure FarmBeats와 데이터 파트너가 됨으로써 시스템을 더 광범위한 채택에 개방하고 더 많은 고객에게 데이터 제품을 제공할 수 있습니다. Azure FarmBeats는 Datahub라는 연장 가능한 API 계층을 제공하여 디바이스에서 체계적으로 표준화된 스키마로 데이터를 수집할 수 있도록 도와줍니다.

고객의 Azure FarmBeats 인스턴스 내에서 데이터를 사용할 수 있게 되면 고객은 데이터를 기반으로 보다 풍부한 분석 및 도구를 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

센서 데이터 통합에 대한 자세한 내용은 [센서 데이터 통합](sensor-partner-integration-in-azure-farmbeats.md)을 참조하고 이미지 파트너 통합은 [이미지 파트너 통합](imagery-partner-integration-in-azure-farmbeats.md)을 참조하세요.
