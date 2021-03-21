---
title: 보고서 쿼리 API 사용해 보기
description: 이 API를 사용 하 여 상업적 marketplace 분석 보고서에 대 한 보고서 쿼리를 실행 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583934"
---
# <a name="try-report-queries-api"></a>보고서 쿼리 API 사용해 보기

이 API는 보고서 쿼리 문을 실행 합니다. API는 파트너가 데이터를 예상한 대로 확인 하는 데 사용할 수 있는 10 개의 레코드만 반환 합니다.

> [!IMPORTANT]
> 이 API의 쿼리 실행 제한 시간은 100 초입니다. API가 100 초를 초과 하는 것으로 확인 되는 경우 쿼리 구문이 정확 하거나 200 이외의 오류 코드를 수신 하는 것일 가능성이 높습니다. 쿼리 구문이 올바르면 실제 보고서 생성이 성공 합니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**요청 헤더**

| **Header** | **Type** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. 양식의 Azure AD (Azure Active Directory) 액세스 토큰 `Bearer <token>` |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
|||

**QueryParameter**

| **매개 변수 이름** | **Type** | **설명** |
| --- | --- | --- |
| `exportQuery` | string | 실행 해야 하는 보고서 쿼리 문자열 |
| `queryId` | 문자열 | 유효한 기존 쿼리 ID |
|||

**Path**  **매개 변수**

없음

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드: 쿼리 실행의 상위 10 개 행
