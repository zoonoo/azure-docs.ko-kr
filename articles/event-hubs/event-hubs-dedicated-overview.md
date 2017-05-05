---
title: "Azure Event Hubs Dedicated 용량 개요 | Microsoft Docs"
description: "Azure Event Hubs Dedicated 용량에 대한 개요입니다."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: sethm;babanisa
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: fa0d49498db74ec81c98f3df9317d42cdd31ab46
ms.lasthandoff: 04/18/2017


---

# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*Event Hubs Dedicated* 용량은 요구 사항이 가장 까다로운 고객을 위해 단일 테넌트 배포를 제공합니다. 전체 규모 Azure Event Hubs는 초당 2백만 개 이상의 이벤트 또는 초당 최대 2GB의 원격 분석을 수신할 수 있으며 완벽한 내구성의 저장소와 1초 미만의 대기 시간을 제공합니다. 또한 동일한 시스템에서 실시간 및 배치를 처리하여 통합 솔루션을 사용할 수 있습니다. 제품에 Event Hubs 보관 파일이 포함되어 있기 때문에 단일 스트림으로 실시간 및 배치 기반 파이프라인을 모두 지원하여 솔루션의 복잡성을 줄일 수 있습니다.

다음은 Event Hubs에 제공되는 서비스 계층을 비교한 표입니다. Standard 및 Basic은 대부분의 기능이 사용량에 따라 요금이 부과되는 반면 Event Hubs Dedicated 제품은 혼합 방식의 월 요금제가 적용됩니다. Dedicated 계층은 표준 요금제의 기능을 제공합니다. 하지만 워크로드가 까다로운 고객을 위해 엔터프라이즈급 용량을 제공합니다. 

| 기능 | Basic | Standard | 전용 |
| --- |:---:|:---:|:---:|
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 100만 이벤트당 요금 부과 | 포함됨 |
| 처리량 단위(1MB/초 수신, 2MB/초 송신) | 시간당 요금 부과 | 시간당 요금 부과 | 포함됨 |
| 메시지 크기 | 256 KB | 256 KB | 1MB |
| 게시자 정책 | 해당 없음 | 예 | 예 |     
| 소비자 그룹 | 1 - 기본값 | 20 | 20 |
| 메시지 재생 | 예 | 예 | 예 |
| 최대 처리량 단위 | 20 | 20(100까지 유연하게 구성 가능)    | 1 CU≈200 |
| 조정된 연결 | 100개 포함 | 1,000개 포함 | 100,000개 포함 |
| 추가 조정된 연결 | 해당 없음 | 예 | 예 |
| 메시지 보존 | 1일 포함 | 1일 포함 | 최대 7일 포함 |
| 보관(미리 보기) | 해당 없음    | 시간당 요금 부과 | 포함됨 |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs Dedicated 용량의 이점

Event Hubs Dedicated를 사용하는 경우 다음과 같은 이점이 제공됩니다.

* 다른 테넌트의 노이즈가 없는 단일 테넌트 호스팅
* 메시지 크기가 256KB인 Standard 및 Basic에 비해 1MB로 증가
* 매번 반복 가능한 성능
* 폭발적인 요구를 충족하는 보장된 용량
* 1-8 CU(용량 단위) 사이에서 확장 가능 - 초당 최대 2백만 개 이벤트 수신
  * CU는 Event Hubs Dedicated의 규모를 관리하며 각 CU는 TU(처리량 단위) 약 200개에 해당하는 용량 제공 가능
* 유지 관리 시간 없음: 부하 분산, 운영 체제 업데이트, 보안 패치 및 분할을 Microsoft에서 관리
* 고정 월 요금

또한 Event Hubs Dedicated는 Standard 제품의 처리량 제한 중 일부를 제거합니다. Basic 및 Standard 계층의 처리량 단위는 초당 1000개 이벤트 또는 TU당 1MBps를 수신하고 그 두 배를 송신할 수 있습니다. Dedicated 제품은 수신 및 송신 이벤트 수에 제한이 없습니다. 이러한 제한은 구입한 이벤트 허브의 처리 용량에 따라서만 적용됩니다.

이 서비스는 가장 규모가 큰 원격 분석 사용자를 대상으로 하며 기업계약을 맺은 고객에게 제공됩니다.

## <a name="how-to-onboard"></a>등록 방법

Event Hubs Dedicated 플랫폼은 기업 계약을 통해 다양한 크기의 CU로 대중에게 제공됩니다. 각 CU는 대략 처리량 단위 200개와 동일한 처리량을 제공합니다. 언제든지 요구 사항에 맞게 CU를 추가하거나 제거하여 용량을 늘리거나 줄일 수 있습니다. Dedicated 요금제는 Event Hubs 제품 팀의 실습 온보딩을 통해 고객에게 적합하도록 유연한 배포가 가능하다는 고유의 장점이 있습니다. 

## <a name="next-steps"></a>다음 단계
Event Hubs Dedicated 용량에 대한 추가 정보를 얻으려면 Microsoft 영업 담당자 또는 Microsoft 지원에 문의하세요. 다음 링크에서도 Event Hubs에 대한 자세한 내용을 확인할 수 있습니다.

가격 책정에 대한 자세한 내용은 다음 링크를 방문하세요.

- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다. 

