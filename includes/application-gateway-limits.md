---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689112"
---
| Resource | 기본/최대 제한 | 참고 |
| --- | --- | --- |
| Azure Application Gateway |구독 당 1,000 개 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 프라이빗 1 |
| 프런트 엔드 포트 |100<sup>1</sup> | |
| 백 엔드 주소 풀 |100<sup>1</sup> | |
| 풀당 백 엔드 서버 |1,200 | |
| HTTP 수신기 |100<sup>1</sup> | |
| HTTP 부하 분산 규칙 |100<sup>1</sup> | |
| 백 엔드 HTTP 설정 |100<sup>1</sup> | |
| 게이트웨이당 인스턴스 |32 | |
| SSL 인증서 |100<sup>1</sup> |HTTP 수신기 당 1 |
| SSL 인증서 최대값 |V1 SKU-10KB<br>V2 SKU-25KB| |
| 인증 인증서 |100 | |
| 신뢰할 수 있는 루트 인증서 |100 | |
| 최소 요청 시간 초과 |1 초 | |
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
|최대 WAF 사용자 지정 규칙|100||

<sup>1</sup> WAF가 활성화 된 Sku의 경우 최적의 성능을 위해 40 리소스의 수를 제한 하는 권장 합니다.
