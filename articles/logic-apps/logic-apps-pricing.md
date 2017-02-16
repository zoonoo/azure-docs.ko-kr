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
ms.date: 01/23/2017
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 11aa3f74d112244cd96278c2b2e3d701e031aee8
ms.openlocfilehash: 55756029ebba3f3467828db5567a6616f7eeba2b

---
# <a name="logic-apps-pricing-model"></a>논리 앱 가격 책정 모델
Azure Logic Apps을 사용하면 클라우드에서 통합 워크플로의 크기를 조정하고 실행할 수 있습니다.  Logic Apps의 청구 및 가격 책정은 다음과 같습니다.
## <a name="consumption-pricing"></a>소비 가격
새로 만든 논리 앱에서는 소비 요금제를 사용합니다. 논리 앱 소비 가격 책정 모델로 사용한 양만큼만 요금을 지불합니다.  소비 요금제를 사용할 경우 논리 앱은 제한되지 않습니다.
논리 앱 인스턴스 실행 시 실행되는 모든 작업은 요금이 청구됩니다.
### <a name="what-are-action-executions"></a>작업 실행이란 무엇인가요?
논리 앱 정의의 모든 단계는 트리거, 조건과 같은 제어 흐름 단계, 범위, for each 루프, do until 루프, 커넥터와 네이티브 작업에 대한 호출을 포함하는 작업입니다
트리거는 특정 이벤트가 발생할 때 논리 앱의 새 인스턴스를 인스턴스화하기 위해 설계된 특수한 작업입니다.  논리 앱을 측정하는 방식에 영향을 줄 수 있는 트리거의 다양한 동작이 있습니다.
* **폴링 트리거** – 이 트리거는 논리 앱의 인스턴스를 만들기 위한 기준을 만족하는 메시지를 수신할 때까지 끝점을 지속적으로 폴링합니다.  논리 앱 디자이너의 트리거에서 폴링 간격을 구성할 수 있습니다.  각 폴링 요청은 논리 앱의 인스턴스를 만들지 않더라도 작업 실행으로 계산됩니다.
* **웹후크 트리거** – 이 트리거는 클라이언트가 특정 끝점에서 요청을 보낼 때까지 대기합니다.  웹후크 끝점으로 전송된 각 요청은 작업 실행으로 계산됩니다. 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.
* **되풀이 트리거** – 이 트리거는 트리거에 구성된 되풀이 간격을 기반으로 논리 앱의 인스턴스를 만듭니다.  예를 들어&3;일 또는&1;분마다 실행하도록 되풀이 트리거를 구성할 수 있습니다.
트리거 기록 부분의 논리 앱 리소스 블레이드에서 트리거 실행을 볼 수 있습니다.
실행된 모든 작업은 성공 여부에 관계없이 작업 실행으로 측정됩니다.  조건이 충족되지 않아 건너뛴 작업 또는 논리 앱에서 완료하기 전에 종료되어 실행되지 않은 작업은 작업 실행으로 계산되지 않습니다.

루프 내에서 실행된 작업은 루프의 반복당 계산됩니다.  예를 들어 10개 항목 목록을 반복하는 for each 루프의 단일 작업은 목록에 있는 항목 수(10)에 루프에 있는 작업 수(1)를 곱한 값에 루프의 시작인 경우 1을 더하여 계산합니다. 이 예에서는 (10 * 1) + 1 = 11 작업 실행이 됩니다.
비활성화된 Logic Apps는 인스턴스화된 새 인스턴스를 포함할 수 없으므로 비활성화된 기간 중에는 비용이 청구되지 않습니다.  논리 앱을 비활성화한 후에는 논리 앱이 완전히 비활성화되기 전에 인스턴스가 정지되는 데 약간의 시간이 걸릴 수 있습니다.
### <a name="integration-account-usage"></a>통합 계정 사용량
소비 기반 사용량에는 추가 비용 없이 Logic Apps의 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 사용할 수 있도록 하는 탐색, 개발 및 테스트 목적의 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 포함되어 있습니다. 하위 지역당 최대 1개의 계정을 만들고 최대 10개의 규약과 25개의 지도를 저장할 수 있습니다. 스키마, 인증서 및 파트너에는 제한이 없고 필요한 만큼 업로드할 수 있습니다.

소비에 통합 계정을 포함하는 것 외에도, 표준 Logic Apps SLA를 사용하여 이러한 제한이 없는 표준 통합 계정을 만들 수도 있습니다. 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요.

## <a name="app-service-plans"></a>앱 서비스 계획
App Service 계획을 참조하여 이전에 만든 Logic Apps는 계속 이전과 같이 동작합니다. 선택한 계획에 따라, 정해진 일별 실행이 초과된 후에 조절되지만 작업 실행 미터기를 사용하여 요금이 청구됩니다.
구독에 논리 앱과 명시적으로 연결되지 않은 App Service 계획이 있는 EA 고객은 포함된 수량 이점을 얻을 수 있습니다.  예를 들어 EA 구독에 표준 App Service 계획이 있고 동일한 구독에 논리 앱이 있는 경우 일별로 10,000개 작업 실행에 대한 요금이 청구되지 않습니다(다음 표 참조). 

앱 서비스 계획 및 일일 허용된 작업 실행:
|  | 무료/공유/기본 | 표준 | Premium |
| --- | --- | --- | --- |
| 일일 작업 실행 |200 |10000 |50,000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>앱 서비스 계획 가격 책정에서 소비로 변환
연결된 App Service 계획이 있는 논리 앱을 소비 모델로 변경하려면 논리 앱 정의에서 App Service 계획에 대한 참조를 제거합니다.  이 작업은 PowerShell cmdlet `Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`를 호출하여 수행할 수 있습니다.
## <a name="pricing"></a>가격
가격 책정 세부 정보는 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Logic Apps 개요][whatis]
* [첫 번째 논리 앱 만들기][만들기] [가격 책정]: https://azure.microsoft.com/pricing/details/logic-apps/ [whatis]: logic-apps-what-are-logic-apps.md [만들기]: logic-apps-create-a-logic-app.md




<!--HONumber=Jan17_HO4-->


