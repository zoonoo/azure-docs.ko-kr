---
title: 포함 파일
description: 포함 파일
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006517"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 구독당 Front Door 리소스 | 100 |
| 리소스당 사용자 지정 도메인을 포함한 프런트 엔드 호스트 | 100 |
| 리소스당 라우팅 규칙 | 100 |
| 리소스당 백 엔드 풀 | 50 |
| 백 엔드 풀당 백 엔드 | 100 |
| 라우팅 규칙에 일치하는 경로 패턴 | 25 |
| 정책당 사용자 지정 웹 응용 프로그램 방화벽 규칙 | 10 |
| 리소스당 웹 응용 프로그램 방화벽 정책 | 100 |

### <a name="timeout-values"></a>시간 제한 값
#### <a name="client-to-front-door"></a>클라이언트-Front Door
- Front Door에는 유휴 TCP 연결 시간 제한(61초)이 있습니다.
#### <a name="front-door-to-application-backend"></a>Front Door-응용 프로그램 백 엔드
- 청크 분할 응답인 경우 첫 번째 청크가 수신될 경우/때 200이 반환됩니다.
- HTTP 요청이 백 엔드로 전달되면 Front Door는 클라이언트에 503 오류를 반환하기 전에 백 엔드의 첫 번째 패킷을 30초 동안 기다립니다.
- 백 엔드에서 첫 번째 패킷을 수신한 후 Front Door는 클라이언트에 503 오류를 반환하기 전에 30초(유휴 시간 제한) 동안 대기합니다.
- Front Door-백 엔드 TCP 세션 시간 제한은 30분입니다.

### <a name="upload--download-data-limit"></a>업로드/다운로드 데이터 제한

|  | CTE(청크 분할 전송 인코딩) 포함 | HTTP 청크 분할 없음 |
| ---- | ------- | ------- |
| **다운로드** | 다운로드 크기에는 제한이 없습니다. | 다운로드 크기에는 제한이 없습니다. |
| **업로드** |  각 CTE 업로드가 28.6MB 미만이면 제한이 없습니다. | 크기는 28.6MB보다 클 수 없습니다. |
