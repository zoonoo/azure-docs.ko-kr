---
title: 이 Azure 센티널 통합 문서를 사용 하 여 데이터 커넥터의 상태 모니터링 | Microsoft Docs
description: 상태 모니터링 통합 문서를 사용 하 여 데이터 커넥터의 연결 및 성능을 추적 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656993"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>이 Azure 센티널 통합 문서를 사용 하 여 데이터 커넥터의 상태 모니터링

**데이터 커넥터 상태 모니터링 통합 문서** 를 사용 하면 Azure 센티널 내에서 데이터 커넥터의 상태, 연결 및 성능을 추적할 수 있습니다. 통합 문서는 추가 모니터를 제공 하 고, 변칙을 검색 하 고, 작업 영역의 데이터 수집 상태에 대 한 통찰력을 제공 합니다. 통합 문서의 논리를 사용 하 여 수집 데이터의 일반 상태를 모니터링 하 고 사용자 지정 보기 및 규칙 기반 경고를 작성할 수 있습니다.

## <a name="use-the-health-monitoring-workbook"></a>상태 모니터링 통합 문서 사용

1. Azure 센티널 포털의 **위협 관리** 메뉴에서 **통합 문서** 를 선택 합니다.

1. **통합 문서** 갤러리에서 검색 표시줄에 *상태* 를 입력 하 고 결과 중에서 **데이터 수집 상태 모니터링** 을 선택 합니다.

1. **템플릿 보기** 를 선택 하 여 통합 문서를 있는 그대로 사용 하거나 **저장** 을 선택 하 여 통합 문서의 편집 가능한 복사본을 만듭니다. 복사본을 만들 때 **저장 된 통합 문서 보기** 를 선택 합니다.

1. 통합 문서에서 먼저 보려는 **구독** 및 **작업 영역** 을 선택 하 고 **TimeRange** 를 정의 하 여 필요에 따라 데이터를 필터링 합니다. **도움말 표시** 토글을 사용 하 여 통합 문서에 대 한 현재 위치 설명을 표시 합니다.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="데이터 커넥터 상태 모니터링 통합 문서 방문 페이지" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

이 통합 문서에는 다음 세 개의 탭 섹션이 있습니다.

1. **개요** 탭에는 선택한 작업 영역에 있는 데이터 수집의 일반적인 상태 (볼륨 측정, EPS 비율 및 마지막으로 받은 시간)가 표시 됩니다.

1. **데이터 수집** 이상 탭은 테이블 및 데이터 소스에 따라 데이터 수집 프로세스의 비정상 상태를 검색 하는 데 도움이 됩니다. 각 탭에는 특정 테이블에 대 한 잘못 된 내용이 표시 됩니다. **일반** 탭에는 테이블 컬렉션이 포함 되어 있습니다. 변칙은 이상 **점수** 를 반환 하는 **series_decompose_anomalies ()** 함수를 사용 하 여 계산 됩니다. [이 함수에 대해 자세히 알아보세요](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). 계산할 함수에 대해 다음 매개 변수를 설정 합니다.

    - **AnomaliesTimeRange**: 이 시간 선택기는 데이터 컬렉션 변칙 보기에만 적용됩니다.
    - **SampleInterval**: 지정된 시간 범위 내에서 데이터가 샘플링되는 시간 간격입니다. 변칙 점수는 마지막 간격의 데이터에 대해서만 계산됩니다.
    - **PositiveAlertThreshold**: 이 값은 양의 변칙 점수 임계값을 정의합니다. 10 진수 값을 허용 합니다.
    - **NegativeAlertThreshold**: 이 값은 음의 변칙 점수 임계값을 정의합니다. 10 진수 값을 허용 합니다.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="데이터 커넥터 상태 모니터링 통합 문서 예외 페이지" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. **에이전트 정보** 탭에는 Azure VM, 다른 클라우드 VM, 온-프레미스 vm 또는 물리적 인지 여부에 관계 없이 다양 한 컴퓨터에 설치 된 Log Analytics 에이전트의 상태에 대 한 정보가 표시 됩니다. 다음을 모니터링할 수 있습니다.

   - 시스템 위치

   - 하트 비트 상태 및 대기 시간

   - 사용 가능한 메모리 및 디스크 공간

   - 에이전트 작업

    이 섹션에서는 사용자의 컴퓨터 환경을 설명 하는 탭을 선택 해야 합니다. Azure Arc 관리 컴퓨터만 보려면 **azure 관리 컴퓨터** 탭을 선택 합니다. **모든 컴퓨터** 탭을 선택 하 여 Log Analytics 에이전트가 설치 된 관리 되는 컴퓨터와 비 Azure 컴퓨터를 모두 볼 수 있습니다.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="데이터 커넥터 상태 모니터링 통합 문서 에이전트 정보 페이지" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>다음 단계
데이터를 [Azure 센티널에](quickstart-onboard.md)등록 하 고, [데이터 원본을 연결](connect-data-sources.md)하 고, [데이터와 잠재적 위협을 파악](quickstart-get-visibility.md)하는 방법에 대해 알아봅니다.