---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 5ad1339c04444bcb4cc550be26e239e65227d2ce
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58494756"
---
| 리소스 | 기본 제한 | 참고 |
| --- | --- | --- |
| Azure Application Gateway |구독 당 1,000 개 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 개인 1 |
| 프런트 엔드 포트 |100<sup>1</sup> | |
| 백 엔드 주소 풀 |100<sup>1</sup> | |
| 풀당 백 엔드 서버 |1,200 | |
| HTTP 수신기 |100<sup>1</sup> | |
| HTTP 부하 분산 규칙 |100<sup>1</sup> | |
| 백 엔드 HTTP 설정 |100<sup>1</sup> | |
| 게이트웨이당 인스턴스 |32 | |
| SSL 인증서 |100<sup>1</sup> |HTTP 수신기 당 1 |
| 인증 인증서 |100 | |
| 신뢰할 수 있는 루트 인증서 |100 | |
| 최소 요청 시간 초과 |1초 | |
| 최대 요청 시간 초과 |24시간 | |
| 사이트 수 |100<sup>1</sup> |HTTP 수신기 당 1 |
| 수신기당 URL 맵 |1 | |
| URL 맵당 최대 경로 기반 규칙|100||
| 구성 리디렉션 |100<sup>1</sup>| |
| 동시 WebSocket 연결 |보통 게이트웨이에 20k<br> 큰 게이트웨이에 50k| |
| 최대 URL 길이|8,000||
| 최대 파일 업로드 크기 표준 |2GB | |
| 최대 파일 업로드 크기 WAF |100MB 중간 WAF 게이트웨이<br>500MB 큰 WAF 게이트웨이| |
| 파일이 없으면 WAF 본문 크기 제한|128KB||

<sup>1</sup> WAF가 활성화 된 Sku의 경우 최적의 성능을 위해 40 리소스의 수를 제한 하는 권장 합니다.
