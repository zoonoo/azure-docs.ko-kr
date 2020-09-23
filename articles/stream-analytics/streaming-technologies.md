---
title: Azure에서 실시간 및 스트림 처리 솔루션 선택
description: Azure에서 응용 프로그램을 빌드하는 데 적합 한 실시간 분석 및 스트리밍 처리 기술을 선택 하는 방법에 대해 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: a204aacc0061091e0a273581e766bb669d191f0b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903713"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Azure에서 실시간 분석 및 스트리밍 처리 기술 선택

Azure에서 실시간 분석 및 스트리밍 처리에 사용할 수 있는 몇 가지 서비스가 있습니다. 이 문서에서는 응용 프로그램에 가장 적합 한 기술을 결정 하는 데 필요한 정보를 제공 합니다.

## <a name="when-to-use-azure-stream-analytics"></a>사용 해야 하는 경우 Azure Stream Analytics

Azure Stream Analytics은 Azure에서 Stream Analytics에 권장 되는 서비스입니다. 다음을 포함 하지만이에 국한 되지 않는 다양 한 시나리오를 위한 것입니다.

* 데이터 시각화를 위한 대시보드
* Temporal 및 공간 패턴 또는 이상에서 실시간 [경고](stream-analytics-set-up-alerts.md)
* ETL(추출, 변환, 로드)
* [이벤트 소싱 패턴](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

응용 프로그램에 Azure Stream Analytics 작업을 추가 하는 것은 이미 알고 있는 SQL 언어를 사용 하 여 Azure에서 스트리밍 분석을 실행 하는 가장 빠른 방법입니다. Azure Stream Analytics은 작업 서비스 이므로 클러스터를 관리 하는 데 시간이 걸리지 않으며, 작업 수준에서 99.9% SLA를 사용 하 여 가동 중지 시간에 대해 걱정 하지 않아도 됩니다. 요금 청구는 작업 수준에서 시작 비용이 낮고 (하나의 스트리밍 단위) 확장 가능 하 게 (최대 192 스트리밍 단위) 수행 됩니다. 클러스터를 실행 하 고 유지 관리 하는 것 보다 몇 가지 Stream Analytics 작업을 실행 하는 것이 훨씬 더 비용 효율적입니다.

Azure Stream Analytics에는 풍부한 기본 환경이 있습니다. 추가 설정 없이 다음 기능을 즉시 활용할 수 있습니다.

* 기간 이동 [집계](stream-analytics-window-functions.md), 임시 조인, 임시 분석 함수 등의 기본 제공 임시 연산자.
* 네이티브 Azure [입력](stream-analytics-add-inputs.md) 및 [출력](stream-analytics-define-outputs.md) 어댑터
* 지 오 펜싱에 대 한 지리 공간적 참조 데이터로의 조인을 포함 하 여 느리게 변경 되는 [참조 데이터](stream-analytics-use-reference-data.md) (조회 테이블이 라고도 함) 지원
* [변칙 검색](stream-analytics-machine-learning-anomaly-detection.md) 같은 통합 된 솔루션
* 동일한 쿼리의 여러 시간 창
* 임의의 시퀀스에서 여러 temporal 연산자를 구성 하는 기능
* Event Hubs에서 도착 하는 입력에서의 100-밀리초의 종단 간 대기 시간 아래에서 Event Hubs에서의 네트워크 지연 및 지속적으로 높은 처리량을 포함 하 여 Event Hubs에 있는 출력

## <a name="when-to-use-other-technologies"></a>다른 기술을 사용 하는 경우

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>JavaScript 또는 C 이외의 언어로 Udf, UDAs 및 사용자 지정 deserializers를 작성 하려고 합니다. #

Azure Stream Analytics은 클라우드 작업에 대 한 JavaScript의 UDF (사용자 정의 함수) 또는 UDA (사용자 정의 집계)를 지원 하 고 IoT Edge 작업의 경우 c #을 지원 합니다. C # 사용자 정의 deserializers도 지원 됩니다. Java 또는 Python과 같은 다른 언어로 역직렬 변환기, UDF 또는 UDA를 구현 하려는 경우 Spark 구조적 스트리밍을 사용할 수 있습니다. 사용자의 가상 컴퓨터에서 Event Hubs **EventProcessorHost** 를 실행 하 여 임의 스트리밍 처리를 수행할 수도 있습니다.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>솔루션이 다중 클라우드 또는 온-프레미스 환경에 있습니다.

Azure Stream Analytics은 Microsoft의 소유 기술 이며 Azure 에서만 사용할 수 있습니다. 클라우드 또는 온-프레미스에서 솔루션을 이식할 수 있어야 하는 경우 Spark 구조적 스트리밍 또는 스톰과 같은 오픈 소스 기술을 고려 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal를 사용 하 여 Stream Analytics 작업을 만듭니다.](stream-analytics-quick-create-portal.md)
* [Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio Code를 사용 하 여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
