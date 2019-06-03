---
title: 포함 파일
description: 포함 파일
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238581"
---
다음 제한은 구독 당 Azure Log Analytics 리소스에 적용 합니다.

| Resource | 기본 제한 | 설명
| --- | --- | --- |
| 구독당 무료 작업 영역의 수 | 10 | 이 제한을 늘릴 수 없습니다. |
| 구독당 유료 작업 영역의 수 | N/A | 리소스 그룹 내 리소스 수가 구독 당 리소스 그룹의 수를 제한 하 고 있습니다. | 

>[!NOTE]
>2018 년 4 월 2 일부 터 새 구독에서 새 작업 영역을 자동으로 사용 합니다 *GB 당* 가격 책정 계획 합니다. 4 월 2 일 전에 만들어진 기존 구독 또는 기존 기업 계약 등록과 연결 된 구독에 대 한 새 작업 영역에 대 한 세 가지 가격 책정 계층에서 선택 계속 수 있습니다. 
>

각 Log Analytics 작업 영역에는 다음과 같은 제한이 적용 됩니다.

|  | 무료 | Standard | Premium | 독립 실행형 | OMS | GB당 |
| --- | --- | --- | --- | --- | --- |--- |
| 하루에 수집된 데이터 볼륨 |500MB<sup>1</sup> |없음 |없음 | 없음 | 없음 | 없음
| 데이터 보존 기간 |7 일 |1개월 |12개월 | 1개월<sup>2</sup> | 1개월<sup>2</sup>| 1개월<sup>2</sup>|

<sup>1</sup>500MB 일별 데이터 전송 제한, 데이터 분석 해당 고객에 게 도달 하는 경우 중지 하 고 다음 날의 시작 부분에 다시 시작 합니다. 1일은 UTC를 기준으로 합니다.

<sup>2</sup>독립 실행형, OMS 및 GB 당 가격 책정 계획에 대 한 데이터 보존 기간을 730 일로 늘릴 수 있습니다.

| Category | 제한 | 설명
| --- | --- | --- |
| 데이터 수집기 API | 단일 게시물에 대 한 최대 크기는 30MB입니다.<br>필드 값에 대 한 최대 크기는 32KB입니다. | 여러 개의 게시물에 더 큰 볼륨을 분할 합니다.<br>32KB보다 긴 필드는 잘립니다. |
| 검색 API | 집계 되지 않은 데이터용 5,000 개의 레코드로 반환 합니다.<br>집계 된 데이터에 대 한 500,000 개의 레코드입니다. | 집계 된 데이터를 포함 하는 검색 된 `summarize` 명령입니다.
 
