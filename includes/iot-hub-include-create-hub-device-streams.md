---
title: 포함 파일(디바이스 스트림 미리 보기)
description: 포함 파일(디바이스 스트림 미리 보기)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 700dd305cf5365a604a5613bd5b48dbd2ac8f840
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020850"
---
이 섹션에서는 [Azure Portal](https://portal.azure.com)을 사용하여 IoT Hub를 만드는 방법에 대해 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **리소스 만들기**를 클릭한 후 **Marketplace 검색** 필드에 *IoT Hub*를 입력합니다.

1. 검색 결과에서 **IoT Hub**, **만들기**를 차례로 선택합니다.

1. **기본** 탭에서 다음과 같이 필드를 완성합니다.

   - **구독**: 허브에 사용할 구독을 선택합니다.

   - **리소스 그룹**: 리소스 그룹을 선택하거나 새로 만듭니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 사용하려는 이름을 입력합니다. 기존 리소스 그룹을 사용하려면 해당 기존 리소스 그룹을 선택합니다. 자세한 내용은 [Azure Resource Manager 리소스 그룹 관리](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.

   - **지역**: 허브를 배치할 지역을 선택합니다. **미국 중부** 또는 **미국 중부 EUAP** 중에서 IoT Hub 디바이스 스트림 미리 보기를 지원하는 Azure 지역을 선택합니다.

   - **IoT Hub 이름**: 허브 이름을 입력합니다. 이 이름은 전역적으로 고유해야 합니다. 입력한 이름을 사용할 수 있으면 녹색 확인 표시가 나타납니다.

   ![Azure Portal에서 IoT Hub 만들기](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. 완료되면 **다음: 크기 및 규모**를 선택하여 허브를 계속 만듭니다.

   ![Azure Portal을 사용하여 새 IoT Hub의 크기 및 규모 설정](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   **크기 및 배율**에서 기본 설정을 수락하고 맨 아래에서 **검토 + 만들기**를 선택할 수 있습니다. 다음 옵션을 살펴보세요.

   - **가격 책정 및 크기 조정 계층**: 선택한 계층입니다. 표준 계층(**S1**, **S2** 또는 **S3**) 또는 **F1: 체험 계층**을 선택합니다. 이 선택은 허브에서 예상하는 Fleet의 크기와 비 스트리밍 워크로드(예: 원격 분석 메시지)에 따라 결정될 수도 있습니다. 예를 들어 무료 계층은 테스트와 평가를 위해 제공됩니다. IoT Hub에 500개 디바이스를 연결할 수 있으며 하루에 8,000개 메시지까지 허용합니다. 각 Azure 구독은 체험 계층에 하나의 IoT Hub를 만들 수 있습니다. 

   - **IoT Hub 단위 수**: 하루 단위당 허용되는 메시지의 수는 허브의 가격 책정 계층에 따라 다릅니다. 허브에서 예상하는 비 스트리밍 워크로드에 따라 결정됩니다. 지금은 1을 선택하면 됩니다.

   - **고급 설정** > **디바이스-클라우드 파티션**: 이 속성은 디바이스-클라우드 메시지를 메시지의 동시 판독기 수와 연결합니다. 대부분의 허브에는 4개의 파티션만 필요합니다.

   계층 옵션에 대한 자세한 내용은 [적절한 IoT Hub 계층 선택](../articles/iot-hub/iot-hub-scaling.md)을 참조하세요.

1. 선택 사항을 검토하려면 **검토 + 만들기**를 선택합니다. 결과는 다음과 유사합니다.

   ![새 IoT Hub를 만들기 위한 정보](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. 새 IoT Hub를 만들려면 **만들기**를 선택합니다. 이 프로세스는 몇 분 정도 걸립니다.
