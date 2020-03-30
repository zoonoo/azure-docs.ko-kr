---
title: 개요 - 클라우드 기반 앱 및 솔루션용 Azure 서버리스
description: Azure Logic Apps 및 Azure 함수를 사용하여 인프라에 대한 걱정 없이 클라우드 기반 앱 및 솔루션을 만드는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666553"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 서버리스: Azure 논리 앱 및 Azure 함수를 사용하여 클라우드 기반 앱 및 솔루션 빌드에 대한 개요

[서버리스](https://azure.microsoft.com/solutions/serverless/) 앱은 개발 속도 향상, 코드 감소, 단순성 및 확장과 같은 이점을 제공합니다. 이 문서에서는 서버없는 솔루션 및 Azure 서버리스 오퍼링의 다양한 특성을 다룹니다.

## <a name="what-is-serverless"></a>서버를 사용하지 않음은 무엇입니까?

서버리스는 서버가 없다는 것을 의미하지 는 않지만 개발자는 서버에 대해 걱정할 필요가 없습니다. 기존 애플리케이션 개발의 많은 부분에서 애플리케이션의 요구에 맞는 솔루션의 크기 조정, 호스팅 및 모니터링에 대한 질문에 대답합니다. 서버가 없는 경우 이러한 질문은 솔루션의 일부로 처리됩니다. 또한 서버리스 앱은 소비 기반 요금제에 따라 요금이 청구됩니다. 앱을 사용하지 않으면 요금이 부과되지 않습니다. 이러한 기능을 통해 개발자는 솔루션의 비즈니스 논리에만 집중할 수 있습니다.

서버리스의 핵심 Azure 서비스는 [Azure 논리 앱](https://azure.microsoft.com/services/logic-apps/) 및 Azure [함수입니다.](https://azure.microsoft.com/services/functions/) 두 솔루션 모두 앞서 설명한 원칙을 따르고 개발자가 최소한의 코드로 강력한 클라우드 앱을 빌드하는 데 도움이 됩니다.

## <a name="what-is-azure-logic-apps"></a>Azure Logic Apps란?

[Azure Logic Apps는](logic-apps-overview.md) 클라우드에서 확장 가능한 통합 및 워크플로를 단순화하고 구현하는 방법을 제공합니다. 이 서비스는 워크플로우라는 일련의 단계로 프로세스를 모델링하고 자동화하는 시각적 디자이너를 제공합니다. 클라우드 서비스 및 온-프레미스 시스템에 걸쳐 서버리스 앱을 다른 API에 빠르게 연결하는 많은 [커넥터가](../connectors/apis-list.md) 있습니다. 모든 논리 앱은 "계정이 Dynamics CRM에 추가되는 경우"와 같은 트리거로 시작합니다. 트리거가 실행된 후 워크플로는 작업, 변환 및 조건부 논리의 조합을 실행할 수 있습니다. 논리 앱은 프로세스에서 다른 Azure Functions를 오케스트레이션할 때 특히 프로세스가 외부 시스템 또는 API와 상호 작용해야 하는 경우에 적합합니다.

Logic Apps를 시작하려면 [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)로 시작합니다. 논리 앱에 대한 자세한 내용은 [개발자 참조를](logic-apps-workflow-definition-language.md)참조하십시오.

## <a name="what-is-azure-functions"></a>Azure Functions란?

Azure Functions는 클라우드에서 코드 또는 "함수"를 쉽게 실행할 수 있는 서비스입니다. 전체 앱이나 필요한 인프라에 대해 걱정하지 않고 현재 문제에 필요한 코드만 작성할 수 있습니다. Functions는 개발 생산성을 높일 수 있으며 C#, F#, Node.js, Python, PHP 등의 원하는 개발 언어를 사용할 수 있습니다. 필요에 따라 코드가 실행되고 Azure 의 확장이 조정되는 시간에 대해서만 비용을 지불합니다.

Azure 함수를 시작하려면 첫 [번째 Azure 함수 만들기로](../azure-functions/functions-create-first-azure-function.md)시작합니다. 함수에 대한 자세한 내용은 [개발자 참조를](../azure-functions/functions-reference.md)참조하십시오.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Azure에서 서버리스 앱을 빌드하고 배포하는 방법은 무엇입니까?

Azure는 서버리스 앱을 개발, 배포 및 관리하기 위한 다양한 도구를 제공합니다. Visual Studio 또는 [Visual Studio 코드의](quickstart-create-logic-apps-visual-studio-code.md)도구를 사용하여 Azure [포털에서](logic-apps-serverless-get-started-vs.md)직접 앱을 빌드할 수 있습니다. 앱을 빌드한 후 [Azure 리소스 관리자 템플릿을 사용 하](logic-apps-deploy-azure-resource-manager-templates.md)여 해당 앱을 빠르게 배포할 수 있습니다. 또한 Azure는 Azure 포털을 통해, API 또는 SDK를 통해 또는 Azure 모니터 로그 및 응용 프로그램 인사이트를 위한 통합 툴링을 통해 액세스할 수 있는 모니터링을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [비주얼 스튜디오에서 서버리스 앱 빌드](logic-apps-serverless-get-started-vs.md)
* [서버리스로 고객 인사이트 대시보드 생성](logic-apps-scenario-social-serverless.md)
* [논리 앱 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)
