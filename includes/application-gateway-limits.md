---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 92ec7c0a1469c9f02855cd6191faa8514e54c8f0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829210"
---
| 리소스 | 제한 | 참고 |
| --- | --- | --- |
| Azure Application Gateway |구독당 1000 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 프라이빗 1 |
| 프런트 엔드 포트 |100<sup>1</sup> | |
| 백 엔드 주소 풀 |100<sup>1</sup> | |
| 풀당 백 엔드 서버 |1,200 | |
| HTTP 수신기 |200<sup>1</sup> |트래픽을 라우팅하는 100개의 활성 수신기로 제한됩니다. 활성 수신기 수 = 총 수신기 수 - 비활성 수신기 수입니다.<br>라우팅 규칙 내의 기본 구성이 트래픽을 라우팅하도록 설정된 경우(예: 수신기, 백 엔드 풀 및 HTTP 설정을 사용하는 경우) 이러한 설정은 수신기로도 간주됩니다.|
| HTTP 부하 분산 규칙 |100<sup>1</sup> | |
| 백 엔드 HTTP 설정 |100<sup>1</sup> | |
| 게이트웨이당 인스턴스 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 인증서 |100<sup>1</sup> |HTTP 수신기당 1 |
| 최대 SSL 인증서 크기 |V1 SKU - 10KB<br>V2 SKU - 16KB| |
| 인증 인증서 |100 | |
| 신뢰할 수 있는 루트 인증서 |100 | |
| 최소 요청 시간 제한 |1초 | |
| 최대 요청 시간 제한 |24시간 | |
| 사이트 수 |100<sup>1</sup> |HTTP 수신기당 1 |
| 수신기당 URL 맵 |1 | |
| URL 맵당 최대 경로 기반 규칙|100||
| 구성 리디렉션 |100<sup>1</sup>| |
| 동시 WebSocket 연결 |중간 게이트웨이 20,000<br> 대형 게이트웨이 50,000| |
| 최대 URL 길이|32KB| |
| HTTP/2에 대한 최대 헤더 크기 |4KB| |
| 최대 파일 업로드 크기, 표준 |2GB | |
| 최대 파일 업로드 크기 WAF |V1 중간 WAF 게이트웨이, 100MB<br>V1 대형 WAF 게이트웨이, 500MB<br>V2 WAF, 750MB| |
| WAF 본문 크기 제한(파일 제외)|128KB||
| 최대 WAF 사용자 지정 규칙 수|100||
| 최대 WAF 제외 수|100||

<sup>1</sup> WAF 기반 SKU의 경우 리소스 수를 40개로 제한해야 합니다.
