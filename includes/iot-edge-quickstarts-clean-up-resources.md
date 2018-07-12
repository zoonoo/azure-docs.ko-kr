---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c0b9f9e9808de90df84edf2d3c409a921629baee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728308"
---
권장되는 다음 아티클을 계속 진행하는 경우 지금까지 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다.

그렇지 않으면 요금이 부과되지 않도록 이 아티클에서 만든 로컬 구성과 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> Azure 리소스와 리소스 그룹을 삭제하면 되돌릴 수 없습니다. 일단 삭제되면 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

IoT Hub만 삭제하려면 `<YourIoTHub>`를 허브 이름으로 바꾸고 `<TestResources>`를 리소스 그룹 이름으로 바꾼 후에 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


이름으로 전체 리소스 그룹을 삭제하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

2. **이름을 기준으로 필터링...** 텍스트 상자에 IoT Hub가 들어 있는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

    ![삭제](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제**를 클릭합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.






