---
title: 개요-Azure 서버 리스
description: 인프라에 대해 걱정 하지 않고 클라우드에서 강력한 솔루션 만들기
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: d658efd278425358a2a931fe976827c3bae4247d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792833"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>개요: Azure Logic Apps 및 Azure Functions를 사용 하 여 Azure 서버 리스

[서버](https://azure.microsoft.com/solutions/serverless/) 를 사용 하지 않는 앱은 개발 속도 향상, 코드 축소, 간소화 및 규모와 같은 이점을 제공 합니다. 이 문서에서는 서버를 사용 하지 않는 솔루션과 Azure 서버를 사용 하지 않는 제품의 다양 한 특성을 다룹니다.

## <a name="what-is-serverless"></a>서버를 사용하지 않음은 무엇입니까?

서버를 사용 하지 않는 경우 서버가 없다는 것을 의미 하지는 않지만 개발자가 서버에 대해 걱정 하지 않아도 됩니다. 기존 애플리케이션 개발의 많은 부분에서 애플리케이션의 요구에 맞는 솔루션의 크기 조정, 호스팅 및 모니터링에 대한 질문에 대답합니다. 서버를 사용 하지 않는 경우 이러한 질문은 솔루션의 일부로 처리 됩니다. 또한 서버 리스 앱은 사용량 기반 계획에서 청구 됩니다. 앱을 사용 하지 않는 경우 요금이 발생 하지 않습니다. 개발자는 이러한 기능을 통해 솔루션의 비즈니스 논리에만 집중할 수 있습니다.

서버를 사용 하지 않는 핵심 Azure 서비스는 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) [Azure Functions](https://azure.microsoft.com/services/functions/)됩니다. 두 솔루션은 앞서 설명한 원칙을 따르고 개발자가 최소한의 코드로 강력한 클라우드 앱을 빌드하는 데 도움을 줍니다.

## <a name="what-is-azure-logic-apps"></a>Azure Logic Apps란?

[Azure Logic Apps](logic-apps-overview.md) 는 클라우드에서 확장 가능한 통합 및 워크플로를 단순화 하 고 구현 하는 방법을 제공 합니다. 이 서비스는 워크플로 라는 일련의 단계로 프로세스를 모델링 하 고 자동화 하는 비주얼 디자이너를 제공 합니다. 서버를 사용 하지 않는 앱을 다른 Api에 빠르게 연결 하는 클라우드 서비스와 온-프레미스 시스템 간에 많은 [커넥터가](../connectors/apis-list.md) 있습니다. 모든 논리 앱은 "Dynamics CRM에 계정을 추가 하는 경우"와 같은 트리거로 시작 합니다. 트리거가 발생 한 후 워크플로는 작업, 변환 및 조건부 논리의 조합을 실행할 수 있습니다. 특히 프로세스가 외부 시스템 또는 API와 상호 작용 해야 하는 경우에는 프로세스에서 다른 Azure Functions 오케스트레이션 때 매우 유용 합니다. Logic Apps

Logic Apps를 시작하려면 [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)로 시작합니다. Logic Apps에 대 한 자세한 기술 정보는 [개발자 참조](logic-apps-workflow-definition-language.md)를 참조 하세요.

## <a name="what-is-azure-functions"></a>Azure Functions란?

Azure Functions는 클라우드에서 코드 또는 "함수"를 쉽게 실행 하기 위한 서비스입니다. 전체 앱 또는 필요한 인프라에 대해 걱정 하지 않고 현재 문제에 필요한 코드만 작성할 수 있습니다. Functions는 개발 생산성을 높일 수 있으며 C#, F#, Node.js, Python, PHP 등의 원하는 개발 언어를 사용할 수 있습니다. 코드가 실행 되는 시간에 대해서만 비용을 지불 하 고 필요에 따라 Azure를 확장 합니다.

Azure Functions를 시작 하려면 [첫 번째 Azure Function 만들기](../azure-functions/functions-create-first-azure-function.md)를 시작 합니다. 함수에 대 한 자세한 기술 정보는 [개발자 참조](../azure-functions/functions-reference.md)를 참조 하세요.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Azure에서 서버 리스 앱을 빌드 및 배포 하려면 어떻게 해야 하나요?

Azure는 서버 리스 앱을 개발, 배포 및 관리 하기 위한 다양 한 도구를 제공 합니다. [Visual Studio의 도구](logic-apps-serverless-get-started-vs.md)를 사용 하 여 Azure Portal에서 직접 또는 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)를 사용 하 여 앱을 빌드할 수 있습니다. 앱을 빌드한 후 [Azure Resource Manager 템플릿을 사용 하 여 해당 앱을 신속 하 게 배포할](logic-apps-deploy-azure-resource-manager-templates.md)수 있습니다. 또한 Azure는 Azure Portal 또는 API 또는 Sdk를 통해 액세스 하거나 Azure Monitor 로그 및 Application Insights에 대 한 통합 도구를 사용 하 여 액세스할 수 있는 모니터링을 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 서버 리스 앱 빌드](logic-apps-serverless-get-started-vs.md)
* [서버를 사용 하지 않는 customer insights 대시보드 만들기](logic-apps-scenario-social-serverless.md)
* [논리 앱 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)
