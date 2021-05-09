---
title: 보고서 쿼리 API 사용해 보기
description: 이 API를 사용하여 상업용 Marketplace 분석 보고서에 대한 보고서 쿼리를 실행합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583934"
---
# <a name="try-report-queries-api"></a>보고서 쿼리 API 사용해 보기

이 API는 보고서 쿼리 문을 실행합니다. API는 데이터가 예상한 대로인지 확인하는 데 파트너가 사용할 수 있는 10개의 레코드만 반환합니다.

> [!IMPORTANT]
> 이 API의 쿼리 실행 제한 시간은 100초입니다. API에 100초 이상이 소요될 경우 쿼리가 구문상 정확할 가능성이 높습니다. 그렇지 않으면 200 이외의 오류 코드를 수신하게 됩니다. 쿼리 구문이 올바르면 실제 보고서 생성이 성공합니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**요청 헤더**

| **Header** | **형식** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 형식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
|||

**QueryParameter**

| **매개 변수 이름** | **형식** | **설명** |
| --- | --- | --- |
| `exportQuery` | string | 실행해야 하는 보고서 쿼리 문자열 |
| `queryId` | 문자열 | 유효한 기존 쿼리 ID |
|||

**경로**  **매개 변수**

없음

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드: 쿼리 실행의 상위 10개 행
