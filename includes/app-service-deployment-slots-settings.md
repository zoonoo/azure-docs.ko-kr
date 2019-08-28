---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623692"
---
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 일부 구성 요소는 교환 (슬롯 특정)에서 콘텐츠를 따르고, 다른 구성 요소는 교환 (슬롯 특정) 후에 동일한 슬롯에 유지 됩니다. 다음 목록은 슬롯을 교환할 때 변경되는 설정을 보여줍니다.

**교환된 설정**:

* 프레임 워크 버전, 32/64 비트, 웹 소켓 등의 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 모니터링 및 진단 설정
* 공용 인증서
* WebJob 콘텐츠
* 하이브리드 연결 *
* 가상 네트워크 통합 *
* 서비스 끝점 *
* Azure Content Delivery Network *

별표 (*)로 표시 된 기능은 슬롯에 고정 되도록 예정 되어 있습니다. 

**교환되지 않은 설정**:

* 게시 끝점
* 사용자 지정 도메인 이름
* 프라이빗 인증서 및 SSL 바인딩
* 크기 조정 설정
* WebJob 스케줄러
* IP 제한
* 항상 설정됨
* 프로토콜 설정 (HTTPS, TLS 버전, 클라이언트 인증서)
* 진단 로그 설정
* CORS (원본 간 리소스 공유)

<!-- VNET and hybrid connections not yet sticky to slot -->