---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 44f9400a1ecebd3c204ec0b891c5d7fe01bdfd25
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51628173"
---
| 리소스 | 기본 제한 | 참고 |
| --- | --- | --- |
| Application Gateway |구독당 1,000 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 개인 1 |
| 프런트 엔드 포트 |40 | |
| 백 엔드 주소 풀 |40 | |
| 풀당 백 엔드 서버 |1200 | |
| HTTP 수신기 |40 | |
| HTTP 부하 분산 규칙 |400 |HTTP 수신기 개수 * n |
| 백 엔드 HTTP 설정 |40 | |
| 게이트웨이당 인스턴스 |75 | |
| SSL 인증서 |40 |HTTP 수신기당 1 |
| 인증 인증서 |40 | |
| 최소 요청 시간 초과 |1초 | |
| 최대 요청 시간 초과 |24시간 | |
| 사이트 수 |20 |HTTP 수신기당 1 |
| 수신기당 URL 맵 |1 | |
| URL 맵당 최대 경로 기반 규칙|100|
| 구성 리디렉션 |40| |
| 동시 WebSocket 연결 |5,000| |
|최대 URL 길이|8000|
| 최대 파일 업로드 크기 표준 |2GB | |
| 최대 파일 업로드 크기 WAF |중간 WAF 게이트웨이 - 100MB<br>대형 WAF 게이트웨이 - 500MB| |
|WAF 본문 크기 제한(파일 없음)|128KB|
