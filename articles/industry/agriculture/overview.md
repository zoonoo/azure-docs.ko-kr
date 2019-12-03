---
title: Azure FarmBeats 개요
description: Azure FarmBeats에 대한 개요를 제공합니다.
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ff28ae7dbff40910ac0431fb47f7f0be3e1569c0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538138"
---
# <a name="overview-of-azure-farmbeats"></a>Azure FarmBeats 개요

Azure FarmBeats는 농업에서 지능형 데이터 기반 솔루션을 신속하게 빌드할 수 있도록 설계된 Azure 서비스 및 기능의 컬렉션입니다. Azure FarmBeats는 심층 데이터 엔지니어링 리소스에 투자하지 않고도, 센서, 드론, 카메라, 위성과 같은 다양한 원본에서 농업 관련 데이터를 획득, 집계 및 처리할 수 있는 Azure Marketplace 제품입니다.

> [!NOTE]
> Azure FarmBeats는 현재 퍼블릭 미리 보기로 제공됩니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. Azure FarmBeats는 서비스 수준 계약 없이 제공됩니다. 지원을 받으려면 [Azure FarmBeats 포럼](https://aka.ms/FarmBeatsMSDN )을 사용합니다.

Azure FarmBeats를 사용하면 센서, 위성, 드론 등의 다양한 원본에서 모두 하나의 농장 컨텍스트(지리적 관심 영역)에 있는 데이터를 가져올 수 있습니다.

다음 작업을 수행할 수 있습니다.

- 다양한 공급자의 농업 데이터 세트 집계
- 다양한 데이터 세트를 융합하여 AI/ML(인공 지능/기계 언어) 모델을 빠르게 빌드

Azure FarmBeats는 다음을 수행하는 강력하고 간단한 방법을 제공합니다.

- GeoJSON 파일을 사용하여 팜에 대한 맵을 만듭니다.
- 위성 이미지를 기준으로 하는 식생 지수 및 급수 지수를 사용하여 농장 상태를 평가합니다.
- 사용할 센서 수와 배치할 위치에 대한 권장 사항을 확인합니다.
- 다양한 센서 공급자의 센서를 통해 수집된 토양 데이터를 시각화하여 농장 상태를 추적합니다.
- 위성 및 센서의 융합 데이터를 기준으로 토양 수분 지도를 가져옵니다.
- 집계된 데이터 세트를 토대로 AI/ML 모델을 빌드하여 실행 가능한 인사이트를 얻습니다.
- 농장 상태 권장 지침을 제공하여 디지털 농업 솔루션을 빌드하거나 보강합니다.

Azure FarmBeats 구성 요소에 대해서는 이 문서의 다음 섹션에서 설명합니다.

## <a name="data-hub"></a>데이터 허브

Azure FarmBeats Data 허브는 API 계층으로, 여러 공급자의 다양한 농업 데이터 세트를 집계, 정규화 및 컨텍스트화할 수 있습니다. 이 미리 보기에서는 2개의 센서 공급자 [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), 1개의 위성 영상 공급자 [Sentinel-2](https://sentinel.esa.int/web/sentinel/home), 3개의 드론 영상 공급자 [senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/), [DJI](https://dji.com/)를 활용할 수 있습니다. 데이터 허브는 API 플랫폼으로 설계되었으며, Azure FarmBeats와 연결하기 위해 좀 더 많은 공급자와 공조하고 있으므로 솔루션을 빌드하는 동안 더 많은 옵션을 선택할 수 있습니다.

## <a name="accelerator"></a>액셀러레이터

사용자 인터페이스 및 모델 개발을 가동하는, 데이터 허브 위에 빌드된 샘플 솔루션입니다. 이 웹 애플리케이션은 API를 활용하여 수집된 센서 데이터를 차트로 시각화하고 모델 출력을 지도로 시각화하는 방법을 보여 줍니다. 예를 들어 액셀러레이터를 사용하여 농장을 신속하게 만들고 해당 농장에 대한 식생 지수 지도 또는 센서 배치 지도를 쉽게 가져올 수 있습니다.

## <a name="resources"></a>리소스

자세한 내용은 Azure FarmBeats [블로그](https://aka.ms/AzureFarmBeats) 및 [포럼](https://aka.ms/FarmBeatsMSDN)을 방문하세요.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats를 시작하려면 [Azure 마켓플레이스](https://aka.ms/FarmBeatsMarketplace)를 방문하여 배포 프로세스를 시작합니다.
