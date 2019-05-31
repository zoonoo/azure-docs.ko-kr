---
title: 포함 파일
description: 포함 파일
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e1f5a1c8229544d97d9ff64748390f0d5237ab97
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238754"
---
| Resource | 기본/최대 제한 |
| --- | --- |
| 구독 당 azure 프런트 도어 서비스 리소스 | 100 |
| 리소스 당 사용자 지정 도메인을 포함 하는 프런트 엔드 호스트 | 100 |
| 리소스당 라우팅 규칙 | 100 |
| 백 엔드 풀 리소스 당 | 50 |
| 백 엔드 풀 당 백 엔드 | 100 |
| 라우팅 규칙에 일치하는 경로 패턴 | 25 |
| 정책당 사용자 지정 웹 애플리케이션 방화벽 규칙 | 10 |
| 리소스당 웹 애플리케이션 방화벽 정책 | 100 |

### <a name="timeout-values"></a>시간 제한 값
#### <a name="client-to-front-door"></a>클라이언트-Front Door
- Front Door에는 유휴 TCP 연결 시간 제한(61초)이 있습니다.

#### <a name="front-door-to-application-back-end"></a>응용 프로그램 백 엔드에 프런트 도어
- 응답은 청크 분할 된 응답을 하는 경우 또는 첫 번째 청크 수신 되 면 200이 반환 됩니다.
- HTTP 요청을 백 엔드로 전달 프런트 도어 백 엔드에서 첫 번째 패킷 30 초 동안 기다립니다. 그런 다음 클라이언트에 503 오류를 반환합니다.
- 첫 번째 패킷을 백 엔드에서 받은 후 첫 번째 관문 유휴 시간에서 30 초 동안 대기 합니다. 그런 다음 클라이언트에 503 오류를 반환합니다.
- 백 엔드 TCP 세션 제한 시간에 첫 번째 관문 30 분입니다.

### <a name="upload-and-download-data-limit"></a>업로드 및 다운로드 데이터 제한

|  | 청크 분할된 전송 인코딩을 (CTE)를 사용 하 여 | HTTP 청크 없이 |
| ---- | ------- | ------- |
| **다운로드** | 다운로드 크기에는 제한이 없습니다. | 다운로드 크기에는 제한이 없습니다. |
| **업로드** |  각 CTE 업로드 2GB 미만인 경우에 제한이 없습니다. | 크기는 2GB 보다 클 수 없습니다. |
