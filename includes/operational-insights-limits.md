---
title: 포함 파일
description: 포함 파일
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293458"
---
현재 사용량 기반 가격 책정 계층 2018 년 4 월에에서 도입 된 각 Log Analytics 작업 영역에는 다음과 같은 제한이 적용 됩니다.

|     | 2018 GB 당 |
| --- | --- | 
| 하루에 수집된 데이터 볼륨 | 없음 |
| 데이터 보존 기간 | 부터 730 30 일<sup>1</sup> |

가격 책정 계층 최근 레거시 각 Log Analytics 작업 영역에는 다음과 같은 제한이 적용 됩니다.

|  | 무료 | 독립 실행형 (GB 당) | 노드당 (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| 하루에 수집된 데이터 볼륨 |500MB<sup>2</sup> |없음 |없음 |
| 데이터 보존 기간 |7 일 | 부터 730 30 일<sup>1</sup> | 부터 730 30 일<sup>1</sup> |

가장 오래 된 레거시 가격 책정 계층을 각 Log Analytics 작업 영역에는 다음과 같은 제한이 적용 됩니다.

|  | Standard | Premium | 
| --- | --- | --- | --- | --- | --- |--- |
| 하루에 수집된 데이터 볼륨 | 없음 | 없음 | 
| 데이터 보존 기간 |30일 | 365일 |

<sup>1</sup>31 일이 지난 데이터 보존은 추가 비용에 대해 사용할 수 있습니다. [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.

<sup>2</sup>작업 영역 데이터 분석을 500MB 일별 데이터 전송 제한에 도달 하면 중지 하 고 다음 날의 시작 부분에 다시 시작 합니다. 1일은 UTC를 기준으로 합니다.

>[!NOTE]
>기간까지 사용한 Log Analytics에 따라 레거시 가격 책정 계층에 대 한 액세스를 할 수 있습니다. 에 대해 자세히 알아보세요 [Log Analytics 레거시 가격 책정 계층](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)합니다. 
>

다음 제한은 구독 당 Azure Log Analytics 리소스 (작업 영역)에 적용 합니다.

| 가격 책정 계층    | 구독 당 작업 영역의 수 | 설명
| --- | --- | --- |
| 무료 계층  | 10 | 이 제한을 늘릴 수 없습니다. |
| 무료 이외의 모든 계층 | N/A | 리소스 그룹 내 리소스 수가 구독 당 리소스 그룹의 수를 제한 하 고 있습니다. | 

Log Analytics Api에는 다음과 같은 제한이 적용 됩니다.

| Category | 제한 | 설명
| --- | --- | --- |
| 데이터 수집기 API | 단일 게시물에 대 한 최대 크기는 30MB입니다.<br>필드 값에 대 한 최대 크기는 32KB입니다. | 여러 개의 게시물에 더 큰 볼륨을 분할 합니다.<br>32KB보다 긴 필드는 잘립니다. |
| 검색 API | 집계 되지 않은 데이터용 5,000 개의 레코드로 반환 합니다.<br>집계 된 데이터에 대 한 500,000 개의 레코드입니다. | 집계 된 데이터를 포함 하는 검색 된 `summarize` 명령입니다.
 
