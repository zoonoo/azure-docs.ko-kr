---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334925"
---
| 리소스 | 제한 | 참고 |
| --- | --- | --- |
| Azure Application Gateway |구독당 1,000개 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 프라이빗 1 |
| 프런트 엔드 포트 |100<sup>1</sup> | |
| 백 엔드 주소 풀 |100<sup>1</sup> | |
| 풀당 백 엔드 서버 |1,200 | |
| HTTP 수신기 |200<sup>1</sup> |트래픽을 라우팅하는 활성 수신기 100개로 제한됩니다. 활성 수신기 = 총 청취자 수 - 활성 되지 않은 수신기입니다.<br>라우팅 규칙 내의 기본 구성이 트래픽을 라우팅하도록 설정된 경우(예: 수신기, 백 엔드 풀 및 HTTP 설정이 있음) 수신기로도 계산됩니다.|
| HTTP 로드 밸런싱 규칙 |100<sup>1</sup> | |
| 백 엔드 HTTP 설정 |100<sup>1</sup> | |
| 게이트웨이당 인스턴스 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 인증서 |100<sup>1</sup> |HTTP 수신기당 1개 |
| 최대 SSL 인증서 크기 |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| 인증 인증서 |100 | |
| 신뢰할 수 있는 루트 인증서 |100 | |
| 요청 시간 최소 |1초 | |
| 요청 시간 시간 최대 |24시간 | |
| 사이트 수 |100<sup>1</sup> |HTTP 수신기당 1개 |
| 수신기당 URL 맵 |1 | |
| URL 맵당 최대 경로 기반 규칙|100||
| 구성 리디렉션 |100<sup>1</sup>| |
| 동시 WebSocket 연결 |중간 게이트웨이 20k<br> 대형 게이트웨이 50k| |
| 최대 URL 길이|32kb| |
| HTTP/2의 최대 헤더 크기 |4KB| |
| 최대 파일 업로드 크기, 표준 |2GB | |
| 최대 파일 업로드 크기 WAF |V1 중간 WAF 게이트웨이, 100MB<br>V1 대형 WAF 게이트웨이, 500MB<br>V2 WAF, 750 MB| |
| 파일 없이 WAF 바디 크기 제한|128KB||
| 최대 WAF 사용자 지정 규칙|100||
| 최대 WAF 제외|100||

<sup>1</sup> WAF 지원 SCO의 경우 최적의 성능을 위해 리소스 수를 40개로 제한하는 것이 좋습니다.
