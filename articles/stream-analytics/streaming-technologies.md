---
title: Azure에서 실시간 및 스트림 처리 솔루션 선택
description: Azure에서 애플리케이션을 빌드하는 데 적합한 실시간 분석 및 스트리밍 처리 기술을 선택하는 방법에 대해 알아봅니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019060"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Azure에서 실시간 분석 및 스트리밍 처리 기술 선택

Azure에서 실시간 분석 및 스트리밍 처리에 사용할 수 있는 몇 가지 서비스가 있습니다. 이 문서에서는 애플리케이션에 가장 적합한 기술을 결정하는 데 필요한 정보를 제공합니다.

## <a name="when-to-use-azure-stream-analytics"></a>Azure Stream Analytics를 사용하는 경우

Azure Stream Analytics는 Azure에서 스트림 분석에 권장되는 서비스입니다. 다음을 포함하지만 이에 국한되지 않는 다양한 시나리오를 위한 것입니다.

* 데이터 시각화를 위한 대시보드
* 시간 및 공간 패턴 또는 이상 징후에 대한 실시간 [경고](stream-analytics-set-up-alerts.md)
* ETL(추출, 변환, 로드)
* [이벤트 소싱 패턴](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

애플리케이션에 Azure Stream Analytics 작업을 추가하는 것은 이미 알고 있는 SQL 언어를 사용하여 Azure에서 스트리밍 분석을 실행하는 가장 빠른 방법입니다. Azure Stream Analytics는 작업 서비스이므로 클러스터를 관리하는 데 시간을 할애할 필요가 없으며 작업 수준에서 99.9% SLA로 가동 중지 시간에 대해 걱정할 필요가 없습니다. 요금 청구는 작업 수준에서도 이루어지므로 시작 비용은 낮지만(스트리밍 단위 1개) 스케일링 가능합니다(최대 스트리밍 단위 192개). 클러스터를 실행하고 유지 관리하는 것보다 몇 가지 Stream Analytics 작업을 실행하는 것이 비용 면에서 훨씬 더 효율적입니다.

Azure Stream Analytics에는 풍부한 기본 환경이 있습니다. 추가 설정 없이 다음 기능을 즉시 활용할 수 있습니다.

* [기간 이동 집계](stream-analytics-window-functions.md), 시간 조인 및 시간 분석 함수와 같은 기본 제공 시간 연산자
* 네이티브 Azure [입력](stream-analytics-add-inputs.md) 및 [출력](stream-analytics-define-outputs.md) 어댑터
* 조회 테이블이라고도 하는 느리게 변화하는 [참조 데이터](stream-analytics-use-reference-data.md) 지원(지오펜싱용 지리 공간 참조 데이터와의 조인 포함)
* 통합 솔루션(예: [Anomaly Detection](stream-analytics-machine-learning-anomaly-detection.md))
* 동일한 쿼리의 여러 시간 창
* 임의의 시퀀스에서 여러 시간 연산자를 구성하는 기능
* Event Hubs에 도달하는 입력에서 Event Hubs 간 네트워크 지연을 포함하여 Event Hubs에 랜딩하는 출력까지 지속적으로 높은 처리량으로 유지되는 100ms 미만의 엔드투엔드 대기 시간

## <a name="when-to-use-other-technologies"></a>다른 기술을 사용하는 경우

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>JavaScript 또는 C# 이외의 언어로 UDF, UDA, 사용자 지정 역직렬 변환기를 작성하려고 합니다.

Azure Stream Analytics는 클라우드 작업의 경우 JavaScript에서 UDF(사용자 정의 함수) 또는 UDA(사용자 정의 집계)를 지원하고 IoT Edge 작업의 경우 C#을 지원합니다. C# 사용자 정의 역직렬 변환기도 지원됩니다. Java 또는 Python과 같은 다른 언어로 역직렬 변환기, UDF 또는 UDA를 구현하려는 경우 Spark 구조적 스트리밍을 사용할 수 있습니다. 가상 머신에서 Event Hubs **EventProcessorHost** 를 실행하여 임의의 스트리밍 처리를 수행할 수도 있습니다.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>솔루션이 다중 클라우드 또는 온-프레미스 환경에 있습니다.

Azure Stream Analytics는 Microsoft의 독점 기술이며 Azure에서만 사용할 수 있습니다. 클라우드 또는 온-프레미스에서 솔루션을 이식해야 하는 경우 Spark 구조적 스트리밍 또는 Storm과 같은 오픈 소스 기술을 고려합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio Code를 사용하여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
