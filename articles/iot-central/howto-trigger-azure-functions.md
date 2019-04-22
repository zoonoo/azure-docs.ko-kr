---
title: Azure IoT Central에서 웹후크를 사용하여 Azure Functions 트리거
description: Azure IoT Central에서 규칙이 트리거될 때마다 실행되는 함수 앱을 만듭니다.
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0d92e9bdf8ec207e5ef0e3f891c162182b5a4fff
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264848"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Azure IoT Central에서 웹후크를 사용하여 Azure Functions 트리거

*이 항목은 빌더 및 관리자에게 적용됩니다.*

Azure Functions를 사용하여 서버리스 코드를 IoT Central 규칙의 웹후크 출력에서 실행합니다. VM을 프로 비전 하거나 Azure Functions를 사용 하도록 웹 앱을 게시 하려면 없지만 대신 서버 리스이 코드를 실행할 수 있습니다. Azure Functions를 사용하여 SQL Database 또는 Event Grid와 같은 최종 대상에 보내기 전에 웹후크 페이로드를 변환합니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="how-to-connect-azure-functions"></a>Azure Functions를 연결하는 방법

1. [Azure portal에서 새 함수 앱 만들기](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)합니다.

    ![Azure portal에서 새 함수 앱 만들기](media/howto-trigger-azure-functions/createfunction.png)

2. 함수 앱을 확장 하 고 선택 합니다 **+ 단추** 함수 옆에 있는 합니다. 이 함수는 함수 앱의 첫 번째, 선택 **포털에서** 개발 환경 및 선택 **계속**합니다.

    ![함수 앱에서 사용자 지정 함수 선택](media/howto-trigger-azure-functions/customfunction.png)

3. 선택할 **Webhook + API** 템플릿과 선택 **만들기**합니다. 이 항목에서는.NET 기반 Azure 함수를 사용합니다.

    ![제네릭 웹후크 트리거 선택](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. 새 함수에서 선택 **<> / 함수 URL 가져오기**복사 하 여 값을 저장 합니다. 이 값을 사용하여 웹후크를 구성합니다.

    ![함수의 URL 가져오기](media/howto-trigger-azure-functions/getfunctionurl.png)

4. IoT Central에서 함수 앱에 연결하려는 규칙으로 이동합니다.

5. 웹후크 동작을 추가합니다. **표시 이름**을 입력하고 이전에 **콜백 URL**로 복사한 함수 URL에 붙여넣습니다.

    ![콜백 URL 필드에 함수 URL을 입력합니다.](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. 규칙을 저장합니다. 이제 규칙이 트리거되면 webhook 호출 함수 앱이 실행 되도록 합니다. 함수 앱에서 선택할 수 있습니다 **모니터** 함수 호출 기록을 볼 수 있습니다. App Insights 또는 클래식 보기를 사용하여 기록을 검색할 수 있습니다.

    ![함수의 호출 기록 모니터링](media/howto-trigger-azure-functions/monitorfunction.PNG)

자세한 내용은 [제네릭 웹후크를 통해 트리거되는 함수 만들기](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)에 관한 Azure Functions 문서를 확인해보세요.

## <a name="next-steps"></a>다음 단계
웹후크를 설정하고 사용하는 방법을 배웠으므로 제안되는 다음 단계는 [Microsoft Flow에서 워크플로 만들기](howto-add-microsoft-flow.md)입니다.
