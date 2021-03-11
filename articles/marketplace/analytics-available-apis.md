---
title: 상용 marketplace 분석 데이터에 액세스 하기 위한 Api
description: 이러한 Api를 사용 하 여 파트너 센터의 분석 데이터에 프로그래밍 방식으로 액세스 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584151"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>상용 marketplace 분석 데이터에 액세스 하기 위한 Api

다음은 상용 marketplace 분석 데이터 및 관련 기능에 액세스 하기 위한 Api 목록입니다.

- [데이터 집합 끌어오기 Api](#dataset-pull-apis)
- [쿼리 관리 Api](#query-management-apis)
- [보고서 관리 Api](#report-management-apis)
- [보고서 실행 끌어오기 Api](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>데이터 집합 끌어오기 Api

***표 1: 데이터 집합 끌어오기 Api***

| **API** | **기능** |
| --- | --- |
| [모든 데이터 세트 가져오기](analytics-api-get-all-datasets.md) | 사용 가능한 데이터 집합을 모두 가져옵니다. 데이터 집합 테이블, 열, 메트릭 및 시간 범위를 나열 합니다. |
|||

## <a name="query-management-apis"></a>쿼리 관리 Api

***표 2: 쿼리 관리 Api***

| **API** | **기능** |
| --- | --- |
| [보고서 쿼리 만들기](analytics-programmatic-access.md#create-report-query-api) | 열과 메트릭을 내보내야 하는 데이터 집합을 정의 하는 사용자 지정 쿼리를 만듭니다. |
| [보고서 쿼리 가져오기](analytics-api-get-report-queries.md) | 보고서에서 사용할 수 있는 모든 쿼리를 가져옵니다. 기본적으로 모든 시스템 및 사용자 정의 쿼리를 가져옵니다. |
| [보고서 쿼리 삭제](analytics-api-delete-report-queries.md) | 사용자 정의 쿼리를 삭제 합니다. |
|||

## <a name="report-management-apis"></a>보고서 관리 Api

***표 3: 보고서 관리 Api***

| **API** | **기능** |
| --- | --- |
| [보고서 만들기](analytics-programmatic-access.md#create-report-api) | 정기적으로 쿼리를 실행 하도록 예약 합니다. |
| [보고서 쿼리 시도](analytics-api-try-report-queries.md) | 보고서 쿼리 문을 실행 합니다. 파트너가 데이터의 예상 값을 확인 하는 데 사용할 수 있는 10 개의 레코드만 반환 합니다. |
| [보고서 가져오기](analytics-api-get-report.md) | 예약 된 모든 보고서를 가져옵니다. |
| [보고서 업데이트](analytics-api-update-report.md) | 보고서 매개 변수를 수정 합니다. |
| [보고서 삭제](analytics-api-delete-report.md) | 모든 보고서 및 보고서 실행 레코드를 삭제 합니다. |
| [보고서 실행 일시 중지](analytics-api-pause-report-executions.md) | 예약 된 보고서 실행을 일시 중지 합니다. |
| [보고서 실행 다시 시작](analytics-api-resume-report-executions.md) | 일시 중지 된 보고서의 예약 된 실행을 다시 시작 합니다. |
|||

## <a name="report-execution-pull-apis"></a>보고서 실행 끌어오기 Api

***표 4: 보고서 실행 끌어오기 Api***

| **API** | **기능** |
| --- | --- |
| [보고서 실행 가져오기](analytics-programmatic-access.md#get-report-executions-api) | 지정 된 보고서에 대해 발생 한 모든 실행을 가져옵니다. |
|||

## <a name="next-steps"></a>다음 단계

- [SWAGGER API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)을 통해 api를 사용해 볼 수 있습니다.
