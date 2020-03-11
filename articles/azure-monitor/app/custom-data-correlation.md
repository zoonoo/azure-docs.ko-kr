---
title: Azure Application Insights | Microsoft Docs
description: Application Insights 데이터를 데이터 보강 또는 조회 테이블, Application Insights 없는 데이터 원본 및 사용자 지정 데이터와 같은 다른 데이터 집합과 상호 연결 합니다.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082764"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>사용자 지정 데이터 원본을 사용하여 Application Insights 데이터 상호 연결

Application Insights는 여러 다른 데이터 형식을 수집합니다(예외, 추적, 페이지 보기 및 기타). 이 방법은 응용 프로그램의 성능, 안정성 및 사용을 조사 하는 데 충분 하지만 Application Insights에 저장 된 데이터를 완전히 사용자 지정 데이터 집합에 연결 하는 것이 유용한 경우가 있습니다.

사용자 지정 데이터를 사용하려는 상황은 다음과 같습니다.

- 데이터 보강 또는 조회 테이블: 예를 들어 서버 이름을 서버의 소유자 및 찾을 수 있는 랩 위치로 보완합니다. 
- Application Insights 데이터 원본과 상관 관계: 예를 들어 배송 시간 예측값의 정확도를 확인하려면 구매 처리 서비스의 정보와 웹 스토어에서 구매에 대한 데이터를 상관 관계를 만듭니다. 
- 완전한 사용자 지정 데이터: 대부분의 고객은 Application Insights를 지원하는 Azure Monitor 로그 플랫폼의 쿼리 언어 및 성능을 즐겨 사용하고 Application Insights와 전혀 관련되지 않은 데이터를 쿼리하는 데 사용합니다. 예를 들어 [여기](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)에서 설명한 대로 스마트 홈 설치의 일부로 태양 패널 성능을 추적하려고 합니다.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Application Insights와 사용자 지정 데이터 상관 관계 만들기 

Application Insights가 강력한 Azure Monitor 로그 플랫폼에서 지원되므로 Azure Monitor의 모든 기능을 사용하여 데이터를 수집할 수 있습니다. 그런 다음이 사용자 지정 데이터와 Azure Monitor 로그에서 사용할 수 있는 데이터의 상관 관계를 지정 하는 "join" 연산자를 사용 하 여 쿼리를 작성 합니다. 

## <a name="ingesting-data"></a>데이터 수집

이 섹션에서는 Azure Monitor 로그로 데이터를 가져오는 방법을 검토합니다.

아직 없는 경우 "작업 영역 만들기" 단계를 통해 [이러한 지침](../learn/quick-collect-azurevm.md) 에 따라 새 Log Analytics 작업 영역을 프로 비전 합니다.

Azure Monitor로 로그 데이터 보내기를 시작하려면 다음과 같은 여러 옵션이 있습니다.

- 동기 메커니즘의 경우 직접 [데이터 수집기 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) 를 호출 하거나 논리 앱 커넥터를 사용할 수 있습니다. "Azure Log Analytics"을 찾아 "데이터 전송" 옵션을 선택 하면 됩니다.

  ![스크린샷 선택 및 작업](./media/custom-data-correlation/01-logic-app-connector.png)  

- 비동기 옵션의 경우 데이터 수집기 API를 사용하여 처리 파이프라인을 빌드합니다. 자세한 내용은 [이 아티클](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api)을 참조하세요.

## <a name="correlating-data"></a>데이터 상관 관계 설정

Application Insights는 Azure Monitor 로그 플랫폼을 기반으로 합니다. 따라서 [교차 리소스 조인](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)을 사용하여 Application Insights 데이터와 Azure Monitor로 수집한 모든 데이터의 상관 관계를 만들 수 있습니다.

예를 들어 "myLA" 라는 Log Analytics 작업 영역에서 "LabLocations_CL" 라는 테이블에 랩 인벤토리 및 위치를 수집할 수 있습니다. 그런 다음 "myAI" 라는 Application Insights 앱에서 추적 된 요청을 검토 하 고 요청을 제공 하는 컴퓨터 이름을 이전에 언급 한 사용자 지정 테이블에 저장 된 이러한 컴퓨터의 위치와 상관 관계를 지정 하려는 경우 다음 쿼리를 실행할 수 있습니다. Application Insights 또는 Azure Monitor 컨텍스트입니다.

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>다음 단계

- [데이터 수집기 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) 참조를 체크 아웃합니다.
- [교차 리소스 조인](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)에 대한 자세한 정보입니다.
