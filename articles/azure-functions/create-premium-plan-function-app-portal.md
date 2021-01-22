---
title: 포털에서 Azure Functions 프리미엄 계획 만들기
description: Azure Portal를 사용 하 여 프리미엄 계획에서 실행 되는 함수 앱을 만드는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676589"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Azure Portal에서 프리미엄 계획 함수 앱 만들기

Azure Functions는 가상 네트워크 연결, 콜드 부팅 및 프리미엄 하드웨어를 제공 하는 확장 가능한 프리미엄 요금제를 제공 합니다. 자세한 내용은 [Azure Functions Premium 요금제](functions-premium-plan.md)를 참조 하세요. 

이 문서에서는 Azure Portal를 사용 하 여 프리미엄 계획에서 함수 앱을 만드는 방법에 대해 알아봅니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 더 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

이제 새 함수 앱에서 함수를 만들 수 있습니다. 이러한 함수는 [프리미엄 계획](functions-premium-plan.md)의 이점을 활용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [HTTP 트리거된 함수 추가] (./functions-get-started.md
