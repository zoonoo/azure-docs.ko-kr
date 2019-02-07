---
title: Azure IoT Central 애플리케이션에서 작업 만들기 및 실행 | Microsoft Docs
description: Azure IoT Central 작업에서는 디바이스 속성/설정 업데이트, 명령 실행 등의 대량 디바이스 관리 기능을 사용할 수 있습니다.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 02/04/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: e418ec7d22622c341abd972763d78ac2f0df46d9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772837"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에서 작업 만들기 및 실행

Microsoft Azure IoT Central에서는 작업을 사용하여 연결된 디바이스를 대규모로 관리할 수 있습니다. 이 작업 기능을 통해 디바이스 속성, 설정 및 명령의 대량 업데이트를 수행할 수 있습니다. 이 문서에서는 애플리케이션에서 작업 사용을 시작하는 과정을 안내합니다.

## <a name="create-and-run-a-job"></a>작업 만들기 및 실행

이 섹션에서는 작업을 만들고 실행하는 방법을 보여줍니다. 각 단계에서는 팬 속도를 높여야 하는 냉장고 자판기 디바이스용 작업을 실행하는 방법을 보여 주는 예제를 진행합니다.

1. 탐색 창에서 작업으로 이동합니다.

1. **+ 새로 만들기**를 클릭하여 새 작업 만들기를 시작합니다.

    ![새 작업 만들기](./media/howto-run-a-job-experimental/createnewjob.png)

1. 작성 중인 작업을 쉽게 확인할 수 있도록 이름 및 설명을 입력합니다.

1. 작업을 적용할 디바이스 집합을 선택합니다. 디바이스 집합을 선택하면 선택한 디바이스 집합 내의 디바이스가 화면 오른쪽에 채워집니다. 손상된 디바이스 집합을 선택하면 디바이스가 표시되지 않으며 디바이스 집합이 손상되었다는 메시지가 표시됩니다.

1. 다음으로 정의할 작업 유형(설정, 속성 또는 명령)을 선택합니다. 선택한 작업 유형 옆의 **+** 를 클릭하고 원하는 작업을 추가합니다.

    ![작업 구성](./media/howto-run-a-job-experimental/configurejob.png)

1. 오른쪽에서 작업을 실행할 디바이스를 선택합니다. 맨 위의 확인란을 클릭하면 전체 디바이스 집합의 모든 디바이스가 선택됩니다. 이름 옆의 확인란을 클릭하면 현재 페이지의 모든 디바이스가 선택됩니다.

1. 원하는 디바이스를 선택한 후 **실행**을 선택합니다. 이제 작업이 기본 **작업** 페이지에 표시됩니다. 이 보기에서 현재 실행 중인 작업 및 이전에 실행한 작업의 기록을 확인할 수 있습니다. 실행 중인 작업은 항상 목록 맨 위에 표시됩니다.

    ![작업 보기](./media/howto-run-a-job-experimental/viewjob.png)

    > [!NOTE]
    > 최대 30일 전까지 실행했던 작업의 기록을 확인할 수 있습니다.

1. 작업 개요를 표시하려면 목록에서 확인하려는 작업 이름을 클릭합니다. 이 개요에는 작업 세부 정보, 디바이스 및 디바이스 상태가 포함되어 있습니다.

    ![디바이스 상태 보기](./media/howto-run-a-job-experimental/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>실행 중인 작업 중지

현재 실행 중인 작업을 중지하려면 중지할 실행 중인 작업의 이름을 클릭합니다. 패널에서 **중지** 단추를 선택합니다. 작업이 중지되었음을 반영하여 작업 상태가 변경됩니다.

   ![작업 중지](./media/howto-run-a-job-experimental/stopjob.png)

### <a name="run-a-stopped-job"></a>중지된 작업 실행

현재 중지된 작업을 실행하려면 실행하려는 중지된 작업의 이름을 클릭합니다. 패널에서 **실행** 단추를 선택합니다. 현재 작업이 실행되고 있음을 반영하도록 작업 상태가 변경됐습니다.

   ![다시 시작된 작업](./media/howto-run-a-job-experimental/resumejob.png)

## <a name="view-the-job-status"></a>작업 상태 확인

작업이 작성되면 작업의 최신 상태 메시지가 적용되어 **상태** 열이 업데이트됩니다. 상태 메시지의 의미는 다음과 같습니다.

| 상태 메시지       | 상태 의미                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | 모든 디바이스에서 이 작업이 실행되었습니다.              |
| 실패               | 이 작업은 실패했으며 디바이스에서 완전히 실행되지 않았습니다.  |
| Pending              | 아직 디바이스에서 이 작업 실행이 시작되지 않았습니다.        |
| 실행 중              | 이 작업이 현재 디바이스에서 실행되고 있습니다.             |
| 중지됨              | 이 작업을 사용자가 수동으로 중지했습니다.           |

상태 메시지 뒤에는 작업 내 디바이스의 개요가 표시됩니다. 이러한 디바이스 상태의 의미는 다음과 같습니다.

| 상태 메시지       | 상태 의미                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | 작업이 정상적으로 실행된 디바이스의 수입니다.  |
| 실패               | 작업 실행이 실패한 디바이스의 수입니다.      |

### <a name="view-the-device-status"></a>디바이스 상태 확인

작업의 각 디바이스 상태를 보려면 작업 이름을 클릭합니다. 디바이스 상태에서 작업의 세부 정보와 이 특정 작업에 포함된 모든 디바이스를 확인할 수 있습니다. 각 디바이스 이름 옆에는 다음 상태 메시지 중 하나가 표시됩니다.

| 상태 메시지       | 상태 의미                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | 이 디바이스에서 작업이 실행되었습니다.                                     |
| 실패               | 이 디바이스에서 작업 실행이 실패했습니다. 함께 제공되는 오류 메시지에 자세한 정보가 표시됩니다.  |
| Pending              | 이 디바이스에서 작업이 아직 실행되지 않았습니다.                                  |

> [!NOTE]
> 삭제된 디바이스는 선택할 수 없으며, 디바이스 ID와 함께 삭제된 항목으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 작업을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

- [디바이스 설정 사용](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [디바이스 관리](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [장치 템플릿 버전 관리](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
