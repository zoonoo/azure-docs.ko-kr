---
title: Azure Functions 사용 계획 호스팅
description: Azure Function 사용 계획 호스팅을 통해 동적으로 크기를 조정하는 환경에서 코드를 실행하지만 실행 중에 사용되는 리소스에 대해서만 비용을 지불하는 방법에 대해 알아봅니다.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760545"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions 사용 계획 호스팅

사용 계획을 사용하는 경우 Azure Functions 호스트의 인스턴스는 들어오는 이벤트의 수에 따라 동적으로 추가되고 제거됩니다. 사용 계획은 Azure Functions에 대한 완전한 <em>서버리스</em> 호스팅 옵션입니다.

## <a name="benefits"></a>이점

사용 계획은 높은 부하 기간 동안에도 자동으로 확장됩니다. 사용 계획에서 함수를 실행하는 경우 함수를 실행하는 경우에만 계산 리소스에 대한 요금이 청구됩니다. 소비 계획에서 구성 가능한 시간 후 함수 실행 시간이 초과됩니다.

사용 계획과 다른 계획 및 호스팅 유형에 대한 비교는 [함수 크기 조정 및 호스팅 옵션](functions-scale.md)을 참조하세요.

## <a name="billing"></a>결제

청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, 사용량은 함수 앱 내의 모든 함수에 대해 집계됩니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

사용 계획에서 실행하는 경우 비용을 계산하는 방법에 대한 자세한 내용은 [사용 계획 비용 이해](functions-consumption-costs.md)를 참조하세요.

## <a name="create-a-consumption-plan-function-app"></a>사용 계획에서 함수 앱을 만듭니다.

Azure Portal에서 함수 앱을 만들 때는 사용 계획이 기본값입니다. API를 사용하여 함수 앱을 만들 때 프리미엄 및 전용 요금제를 사용하는 것처럼 App Service 계획을 먼저 만들 필요가 없습니다.

다음 링크를 사용하여 프로그래밍 방식으로 또는 Azure Portal에서 사용 계획으로 서버리스 함수 앱을 만드는 방법을 알아봅니다.

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure Portal](./functions-get-started.md)
+ [Azure Resource Manager 템플릿](functions-create-first-function-resource-manager.md)

[Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) 또는 [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure)에서 Functions 프로젝트를 게시할 때 사용 계획에서 함수 앱을 만들 수도 있습니다.

## <a name="multiple-apps-in-the-same-plan"></a>동일한 계획의 여러 앱

동일한 지역에 있는 함수 앱은 동일한 사용 계획에 할당할 수 있습니다. 여러 앱을 동일한 사용 계획에서 실행하는 것은 아무런 영향을 미치지 않습니다. 여러 앱을 동일한 사용 계획에 할당하는 것은 각 앱의 복원력, 확장성 또는 안정성에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [Azure Functions 호스팅 옵션](functions-scale.md)
+ [Azure Functions에서 이벤트 기반 크기 조정](event-driven-scaling.md)
