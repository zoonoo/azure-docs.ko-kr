---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 05/25/2021
ms.openlocfilehash: 88d0292048edcc6846d0e1637b79f115d1321e70
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369135"
---
다중 테넌트 모델에서 논리 앱 리소스 구조에는 단일 워크플로만 포함될 수 있습니다. 이러한 일대일 관계로 인해 논리 앱과 워크플로는 자주 동의어로 간주되고 참조됩니다. 하지만 단일 테넌트 모델에서는 논리 앱 리소스 구조에 여러 워크플로가 포함될 수 있습니다. 이러한 일대다 관계는 동일한 논리 앱에서 워크플로가 다른 리소스를 공유하고 다시 사용할 수 있음을 의미합니다. 또한 동일한 논리 앱 및 테넌트에 있는 워크플로는 이렇게 공유하는 테넌시와 근접성 덕분에 더 우수한 성능을 제공합니다. 이러한 리소스 구조는 함수 앱이 여러 함수를 호스트할 수 있는 Azure Functions와 유사하게 작동합니다.

논리 앱에서의 워크플로 구성, 성능 및 크기 조정에 관한 자세한 정보와 모범 사례는 단일 테넌트 Azure Logic Apps에 일반적으로 적용할 수 있는 유사한 [Azure Functions용 지침](../articles/azure-functions/functions-best-practices.md)을 참조하세요.