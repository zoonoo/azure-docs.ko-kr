---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334800"
---
**데이터 수집 볼륨 및 보존** 

| 계층 | 일일 한도 | 데이터 보존 | 주석 |
|:---|:---|:---|:---|
| GB당 현재 가격 책정 계층<br>(2018년 4월 출시) | 제한 없음 | 30 - 730 일 | 31일을 초과하는 데이터 보존은 추가 요금으로 사용할 수 있습니다. Azure Monitor 가격에 대해 자세히 알아봅니다. |
| 레거시 프리 티어<br>(2016년 4월 출시) | 500MB | 7 일 | 작업 영역이 일일 500MB 제한에 도달하면 데이터 수집이 중지되고 다음 날 시작 시 다시 시작됩니다. 1일은 UTC를 기준으로 합니다. Azure Security Center에서 수집한 데이터는 일일 500MB 제한에 포함되지 않으며 이 한도를 초과하여 계속 수집됩니다.  |
| GB 계층당 레거시 독립실행형<br>(2016년 4월 출시) | 제한 없음 | 30 ~ 730 일 | 31일을 초과하는 데이터 보존은 추가 요금으로 사용할 수 있습니다. Azure Monitor 가격에 대해 자세히 알아봅니다. |
| 노드당 레거시(OMS)<br>(2016년 4월 출시) | 제한 없음 | 30 ~ 730 일 | 31일을 초과하는 데이터 보존은 추가 요금으로 사용할 수 있습니다. Azure Monitor 가격에 대해 자세히 알아봅니다. |
| 레거시 표준 계층 | 제한 없음 | 30일  | 보존을 조정할 수 없습니다. |
| 레거시 프리미엄 계층 | 제한 없음 | 365일  | 보존을 조정할 수 없습니다. |

**구독당 작업 영역 수입니다.**

| 가격 책정 계층    | 작업 영역 제한 | 주석
|:---|:---|:---|
| 무료 계층  | 10 | 이 제한은 늘릴 수 없습니다. |
| 기타 모든 계층 | 제한 없음 | 리소스 그룹 내의 리소스 수와 구독당 리소스 그룹 수에 따라 제한됩니다. |

**Azure 포털**

| Category | 제한 | 주석 |
|:---|:---|:---|
| 로그 쿼리에서 반환되는 최대 레코드 | 10000 | 쿼리 범위, 시간 범위 및 쿼리의 필터를 사용하여 결과를 줄입니다. |


**데이터 수집기 API**

| Category | 제한 | 주석 |
|:---|:---|:---|
| 단일 게시물의 최대 크기 | 30 MB | 더 큰 볼륨을 여러 개의 게시물로 분할합니다. |
| 필드 값의 최대 크기  | 32KB | 32KB보다 긴 필드는 잘립니다. |

**검색 API**

| Category | 제한 | 주석 |
|:---|:---|:---|
| 단일 쿼리에서 반환되는 최대 레코드 | 500,000 | |
| 반환된 최대 데이터 크기 | 64,000,000바이트(~61MiB)| |
| 최대 쿼리 실행 시간 | 10분 | 자세한 내용은 [시간 시간을](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) 참조하십시오.  |
| 최대 요청 속도 | AAD 사용자 또는 클라이언트 IP 주소당 30초당 200건의 요청 | 자세한 내용은 [요금 제한을](https://dev.loganalytics.io/documentation/Using-the-API/Limits) 참조하십시오. |

**일반 작업 영역 제한**

| Category | 제한 | 주석 |
|:---|:---|:---|
| 테이블의 최대 열         | 500 | |
| 열 이름의 최대 문자 | 500 | |
| 데이터 내보내기 | 현재 사용할 수 없음 | Azure Function 또는 논리 앱을 사용하여 데이터를 집계하고 내보냅니다. | 

**데이터 수집 볼륨 속도**


Azure Monitor는 점점 더 빠른 속도로 매달 테라바이트 단위의 데이터를 보내는 수천 명의 고객을 처리하는 대규모 데이터 서비스입니다. [진단 설정을](../articles/azure-monitor/platform/diagnostic-settings.md) 사용하여 Azure 리소스에서 전송된 데이터에 대한 기본 수집 볼륨 속도 제한은 작업 영역당 약 **6GB/min입니다.** 실제 크기는 로그 길이와 압축 비율에 따라 데이터 형식간에 따라 달라질 수 있으므로 대략적인 값입니다. 이 제한은 에이전트 또는 데이터 [수집API에서](../articles/azure-monitor/platform/data-collector-api.md)전송되는 데이터에는 적용되지 않습니다.

더 높은 속도로 데이터를 단일 작업 공간으로 보내면 일부 데이터가 삭제되고 임계값을 계속 초과하는 동안 6시간마다 작업 영역의 *작업* 테이블로 이벤트가 전송됩니다. 인비많이트 볼륨이 속도 제한을 계속 초과하거나 곧 도달하려는 경우 지원 요청을 열어 작업 영역의 증가를 요청할 수 있습니다.
 
작업 영역에서 이러한 이벤트에 대한 알림을 얻으려면 결과 수에 따라 경고 논리를 기반으로 다음 쿼리를 사용하여 [로그 경고 규칙을](../articles/azure-monitor/platform/alerts-log.md) 만듭니다.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Log Analytics를 사용한 기간에 따라 레거시 가격 책정 계층에 액세스할 수 있습니다. Log Analytics 레거시 가격 책정 계층에 대해 자세히 [알아보세요.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) 