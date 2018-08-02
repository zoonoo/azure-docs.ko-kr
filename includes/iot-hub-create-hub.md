---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: fce9e42e24be7f8678292a5d98a683ca4e579cd2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39357050"
---
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **리소스 만들기** > **사물 인터넷** > **IoT Hub**를 선택합니다.
   
    ![IoT Hub를 탐색하는 Azure Portal 스크린 샷](./media/iot-hub-create-hub/create-iot-hub1.png)

3. **IoT Hub** 창에서 IoT Hub에 다음 정보를 입력합니다.

   * **구독**: 이 IoT Hub를 만드는 데 사용할 구독을 선택합니다.

   * **리소스 그룹**: IoT Hub를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../articles/azure-resource-manager/resource-group-portal.md)를 참조하세요.

   * **지역**: 가장 가까운 위치를 선택합니다.

   * **이름**: IoT Hub에 대한 이름을 만듭니다. 입력한 이름을 사용할 수 있으면 녹색 확인 표시가 나타납니다.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT Hub 기본 사항 창](./media/iot-hub-create-hub/create-iot-hub2.png)

4. **다음: 크기 및 규모**를 선택하여 IoT Hub를 계속 만듭니다. 

5. **가격 책정 및 규모 계층**을 선택합니다. 이 문서의 경우 구독에서 아직 사용할 수 있다면 **F1 - 무료** 계층을 선택합니다. 자세한 내용은 [가격 및 크기 계층](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

   ![IoT Hub 크기 및 규모 창](./media/iot-hub-create-hub/create-iot-hub3.png)

6. **검토 + 만들기**를 선택합니다.

7. IoT Hub 정보를 검토한 다음, **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행 상황을 모니터링할 수 있습니다.