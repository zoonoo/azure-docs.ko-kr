---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82131595"
---
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 일부 구성 요소는 교환 (슬롯 특정)에서 콘텐츠를 따르고, 다른 구성 요소는 교환 (슬롯 특정) 후에 동일한 슬롯에 유지 됩니다. 다음 목록은 슬롯을 교환할 때 변경되는 설정을 보여줍니다.

**교환된 설정**:

* 프레임 워크 버전, 32/64 비트, 웹 소켓 등의 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 공용 인증서
* WebJob 콘텐츠
* 하이브리드 연결 *
* 가상 네트워크 통합 *
* 서비스 끝점 *
* Azure Content Delivery Network *

별표 (*)로 표시 된 기능을 교환 하지 않도록 계획 합니다. 

**교환되지 않은 설정**:

* 게시 엔드포인트
* 사용자 지정 도메인 이름
* 공용이 아닌 인증서 및 TLS/SSL 설정
* 크기 조정 설정
* WebJob 스케줄러
* IP 제한
* Always On
* 진단 설정
* CORS(원본 간 리소스 공유)

> [!NOTE]
> 또한 스왑 되지 않은 설정에 적용 되는 특정 앱 설정은 교환 되지 않습니다. 예를 들어 진단 설정이 교환 되지 않으므로 및와 같은 관련 앱 설정은 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` 슬롯 설정으로 표시 되지 않는 경우에도 교환 되지 않습니다.
>
