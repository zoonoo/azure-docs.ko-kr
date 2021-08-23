---
title: 상업용 Marketplace 분석 데이터에 액세스하기 위한 API
description: 이러한 API를 사용하여 파트너 센터의 분석 데이터에 프로그래밍 방식으로 액세스합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 07dae73901fca44200d6b4fb8c8149b69728d065
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567264"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>상업용 Marketplace 분석 데이터에 액세스하기 위한 API

다음은 상업용 Marketplace 분석 데이터 및 관련 기능에 액세스하기 위한 API의 목록입니다.

- [데이터 세트 풀하기 API](#dataset-pull-apis)
- [쿼리 관리 API](#query-management-apis)
- [보고서 관리 API](#report-management-apis)
- [보고서 실행 풀하기 API](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>데이터 세트 풀하기 API

***표 1: 데이터 세트 풀하기 API***

| **API** | **기능** |
| --- | --- |
| [모든 데이터 세트 가져오기](analytics-api-get-all-datasets.md) | 사용 가능한 데이터 세트를 모두 가져옵니다. 데이터 세트는 테이블, 열, 메트릭, 시간 범위를 나열합니다. |
|||

## <a name="query-management-apis"></a>쿼리 관리 API

***표 2: 쿼리 관리 API***

| **API** | **기능** |
| --- | --- |
| [보고서 쿼리 만들기](analytics-programmatic-access.md#create-report-query-api) | 열과 메트릭을 내보내야 하는 데이터 세트를 정의하는 사용자 지정 쿼리를 만듭니다. |
| [보고서 쿼리 가져오기](analytics-api-get-report-queries.md) | 보고서에서 사용할 수 있는 모든 쿼리를 가져옵니다. 기본적으로 모든 시스템 및 사용자 정의 쿼리를 가져옵니다. |
| [보고서 쿼리 삭제](analytics-api-delete-report-queries.md) | 사용자 정의 쿼리를 삭제합니다. |
|||

## <a name="report-management-apis"></a>보고서 관리 API

***표 3: 보고서 관리 API***

| **API** | **기능** |
| --- | --- |
| [보고서 만들기](analytics-programmatic-access.md#create-report-api) | 정기적으로 쿼리를 실행하도록 예약합니다. |
| [보고서 쿼리 사용해 보기](analytics-api-try-report-queries.md) | 보고서 쿼리 문을 실행합니다. 데이터가 예상한 대로인지 확인하는 데 파트너가 사용할 수 있는 10개의 레코드만 반환합니다. |
| [보고서 가져오기](analytics-api-get-report.md) | 예약된 모든 보고서를 가져옵니다. |
| [보고서 업데이트](analytics-api-update-report.md) | 보고서 매개 변수를 수정합니다. |
| [보고서 삭제](analytics-api-delete-report.md) | 모든 보고서 및 보고서 실행 레코드를 삭제합니다. |
| [보고서 실행 일시 중지](analytics-api-pause-report-executions.md) | 예약된 보고서 실행을 일시 중지합니다. |
| [보고서 실행 계속](analytics-api-resume-report-executions.md) | 일시 중지된 보고서의 예약된 실행을 계속합니다. |
|||

## <a name="report-execution-pull-apis"></a>보고서 실행 풀하기 API

***표 4: 보고서 실행 풀하기 API***

| **API** | **기능** |
| --- | --- |
| [보고서 실행 가져오기](analytics-programmatic-access.md#get-report-executions-api) | 지정된 보고서에 대해 발생한 모든 실행을 가져옵니다. |
|||

## <a name="next-steps"></a>다음 단계

- [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)을 통해 API를 사용해 볼 수 있습니다.
