---
title: "가격 책정 및 대금 청구 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps의 가격 책정 및 대금 청구 방식에 대해 알아봅니다."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: f2a92e45b8a759d2d8193ac188efdcfc694a3e6d
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2017
---
# <a name="logic-apps-pricing-model"></a>Logic Apps 가격 책정 모델
Azure Logic Apps를 사용하여 클라우드에 자동화된 확장 가능한 통합 워크플로를 만들고 실행할 수 있습니다. 다음은 Logic Apps의 청구 및 가격 책정 방식에 대한 세부 정보입니다.
## <a name="consumption-pricing-model"></a>소비 가격 책정 모델
새로 만든 논리 앱을 사용하면 사용한 만큼에 대해서만 비용을 지불합니다. 새 논리 앱은 소비 계획 및 가격 책정 모델을 사용합니다. 즉, 논리 앱 인스턴스에 의해 수행되는 모든 실행이 계량됩니다.
### <a name="what-are-action-executions"></a>작업 실행이란 무엇인가요?
논리 앱 정의의 모든 단계는 작업이며 여기에는 트리거, 제어 흐름 단계, 기본 제공 작업에 대한 호출 및 커넥터에 대한 호출이 포함됩니다.
### <a name="triggers"></a>트리거
트리거는 특정 이벤트가 발생할 때 논리 앱 인스턴스를 만드는 특수 작업입니다. 트리거에는 논리 앱이 계량되는 방식에 영향을 주는 몇 가지 다른 동작이 있습니다.
* **폴링 트리거** – 이 트리거는 워크플로를 시작하기 위해 논리 앱 인스턴스를 만드는 조건을 충족하는 메시지를 수신할 때까지 끝점을 지속적으로 폴링합니다. Logic App Designer를 통해 트리거에서 폴링 간격을 설정할 수 있습니다. 논리 앱 인스턴스를 만들지 않은 경우에도 각 폴링 요청은 실행으로 간주됩니다.
* **웹후크 트리거** – 이 트리거는 클라이언트가 특정 끝점에 요청을 보낼 때까지 대기합니다. 웹후크 끝점으로 전송된 각 요청은 작업 실행으로 계산됩니다. 예를 들어, 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.
* **되풀이 트리거** – 이 트리거는 트리거에 구성한 되풀이 간격을 기반으로 논리 앱 인스턴스를 만듭니다. 예를 들어 3일마다 또는 더 복잡한 일정으로 실행되는 되풀이 트리거를 설정할 수 있습니다.

트리거 실행은 트리거 기록 섹션 아래 논리 앱의 개요 창에서 찾을 수 있습니다.

### <a name="actions"></a>작업
기본 제공 작업(예: HTTP, Azure Functions 또는 API Management는 물론 제어 흐름 단계)은 원시 작업으로 계량되며 해당 유형이 있습니다. [커넥터](https://docs.microsoft.com/connectors)를 호출하는 작업에는 "ApiConnection" 유형이 있습니다. 커넥터는 표준 또는 엔터프라이즈 커넥터로 분류되며 해당 [가격][pricing]으로 계량됩니다.
성공 및 실패한 모든 실행 작업은 작업 실행으로 집계되고 계량됩니다. 조건이 충족되지 않아 건너뛴 작업 또는 완료되기 전에 논리 앱이 종료되어 실행되지 않은 작업은 작업 실행으로 간주되지 않습니다. 사용이 중지된 논리 앱은 새 인스턴스를 인스턴스화할 수 없으므로 사용이 중지되는 동안 비용이 청구되지 않습니다.

> [!NOTE]
> 논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

루프 내에서 실행되는 작업은 루프의 각 주기당 계산됩니다. 예를 들어, 항목이 10개인 목록을 처리하는 "for each" 루프의 단일 작업은 목록 항목 수(10)에 루프의 작업 수(1)를 곱하고 루프 시작에 대한 1을 더하여 계산됩니다. 이 예제의 경우 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다.

### <a name="integration-account-usage"></a>통합 계정 사용량
소비 기반 사용량에는 추가 비용 없이 Logic Apps의 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 탐색, 개발 및 테스트할 수 있는 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 포함됩니다. 지역당 이러한 통합 계정 중 하나를 가질 수 있고 최대 10개의 계약과 25개의 맵을 저장할 수 있습니다. 파트너, 스키마 및 인증서를 무제한 가질 수 있고 업로드할 수 있습니다.

또한 Logic Apps에는 지원되는 Logic Apps SLA와 함께 기본 및 표준 통합 계정이 제공됩니다. 메시지 처리만 사용하려는 경우 또는 대규모 비즈니스 엔터티와 거래 파트너 관계가 있는 소규모 비즈니스 파트너 역할을 수행하려는 경우에는 기본 통합 계정을 사용할 수 있습니다. 표준 통합 계정은 더 복잡한 B2B 관계를 지원하며 관리할 수 있는 엔터티의 수를 늘립니다. 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요.

## <a name="pricing"></a>가격
자세한 내용은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Logic Apps 개요][whatis]
* [첫 번째 논리 앱 만들기][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

