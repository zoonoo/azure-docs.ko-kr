---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211878"
---
| 리소스 | 기본 제한 | 참고 |
| --- | --- | --- |
| Application Gateway |구독당 1,000 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 개인 1 |
| 프런트 엔드 포트 |100<sup>1</sup> | |
| 백 엔드 주소 풀 |100<sup>1</sup> | |
| 풀당 백 엔드 서버 |1200 | |
| HTTP 수신기 |100<sup>1</sup> | |
| HTTP 부하 분산 규칙 |100<sup>1</sup> | |
| 백 엔드 HTTP 설정 |100<sup>1</sup> | |
| 게이트웨이당 인스턴스 |32 | |
| SSL 인증서 |100<sup>1</sup> |HTTP 수신기당 1 |
| 인증 인증서 |100 | |
| 신뢰할 수 있는 루트 인증서 |100 | |
| 최소 요청 시간 초과 |1초 | |
| 최대 요청 시간 초과 |24시간 | |
| 사이트 수 |100<sup>1</sup> |HTTP 수신기당 1 |
| 수신기당 URL 맵 |1 | |
| URL 맵당 최대 경로 기반 규칙|100||
| 구성 리디렉션 |100<sup>1</sup>| |
| 동시 WebSocket 연결 |5,000| |
| 최대 URL 길이|8000||
| 최대 파일 업로드 크기 표준 |2GB | |
| 최대 파일 업로드 크기 WAF |중간 WAF 게이트웨이 - 100MB<br>대형 WAF 게이트웨이 - 500MB| |
| WAF 본문 크기 제한(파일 없음)|128KB||

<sup>1</sup> WAF 지원 SKU의 경우에는 최적의 성능을 위해 리소스 수를 40개로 제한하는 것이 좋습니다.
