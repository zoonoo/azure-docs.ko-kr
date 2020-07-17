---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648256"
---
Azure Functions는 CORS(원본 간 리소스 공유)를 지원합니다. CORS는 [포털](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) 및 [Azure CLI](/cli/azure/functionapp/cors)를 통해 구성됩니다. CORS에 허용된 원본 목록은 함수 앱 수준에서 적용됩니다. CORS를 사용하면 응답에 `Access-Control-Allow-Origin` 헤더가 포함됩니다. 자세한 내용은 [크로스-원본 자원 공유(CORS)](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)를 참조하십시오. 