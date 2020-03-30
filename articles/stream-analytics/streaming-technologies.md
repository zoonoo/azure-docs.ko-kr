---
title: Azure에서 실시간 및 스트림 처리 솔루션 선택
description: Azure에서 응용 프로그램을 빌드하기 위해 적합한 실시간 분석 및 스트리밍 처리 기술을 선택하는 방법에 대해 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860251"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Azure에서 실시간 분석 및 스트리밍 처리 기술 선택

Azure에서 실시간 분석 및 스트리밍 처리에 사용할 수 있는 몇 가지 서비스가 있습니다. 이 문서에서는 응용 프로그램에 가장 적합한 기술을 결정하는 데 필요한 정보를 제공합니다.

## <a name="when-to-use-azure-stream-analytics"></a>Azure 스트림 분석을 사용하는 시기

Azure 스트림 분석은 Azure에서 스트림 분석에 권장되는 서비스입니다. 이 시나리오는 다음을 포함하지만 이에 국한되지 않는 광범위한 시나리오를 의미합니다.

* 데이터 시각화를 위한 대시보드
* 시간적 및 공간적 패턴 또는 이상으로부터의 실시간 [알림](stream-analytics-set-up-alerts.md)
* ETL(추출, 변환, 로드)
* [이벤트 소싱 패턴](/azure/architecture/patterns/event-sourcing)
* [IoT 에지](stream-analytics-edge.md)

응용 프로그램에 Azure Stream Analytics 작업을 추가하는 것은 이미 알고 있는 SQL 언어를 사용하여 Azure에서 스트리밍 분석을 시작하고 실행하는 가장 빠른 방법입니다. Azure Stream Analytics는 작업 서비스이므로 클러스터 를 관리하는 데 시간을 할애할 필요가 없으며 작업 수준에서 99.9%의 SLA로 가동 중지 시간에 대해 걱정할 필요가 없습니다. 또한 작업 수준에서 수행되므로 시작 비용은 낮게(하나의 스트리밍 단위) 확장 가능(최대 192개의 스트리밍 단위). 클러스터를 실행하고 유지 관리하는 것보다 몇 가지 Stream Analytics 작업을 실행하는 것이 훨씬 더 비용 효율적입니다.

Azure 스트림 분석에는 풍부한 즉시 환경이 있습니다. 추가 설정 없이 다음 기능을 즉시 활용할 수 있습니다.

* [창이 있는 집계,](stream-analytics-window-functions.md)임시 조인 및 시간 분석 함수와 같은 기본 제공 시간 연산자입니다.
* 네이티브 Azure [입력](stream-analytics-add-inputs.md) 및 [출력](stream-analytics-define-outputs.md) 어댑터
* 지오펜싱을 위한 지리 공간 참조 데이터와 조인하는 것을 포함하여 느린 변경 [참조 데이터(조회](stream-analytics-use-reference-data.md) 테이블이라고도 함)를 지원합니다.
* [변칙 탐지와](stream-analytics-machine-learning-anomaly-detection.md) 같은 통합 솔루션
* 동일한 쿼리의 여러 시간 창
* 임의의 시퀀스에서 여러 시간 연산자작성 기능.
* 이벤트 허브에 도착하는 입력에서 부터 지속적인 높은 처리량으로 이벤트 허브의 네트워크 지연을 포함한 이벤트 허브의 출력 에 이르는 100ms 종단 간 대기 시간

## <a name="when-to-use-other-technologies"></a>다른 기술을 사용할 때

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>자바스크립트 나 C 이외의 언어로 UdF, UDA 및 사용자 정의 디직렬화기를 작성하려는 경우 #

Azure Stream Analytics는 클라우드 작업에 대한 자바스크립트의 사용자 정의 함수(UDF) 또는 사용자 정의 집계(UDA)와 IoT Edge 작업에 대한 C#을 지원합니다. C# 사용자 정의 디직렬화기도 지원됩니다. 자바 나 파이썬과 같은 다른 언어로 직렬화기, UDF 또는 UDA를 구현하려는 경우 스파크 구조화 스트리밍을 사용할 수 있습니다. 또한 자체 가상 컴퓨터에서 이벤트 허브 **EventProcessorHost를** 실행하여 임의의 스트리밍 처리를 수행할 수도 있습니다.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>솔루션이 멀티 클라우드 또는 온-프레미스 환경에 있습니다.

Azure 스트림 분석은 Microsoft의 독점 기술이며 Azure에서만 사용할 수 있습니다. 클라우드 또는 온-프레미스에서 이식할 수 있는 솔루션이 필요한 경우 스파크 구조화 스트리밍 또는 Storm과 같은 오픈 소스 기술을 고려하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 포털을 사용하여 스트림 분석 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 스트림 분석 작업 만들기](stream-analytics-quick-create-vs.md)
* [비주얼 스튜디오 코드를 사용하여 스트림 분석 작업 만들기](quick-create-vs-code.md)
