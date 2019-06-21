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
ms.openlocfilehash: dfcf47a95d1dbff34ff322768fc4ac6c9674cff4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296001"
---
**데이터 컬렉션 볼륨 및 보존** 

| 계층 | 하루 제한 | 데이터 보존 | 주석 |
|:---|:---|:---|:---|
| 현재 GB 당 가격 책정 계층<br>(2018 년 4 월 도입 됨) | 제한 없음 | 30-730 일 | 31 일이 지난 데이터 보존 기간은 추가 비용에 대해 사용할 수 있습니다. Azure Monitor 가격 책정에 대해 알아봅니다. |
| 레거시 무료 계층<br>(2016 년 4 월 도입 됨) | 500MB | 7 일 | 작업 영역 데이터 분석을 500MB 일별 데이터 전송 제한에 도달 하면 중지 하 고 날의 시작 부분에 다시 시작 합니다. 1일은 UTC를 기준으로 합니다. |
| 레거시 GB 당 독립 실행형 계층<br>(2016 년 4 월 도입 됨) | 제한 없음 | 부터 730 30 일 | 31 일이 지난 데이터 보존 기간은 추가 비용에 대해 사용할 수 있습니다. Azure Monitor 가격 책정에 대해 알아봅니다. |
| 레거시 노드당 (OMS)<br>(2016 년 4 월 도입 됨) | 제한 없음 | 부터 730 30 일 | 31 일이 지난 데이터 보존 기간은 추가 비용에 대해 사용할 수 있습니다. Azure Monitor 가격 책정에 대해 알아봅니다. |
| 레거시 표준 계층 | 제한 없음 | 30일  | 보존을 조정할 수 없습니다. |
| 기존 프리미엄 계층 | 제한 없음 | 365일  | 보존을 조정할 수 없습니다. |

**구독 당 작업 영역의 수입니다.**

| 가격 책정 계층    | 작업 영역 한도 | 설명
|:---|:---|:---|
| 무료 계층  | 10 | 이 제한을 늘릴 수 없습니다. |
| 다른 모든 계층 | 제한 없음 | 리소스 그룹 내 리소스 수가 구독 당 리소스 그룹의 수를 제한 하 고 있습니다. |

**Azure Portal**

| Category | 제한 | 설명 |
|:---|:---|:---|
| 로그 쿼리를 통해 반환 되는 최대 레코드입니다. | 10000 | 쿼리 범위, 기간 및 필터를 사용 하 여 쿼리에서 결과 줄입니다. |


**데이터 수집기 API**

| Category | 제한 | 설명 |
|:---|:---|:---|
| 단일 게시물에 대 한 최대 크기 | 30MB | 여러 개의 게시물에 더 큰 볼륨을 분할 합니다. |
| 필드 값에 대 한 최대 크기  | 32KB | 32KB보다 긴 필드는 잘립니다. |

**검색 API**

| Category | 제한 | 설명 |
|:---|:---|:---|
| 집계 되지 않은 데이터에 대 한 반환 되는 최대 레코드입니다. | 5,000 | |
| 집계 된 데이터에 대 한 최대 레코드 | 500,000 | 집계 된 데이터를 포함 하는 검색 된 `summarize` 명령입니다. |
| 반환 되는 데이터의 최대 크기 | 64,000,000 바이트 (~ 61 MiB)| |
| 최대 쿼리 실행 시간 | 10분 | 참조 [시간 제한을](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) 세부 정보에 대 한 합니다.  |
| 최대 요청 속도 | AAD 사용자 또는 클라이언트 IP 주소 30 초 당 200 요청 | 참조 [속도 제한](https://dev.loganalytics.io/documentation/Using-the-API/Limits) 세부 정보에 대 한 합니다. |

**일반 작업 영역 제한**

| Category | 제한 | 설명 |
|:---|:---|:---|
| 테이블의 최대 열         | 500 | |
| 열 이름에 대 한 최대 문자 수 | 500 | |
| 지역에서 용량 | 미국 중서부 | 현재 임시 용량 한도에 있기 때문이 지역에서 새 작업 영역을 만들 수 없습니다. 이 한도 2019 년 9 월의 끝에서 처리할 예정 되어 있습니다. |
| 데이터 내보내기 | 현재 사용할 수 없음 | 집계 및 데이터 내보내기에 Azure 함수 또는 논리 앱을 사용 합니다. | 

>[!NOTE]
>기간까지 사용한 Log Analytics에 따라 레거시 가격 책정 계층에 대 한 액세스를 할 수 있습니다. 에 대해 자세히 알아보세요 [Log Analytics 레거시 가격 책정 계층](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)합니다. 