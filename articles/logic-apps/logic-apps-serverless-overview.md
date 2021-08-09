---
title: 개요 - 클라우드 기반 앱과 솔루션에 대한 Azure 서버리스
description: Azure Logic Apps와 Azure Functions를 사용하여 인프라에 대한 걱정 없이 클라우드 기반 앱 및 솔루션을 만드는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788098"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 서버리스: Azure Logic Apps와 Azure Functions를 사용하여 클라우드 기반 앱과 솔루션을 빌드하는 것에 대한 개요

[서버리스](https://azure.microsoft.com/solutions/serverless/) 앱은 개발 속도 향상, 코드 감소, 단순성, 스케일링과 같은 이점을 제공합니다. 이 문서에서는 서버리스 솔루션과 Azure 서버리스 제품의 다양한 특성을 다룹니다.

## <a name="what-is-serverless"></a>서버를 사용하지 않음은 무엇입니까?

서버리스는 서버가 없다는 것을 의미하지는 않지만, 개발자가 서버에 대해 걱정하지 않아도 됩니다. 기존 애플리케이션 개발의 많은 부분에서 애플리케이션의 요구에 맞는 솔루션의 크기 조정, 호스팅 및 모니터링에 대한 질문에 대답합니다. 서버리스 방식을 사용하면 솔루션을 통해 이 질문이 알아서 해결됩니다. 또한 서버리스 앱 사용량 기반 계획에 대해 요금이 청구됩니다. 앱이 사용되지 않으면 요금이 발생하지 않습니다. 서버리스 기능을 통해 개발자는 솔루션의 비즈니스 논리에만 집중할 수 있습니다.

Azure의 핵심 서버리스 서비스는 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)과 [Azure Functions](https://azure.microsoft.com/services/functions/)입니다. 두 솔루션은 앞에서 설명한 원칙을 따르고 개발자가 최소한의 코드로 강력한 클라우드 앱을 빌드할 수 있도록 지원합니다.

## <a name="what-is-azure-logic-apps"></a>Azure Logic Apps란?

[Azure Logic Apps](logic-apps-overview.md)는 클라우드에서 스케일링 가능한 통합 및 워크플로를 단순화하고 구현하는 방법을 제공합니다. 이 서비스는 모델에 비주얼 디자이너를 제공하고 프로세스를 워크플로라는 일련의 단계로 자동화합니다. 클라우드 서비스와 온-프레미스 시스템에는 서버리스 앱을 다른 API에 신속하게 연결하는 많은 [커넥터](../connectors/apis-list.md)가 있습니다. 모든 논리 앱은 “Dynamics CRM에 계정이 추가되는 경우”와 같은 트리거로 시작합니다. 트리거가 발생한 후 워크플로는 작업, 변환, 조건부 논리의 조합을 실행할 수 있습니다. Logic Apps는 프로세스에서 다른 Azure Functions를 오케스트레이션하는 경우(특히 프로세스가 외부 시스템 또는 API와 상호 작용해야 하는 경우) 탁월한 선택입니다.

Logic Apps를 시작하려면 [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)로 시작합니다. Logic Apps에 대한 자세한 기술 정보는 [개발자 참조](logic-apps-workflow-definition-language.md)를 참조하세요.

## <a name="what-is-azure-functions"></a>Azure Functions란?

Azure Functions는 클라우드에서 작은 코드 또는 “함수”를 실행하기 위한 서비스입니다. 전체 앱 또는 필요한 인프라에 대해 걱정하지 않고 현재 문제에 필요한 코드만 작성할 수 있습니다. Functions는 개발 생산성을 높일 수 있으며 C#, F#, Node.js, Python, PHP 등의 원하는 개발 언어를 사용할 수 있습니다. 코드가 실행되는 시간에 대해서만 비용을 지불하고 필요에 따라 Azure를 스케일링합니다.

Azure Functions를 시작하려면 [첫 번째 Azure 함수 만들기](../azure-functions/functions-get-started.md)를 시작합니다. Functions에 대한 자세한 기술 정보는 [개발자 참조](../azure-functions/functions-reference.md)를 참조하세요.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Azure에서 서버리스 앱을 빌드하고 배포하려면 어떻게 해야 하나요?

Azure는 서버리스 앱을 개발, 배포, 관리하기 위한 다양한 도구를 제공합니다. [Visual Studio 도구](logic-apps-serverless-get-started-vs.md) 또는 [Visual Studio Code 도구](quickstart-create-logic-apps-visual-studio-code.md)를 사용하여 Azure Portal에 직접 앱을 빌드할 수 있습니다. 앱을 빌드한 후 [Azure Resource Manager 템플릿을 통해 해당 앱을 신속하게 배포](logic-apps-deploy-azure-resource-manager-templates.md)할 수 있습니다. 또한 Azure는 Azure Portal, API 또는 SDK를 통해 액세스하거나 Azure Monitor 로그와 Application Insights에 대한 통합 도구를 통해 액세스할 수 있는 모니터링을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 서버리스 앱 빌드](logic-apps-serverless-get-started-vs.md)
* [서버리스 Customer Insights 대시보드 만들기](logic-apps-scenario-social-serverless.md)
* [논리 앱 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)