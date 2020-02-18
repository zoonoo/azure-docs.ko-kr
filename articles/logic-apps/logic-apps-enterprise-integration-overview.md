---
title: B2B 엔터프라이즈 통합
description: Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 엔터프라이즈 통합용 자동화 B2B 워크플로를 빌드하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191363"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩이 포함된 B2B 엔터프라이즈 통합 솔루션

B2B(기업 간) 솔루션 및 조직 간의 원활한 통신을 위해 EIP(엔터프라이즈 통합 팩)를 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)와 함께 사용하여 자동화된 확장형 엔터프라이즈 통합 워크플로를 빌드할 수 있습니다. 조직에서 서로 다른 프로토콜 및 형식을 사용하더라도 전자 방식으로 메시지를 교환할 수 있습니다. EIP는 여러 형식을 조직의 시스템에서 처리할 수 있는 형식으로 변환하며, [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) 및 [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)를 비롯한 업계 표준 프로토콜을 지원합니다. 암호화와 디지털 서명을 모두 사용하여 메시지의 보안을 향상시킬 수도 있습니다. EIP는 이러한 [엔터프라이즈 통합 커넥터](../connectors/apis-list.md#integration-account-connectors) 및 산업 표준을 지원합니다.

* EDI(전자 데이터 교환)
* EAI(Enterprise Application Integration)

Microsoft BizTalk Server 또는 Azure BizTalk Services에 대해 잘 알고 있는 분들은 그와 개념이 비슷한 EIP의 기능을 쉽게 사용할 수 있습니다. 하지만 EIP는 아키텍처 측면에서 "통합 계정"을 기반으로 하므로 B2B 통신에 사용되는 아티팩트의 저장 및 관리가 간편하다는 결정적인 차이가 있습니다. 이러한 계정은 파트너, 계약, 스키마, 맵, 인증서 등의 모든 아티팩트를 저장하는 클라우드 기반 컨테이너입니다. 

## <a name="why-use-the-enterprise-integration-pack"></a>엔터프라이즈 통합 팩을 사용하는 이유

* EIP를 사용하면 모든 아티팩트를 한 장소, 즉, 통합 계정에 저장할 수 있습니다.

* Azure Logic Apps 및 커넥터를 사용하여 B2B 워크플로를 빌드하고 타사 SaaS(Software-as-Service) 앱, 온-프레미스 앱 및 사용자 지정 앱과 통합할 수 있습니다.

* Azure Functions를 사용하여 Logic Apps에 대한 사용자 지정 코드를 만들 수 있습니다.

## <a name="how-do-i-get-started"></a>어떻게 시작하나요?

EIP를 사용하여 B2B 논리 앱 워크플로를 빌드하려면 다음 항목이 필요합니다.

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 사용하려는 아티팩트가 포함된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 맵과 스키마를 만들려면 [Visual Studio용 Microsoft Azure Logic Apps 엔터프라이즈 통합 도구 2015 2.0](https://aka.ms/vsmapsandschemas) 및 Visual Studio 2015를 사용하면 됩니다.

통합 계정을 만들고 아티팩트를 추가한 후에는 Azure Portal에서 논리 앱을 만들어 이러한 아티팩트로 B2B 워크플로를 빌드할 수 있습니다. 논리 앱을 처음 접하는 경우 [기본 논리 앱을 만들어 보세요](../logic-apps/quickstart-create-first-logic-app-workflow.md). 이러한 아티팩트를 사용하려면 먼저 통합 계정을 논리 앱에 연결해야 합니다. 그 후 논리 앱에서 통합 계정에 액세스할 수 있습니다. Visual Studio 또는 [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)을 사용하여 논리 앱을 만들고, 관리하고, 배포할 수 있습니다.

B2B 논리 앱 빌드를 시작하기 위한 개략적인 단계는 다음과 같습니다.

![B2B 논리 앱을 만들기 위한 필수 구성 요소](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>지금 사용해보기

[AS2 메시지를 보내고 받는 완전히 작동하는 샘플 논리 앱 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>다음 단계

* [거래 업체 만들기](logic-apps-enterprise-integration-partners.md)
* [규약 만들기](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [스키마 추가](logic-apps-enterprise-integration-schemas.md)
* [맵 추가](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [BizTalk Services에서 마이그레이션](../logic-apps/logic-apps-move-from-mabs.md)
