---
title: 포함 파일
description: 포함 파일
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 361fb18574e90fc46e45bff8914c51ee1afa2f96
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89411620"
---
| 리소스 | 제한 |
| --- | --- |
| 구독당 Azure Front Door 리소스 수 | 100 |
| 리소스당 사용자 지정 도메인을 포함하는 프런트 엔드 호스트 수 | 500 |
| 리소스당 라우팅 규칙 | 500 |
| 리소스당 백 엔드 풀 수 | 50 |
| 백 엔드 풀당 백 엔드 수 | 100 |
| 라우팅 규칙에 일치하는 경로 패턴 | 25 |
| 단일 캐시 제거 호출의 URL 수 | 100 |
| 정책당 사용자 지정 웹 애플리케이션 방화벽 규칙 | 100 |
| 구독당 웹 애플리케이션 방화벽 정책 수 | 100 |
| 사용자 지정 규칙당 웹 애플리케이션 방화벽 일치 조건 수 | 10 |
| 일치 조건당 웹 애플리케이션 방화벽 IP 주소 범위 수 | 600 |
| 일치 조건당 웹 애플리케이션 방화벽 문자열 일치 값 수 | 10 |
| 웹 애플리케이션 방화벽 문자열 일치 값 길이 | 256 |
| 웹 애플리케이션 방화벽 POST 본문 매개 변수 이름 길이 | 256 |
| 웹 애플리케이션 방화벽 HTTP 헤더 이름 길이 | 256 |
| 웹 애플리케이션 방화벽 쿠키 이름 길이 | 256 |
| 검사되는 웹 애플리케이션 방화벽 HTTP 요청 본문 크기 | 128KB |
| 웹 애플리케이션 방화벽 사용자 지정 응답 본문 길이 | 2KB |

### <a name="timeout-values"></a>시간 제한 값
#### <a name="client-to-front-door"></a>클라이언트-Front Door
* Front Door에는 유휴 TCP 연결 시간 제한(61초)이 있습니다.

#### <a name="front-door-to-application-back-end"></a>Front Door-애플리케이션 백 엔드 연결
* 응답이 청크 분할 응답인 경우 첫 번째 청크를 받을 때 200이 반환됩니다.
* HTTP 요청이 백 엔드로 전달되면 Front Door에서 백 엔드의 첫 번째 패킷을 30초 동안 기다립니다. 그런 다음, 503 오류를 클라이언트에 반환합니다. 이 값은 API의 sendRecvTimeoutSeconds 필드를 통해 구성할 수 있습니다.
    * 캐싱 시나리오의 경우 이 시간 제한을 구성할 수 없으므로 요청이 캐시되고 Front Door 또는 백 엔드에서 첫 번째 패킷에 대해 30초 넘게 걸리면 504 오류가 클라이언트에 반환됩니다. 
* 백 엔드로부터 첫 번째 패킷을 받으면 Front Door에서 30초(유휴 시간 제한) 동안 기다립니다. 그런 다음, 503 오류를 클라이언트에 반환합니다. 이 시간 제한 값은 구성할 수 없습니다.
* Front Door-백 엔드 TCP 세션 시간 제한은 90초입니다.

### <a name="upload-and-download-data-limit"></a>업로드 및 다운로드 데이터 제한

|  | CTE(청크 분할 전송 인코딩) 사용 | HTTP 청크 분할 사용 안 함 |
| ---- | ------- | ------- |
| **다운로드** | 다운로드 크기 제한이 없습니다. | 다운로드 크기 제한이 없습니다. |
| **업로드** |    각 CTE 업로드가 2GB 미만이면 제한이 없습니다. | 크기는 2GB보다 클 수 없습니다. |

### <a name="other-limits"></a>기타 제한
* 최대 URL 크기 - 8,192바이트 - 원시 URL의 최대 길이(URL의 스키마 + 호스트 이름 + 포트 + 경로 + 쿼리 문자열)를 지정합니다.
* 최대 쿼리 문자열 크기 - 4,096바이트 - 쿼리 문자열의 최대 길이(바이트)를 지정합니다.
* 상태 프로브 URL의 최대 HTTP 응답 헤더 크기 - 4,096바이트 - 상태 프로브의 모든 응답 헤더의 최대 길이를 지정합니다. 
