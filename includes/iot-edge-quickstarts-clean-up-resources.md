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
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008504"
---
권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다.

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> Azure 리소스와 리소스 그룹을 삭제하면 되돌릴 수 없습니다. 이러한 항목을 삭제하면 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관하려는 리소스가 있는 기존 리소스 그룹 내에 IoT 허브를 만든 경우 리소스 그룹을 삭제하지 말고 IoT 허브 리소스만 삭제하면 됩니다.
>

IoT 허브만을 삭제하려면 다음 명령을 실행합니다. \<YourIoTHub>를 IoT 허브 이름으로 \<TestResources>를 리소스 그룹 이름으로 바꿉니다.

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


이름으로 전체 리소스 그룹을 삭제하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹**을 선택합니다.

2. **이름을 기준으로 필터링** 텍스트 상자에 IoT 허브가 들어 있는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록에서 리소스 그룹의 오른쪽에서 줄임표(**...**)를 선택한 다음, **리소스 그룹 삭제**를 선택합니다.

    ![리소스 그룹 삭제](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. 리소스 그룹 삭제를 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인하고 **삭제**를 선택합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.






