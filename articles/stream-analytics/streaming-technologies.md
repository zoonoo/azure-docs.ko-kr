---
title: 실시간 분석 및 스트리밍 Azure에서 처리 기술 선택
description: 바로 실시간 분석 및 Azure에서 응용 프로그램을 빌드하려면 스트리밍 처리 기술 선택 하는 방법에 알아봅니다.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242702"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>실시간 분석 및 스트리밍 Azure에서 처리 기술 선택

실시간 분석 및 Azure에서의 스트리밍 처리에 사용할 수 있는 몇 가지 서비스가 있습니다. 이 문서에서는 응용 프로그램에 가장 적합 한 기술을 결정 하는 데 필요한 정보를 제공 합니다.

## <a name="when-to-use-azure-stream-analytics"></a>Azure Stream Analytics를 사용 하는 경우

Azure Stream Analytics는 Azure의 stream analytics에 대 한 권장 되는 서비스입니다. 다양 한 범위의 포함 되지만 제한 되지 않는지 여부를 지정 하는 시나리오를 의미 합니다.

* 데이터 시각화에 대 한 대시보드
* 실시간 [경고](stream-analytics-set-up-alerts.md) temporal 및 공간 패턴 또는 비정상 상태에서
* ETL(추출, 변환, 로드)
* [이벤트 소싱 패턴](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Azure Stream Analytics는 추가 응용 프로그램에 작업은 가장 빠른 방법은 분석을 스트리밍하려면 하 고 SQL 언어를 사용 하 여 Azure에서 실행 이미 알고 있습니다. Azure Stream Analytics 작업 서비스 이므로 클러스터를 관리 하는 시간을 투자 하지 않아도 및 작업 수준에서 99.9 %SLA 사용 하 여 가동 중지 시간에 걱정할 필요가 없습니다. 청구도 (192 스트리밍 단위)까지 확장 가능 하지만 (하나의 스트리밍 단위)를 시작 비용을 낮게 만들기 작업 수준에서 수행 됩니다. 것이 훨씬 더 비용 효율적으로 실행 하 고 클러스터를 유지 관리 하는 것 보다 몇 가지 Stream Analytics 작업을 실행 합니다.

Azure Stream Analytics는 다양 한 기본 제공 환경을 있습니다. 추가 설정 없이 다음 기능을 즉시 수행할 수 있습니다.

* 기본 제공 임시 연산자와 같은 [기간 이동 집계](stream-analytics-window-functions.md), 임시 조인 및 임시 분석 함수입니다.
* 기본 Azure [입력](stream-analytics-add-inputs.md) 하 고 [출력](stream-analytics-define-outputs.md) 어댑터
* 느린 변경에 대 한 지원을 [참조 데이터](stream-analytics-use-reference-data.md) (라고도 조회 테이블의 경우) 지 오 펜싱에 대 한 지리 공간 참조 데이터 조인 포함 합니다.
* 와 같은 통합 된 솔루션, [변칙 검색](stream-analytics-machine-learning-anomaly-detection.md)
* 동일한 쿼리에서 여러 기간
* 임의 시퀀스에서 여러 명의 temporal 연산자를 작성 하는 기능.
* Event Hubs의 방문을 출력 하기 위해 Event Hubs에 도착 하는 입력에서 종단 간 대기 시간이 100 밀리초에서 지속적으로 높은 처리량의 네트워크 지연이 Event hubs에 비롯 한

## <a name="when-to-use-other-technologies"></a>다른 기술을 사용 하는 경우

### <a name="you-need-to-input-from-or-output-to-kafka"></a>입력 또는 출력 Kafka을 해야

Azure Stream Analytics는 Apache Kafka 입력 또는 출력 어댑터 하지 않습니다. 이벤트에서 방문 했거나 Kafka를 보내야를 사용자 고유의 Kafka 클러스터를 실행 하는 요구 사항이 없는 경우에 이벤트 전송자를 변경 하지 않고 Event Hubs Kafka API를 사용 하 여 Event Hubs로 이벤트를 전송 하 여 Stream Analytics를 사용 하도록 계속 수 있습니다. 사용자 고유의 Kafka 클러스터를 실행 해야 수행 하는 경우 Spark 구조적 스트리밍에 완벽 하 게 지원 되는 사용할 수 있습니다 [Azure Databricks](../azure-databricks/index.yml), 또는에서 Storm [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md)합니다.

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Udf, Uda, 및 사용자 지정 deserializers JavaScript 이외의 언어로 작성 하려는 또는C#

Azure Stream Analytics를 클라우드 작업에 대 한 javascript에서 사용자 정의 함수 (UDF) 또는 사용자 정의 집계 (UDA) 지원 및 C# IoT Edge 작업에 대 한 합니다. C#사용자 정의 deserializers도 지원 됩니다. Java, Python과 같은 다른 언어에서 deserializer는 UDF, UDA를 구현 하려는 경우 Spark Structured Streaming을 사용할 수 있습니다. Event Hubs를 실행할 수도 있습니다 **EventProcessorHost** 자체 가상 머신에서 임의의 스트리밍 처리를 수행할 수 있습니다.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>다중 클라우드 또는 온-프레미스 환경에서 사용자의 솔루션은

Azure Stream Analytics는 Microsoft의 소유 기술 이며만 Azure에서 제공 됩니다. 클라우드 또는 온-프레미스 간에 이식 가능 하도록 솔루션을 할 경우 Storm 또는 Spark Structured Streaming 같은 오픈 소스 기술을 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [Azure portal을 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio Code를 사용 하 여 Stream Analytics 작업 만들기](quick-create-vs-code.md)