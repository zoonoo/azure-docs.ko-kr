---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559007"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

[소비자 그룹](../articles/event-hubs/event-hubs-features.md#event-consumers)은 앱과 Azure 서비스가 동일한 이벤트 허브 엔드포인트의 데이터를 독립적으로 사용할 수 있는 이벤트 스트림에 대한 독립적인 보기를 제공합니다. 이 섹션에서는 끝점에서 데이터를 끌어올 수 있도록이 자습서의 뒷부분에서 사용 되는 IoT hub의 기본 제공 끝점에 소비자 그룹을 추가 합니다.

IoT Hub에 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub를 엽니다.

2. 왼쪽 창에서 **기본 제공 끝점** 을 선택 하 고, 오른쪽 창에서 **이벤트** 를 선택 하 고, **소비자 그룹** 아래에 이름을 입력 합니다. **저장** 을 선택합니다.

   ![IoT Hub에서 소비자 그룹 만들기](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)