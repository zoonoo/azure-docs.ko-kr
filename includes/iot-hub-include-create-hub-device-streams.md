---
title: 포함 파일(디바이스 스트림 미리 보기)
description: 포함 파일(디바이스 스트림 미리 보기)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ea82bdca23d8b34898261eb60fd908d281451fd0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825303"
---
이 섹션에서는 [Azure Portal](https://portal.azure.com)을 사용하여 IoT Hub를 만드는 방법에 대해 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **+ 리소스 만들기**, **사물 인터넷**을 차례로 선택합니다.

3. 오른쪽의 목록에서 **Iot Hub**를 클릭합니다. IoT 허브를 만들기 위한 첫 번째 화면이 표시됩니다.

   ![Azure Portal에서 허브 만들기를 보여 주는 스크린샷](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   다음 필드를 입력합니다.

   **구독**: IoT 허브에 사용할 구독을 선택합니다.

   **리소스 그룹**: 새 리소스 그룹을 만들거나 기존 그룹을 사용할 수 있습니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 클릭하고 사용하려는 이름을 입력합니다. 기존 리소스 그룹을 사용하려면 **기존 항목 사용**을 클릭하고 드롭다운 목록에서 리소스 그룹을 선택합니다. 자세한 내용은 [Azure Resource Manager 리소스 그룹 관리](../articles/azure-resource-manager/manage-resource-groups-portal.md)를 참조하세요.

   **지역**: 허브를 배치할 지역입니다. 지원되는 지역을 선택해야 합니다(예: 미국 중부 또는 미국 중부 EUAP).

   **IoT Hub 이름**: IoT Hub의 이름을 입력합니다. 이 이름은 전역적으로 고유해야 합니다. 입력한 이름을 사용할 수 있으면 녹색 확인 표시가 나타납니다.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. **다음: 크기 및 규모**를 클릭하여 IoT Hub를 계속 만듭니다.

   ![Azure Portal을 사용하여 새 IoT 허브의 크기 및 배율 설정을 보여 주는 스크린샷](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)


   이 화면에서 기본값을 적용하고 아래쪽의 **검토 + 만들기**를 클릭하면 됩니다. 

   **가격 책정 및 크기 조정 계층**: 표준(S1, S2, S3) 중 하나 또는 무료(F1) 계층을 선택해야 합니다. 또한 이 선택은 허브에서 예상하는 fleet의 크기 및 비 스트리밍 작업에 따라 안내될 수도 있습니다. 예를 들어 무료 계층은 테스트와 평가를 위해 제공됩니다. IoT Hub에 500개 디바이스를 연결할 수 있으며 하루에 8,000개 메시지까지 허용합니다. 각 Azure 구독은 무료 계층에 하나의 IoT Hub를 만들 수 있습니다. 

   **IoT Hub 단위**: 이 선택은 허브에서 예상하는 비 스트리밍 작업에 따라 다를 수 있습니다. 지금은 1을 선택할 수 있습니다.

   다른 계층 옵션에 대한 자세한 내용은 [적절한 IoT Hub 계층 선택](../articles/iot-hub/iot-hub-scaling.md)을 참조하세요.

5. **검토 + 만들기**를 클릭하여 선택 사항을 검토합니다. 다음 화면과 비슷하게 표시됩니다.

   ![새 IoT 허브를 만들기 위한 정보를 검토하는 스크린샷](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. **만들기**를 클릭하여 새 IoT 허브를 만듭니다. 허브를 만드는 데 몇 분이 걸립니다.
