---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129688"
---
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 일부 구성 요소는 스왑(슬롯별이 아님)에서 콘텐츠를 따르지만 다른 구성 요소는 스왑 후 동일한 슬롯에 유지됩니다(슬롯별). 다음 목록은 슬롯을 교환할 때 변경되는 설정을 보여줍니다.

**교환된 설정**:

* 프레임워크 버전, 32/64비트, 웹 소켓과 같은 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 공용 인증서
* WebJob 콘텐츠
* 하이브리드 연결 *
* 가상 네트워크 통합 *
* 서비스 끝점 *
* Azure 콘텐츠 전송 네트워크 *

별표(*)로 표시된 피쳐는 스왑해제할 계획입니다. 

**교환되지 않은 설정**:

* 게시 엔드포인트
* 사용자 지정 도메인 이름
* 비공개 인증서 및 TLS/SSL 설정
* 크기 조정 설정
* WebJob 스케줄러
* IP 제한
* Always On
* 진단 로그 설정
* 원본 간 리소스 공유(CORS)

> [!NOTE]
> 스왑되지 않은 설정에 적용되는 특정 앱 설정도 교환되지 않습니다. 예를 들어 진단 로그 설정이 교환되지 않으므로 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` 관련 앱 설정이 슬롯 설정으로 표시되지 않더라도 같은 관련 앱 설정도 교환되지 않습니다.
>
