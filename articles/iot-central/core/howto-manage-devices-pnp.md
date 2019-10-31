---
title: Azure IoT Central 애플리케이션에서 디바이스 관리 | Microsoft Docs
description: 운영자로서 Azure IoT Central 애플리케이션에서 디바이스를 관리하는 방법을 알아봅니다.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b5e4e27e059cdc84370bbf7dbf7c6bc651b1968e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177361"
---
# <a name="manage-devices-in-your-azure-iot-central-application-preview-features"></a>Azure IoT Central 응용 프로그램에서 장치 관리 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 운영자로서 Azure IoT Central 애플리케이션에서 디바이스를 관리하는 방법을 설명합니다. 운영자로서 다음과 같은 일을 할 수 있습니다.

- **장치** 페이지를 사용 하 여 Azure IoT Central 응용 프로그램에 연결 된 장치를 확인, 추가 및 삭제할 수 있습니다.
- 디바이스 인벤토리를 최신 상태로 유지합니다.
- 보기에서 장치 속성에 저장 된 값을 변경 하 여 장치 메타 데이터를 최신 상태로 유지 합니다.
- 보기에서 특정 장치에 대 한 설정을 업데이트 하 여 장치의 동작을 제어 합니다.

## <a name="view-your-devices"></a>디바이스 보기

개별 디바이스를 보려면:

1. 왼쪽 창에서 **장치** 를 선택 합니다. 여기에서 장치 템플릿 및 모든 장치 목록이 표시 됩니다.

1. 장치 템플릿을 선택 합니다.

1. **장치 페이지의** 오른쪽 창에 해당 장치 템플릿에서 만든 장치 목록이 표시 됩니다. 개별 디바이스를 선택하면 해당 디바이스의 디바이스 세부 정보 페이지를 볼 수 있습니다.

    ![디바이스 세부 정보 페이지](./media/howto-manage-devices-pnp/devicelist.png)


## <a name="add-a-device"></a>디바이스 추가

Azure IoT Central 애플리케이션에 디바이스를 추가하려면:

1. 왼쪽 창에서 **장치** 를 선택 합니다.

1. 디바이스를 만들 디바이스 템플릿을 선택합니다.

1. \+ **새로 만들기**를 선택합니다.

1. **시뮬레이션** 된 토글을 **On** 또는 **Off**로 설정 합니다. 실제 디바이스는 Azure IoT Central 애플리케이션에 연결하는 물리적 디바이스입니다. 시뮬레이션된 디바이스에는 Azure IoT Central에서 생성한 샘플 데이터가 있습니다.

1. **만들기**를 클릭합니다.

1. 이 장치는 이제이 템플릿에 대 한 장치 목록에 표시 됩니다. 장치를 선택 하 여 장치에 대 한 모든 보기를 포함 하는 장치 세부 정보 페이지를 표시 합니다.

## <a name="import-devices"></a>디바이스 가져오기

대량의 디바이스를 애플리케이션에 연결하려면 CSV 파일에서 대량으로 디바이스를 가져오면 됩니다. CSV 파일에는 다음 열과 헤더가 있습니다.

* **IOTC_DeviceID** - 디바이스 ID는 모두 소문자여야 합니다.
* **IOTC_DeviceName** - 이 열은 선택 사항입니다.

디바이스를 애플리케이션에 대량으로 등록하려면:

1. 왼쪽 창에서 **장치** 를 선택 합니다.

1. 왼쪽 패널에서 디바이스를 대량으로 만들 디바이스 템플릿을 선택합니다.

    > [!NOTE]
    > 아직 장치 템플릿이 없는 경우 **모든 장치** 에서 장치를 가져와 템플릿 없이 등록할 수 있습니다. 장치를 가져온 후에는 템플릿으로 마이그레이션할 수 있습니다.

1. **가져오기**를 선택합니다.

    ![가져오기 작업](./media/howto-manage-devices-pnp/bulkimport1a.png)


1. 가져올 디바이스 ID 목록이 들어 있는 CSV 파일을 선택합니다.

1. 파일이 업로드되면 디바이스 가져오기가 시작됩니다. 장치 작업 패널에서 가져오기 상태를 추적할 수 있습니다. 이 패널은 가져오기가 시작 된 후 자동으로 나타나거나 오른쪽 위 모퉁이에 있는 종 모양 아이콘을 통해 액세스할 수 있습니다.

1. 가져오기가 완료 되 면 장치 작업 패널에 성공 메시지가 표시 됩니다.

    ![가져오기 성공](./media/howto-manage-devices-pnp/bulkimport3a.png)


장치 가져오기 작업에 실패 하면 장치 작업 패널에 오류 메시지가 표시 됩니다. 모든 오류를 캡처하는 로그 파일이 생성되며 이 파일을 다운로드할 수 있습니다.

**템플릿으로 장치 마이그레이션**

**모든 장치**에서 가져오기를 시작 하 여 장치를 등록 하는 경우 장치 템플릿 연결 없이 장치가 생성 됩니다. 디바이스에 대한 데이터 및 기타 세부 정보를 살펴보려면 디바이스가 템플릿과 연결되어 있어야 합니다. 템플릿과 디바이스를 연결하려면 이러한 단계를 수행합니다.

1. 왼쪽 창에서 **장치** 를 선택 합니다.

1. 왼쪽 패널에서 **모든 장치**를 선택 합니다.

    ![연결되지 않은 디바이스](./media/howto-manage-devices-pnp/unassociateddevices1a.png)


1. 그리드에서 필터를 사용 하 여 장치에 대 한 **장치 템플릿** 열의 값이 "연결 되지 않음" 인지 확인할 수 있습니다.

1. 템플릿과 연결하려는 디바이스를 선택합니다.

1. **마이그레이션**을 선택 합니다.

    ![디바이스 연결을 클릭합니다.](./media/howto-manage-devices-pnp/unassociateddevices2a.png)


1. 사용 가능한 템플릿 목록에서 템플릿을 선택 하 고 **마이그레이션**을 선택 합니다.

1. 선택한 디바이스가 선택한 디바이스 템플릿과 연결됩니다.


## <a name="export-devices"></a>내보내기 디바이스

실제 디바이스를 IoT Central에 연결하려면 해당 연결 문자열이 필요합니다. 장치 세부 정보를 대량으로 내보내 장치 연결 문자열을 만드는 데 필요한 정보를 얻을 수 있습니다. 내보내기 프로세스에서는 선택한 모든 장치에 대 한 장치 id, 장치 이름 및 키를 사용 하 여 CSV 파일을 만듭니다.

애플리케이션에서 디바이스를 대량으로 내보내려면 다음을 수행합니다.

1. 왼쪽 창에서 **장치** 를 선택 합니다.

1. 왼쪽 창에서 장치를 내보내려는 장치 템플릿을 선택 합니다.

1. 내보내려는 장치를 선택 하 고 **내보내기** 작업을 선택 합니다.

    ![내보내기](./media/howto-manage-devices-pnp/export1a.png)


1. 내보내기 프로세스가 시작됩니다. 장치 작업 패널을 사용 하 여 상태를 추적할 수 있습니다.

1. 내보내기가 완료되면 생성된 파일을 다운로드할 수 있는 링크와 함께 성공 메시지가 표시됩니다.

1. 파일 **다운로드** 링크를 선택 하 여 디스크의 로컬 폴더에 파일을 다운로드 합니다.

    ![내보내기 성공](./media/howto-manage-devices-pnp/export2a.png)


1. 내보낸 CSV 파일에는 디바이스 ID, 디바이스 이름, 디바이스 키 및 X509 인증서 지문 열이 포함되어 있습니다.

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

연결 문자열 및 실제 장치를 IoT Central 응용 프로그램에 연결 하는 방법에 대 한 자세한 내용은 [Azure IoT Central에서 장치 연결](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)을 참조 하세요.

## <a name="delete-a-device"></a>디바이스 삭제

Azure IoT Central 애플리케이션에서 실제 디바이스 또는 시뮬레이션된 디바이스를 삭제하려면:

1. 왼쪽 창에서 **장치** 를 선택 합니다.

1. 삭제할 디바이스의 디바이스 템플릿을 선택합니다.

1. 필터 도구를 사용 하 여 장치를 필터링 하 고 검색 합니다. 삭제할 장치 옆의 확인란을 선택 합니다.

1. **삭제**를 선택합니다. 장치 작업 패널에서이 삭제의 상태를 추적할 수 있습니다.

## <a name="change-a-property"></a>속성 변경

클라우드 속성은 장치와 연결 된 장치 메타 데이터입니다 (예: 도시 및 일련 번호). 쓰기 가능한 속성은 장치의 동작을 제어 합니다. 다시 말해서, 설정을 통해 디바이스에 입력을 제공할 수 있습니다.  장치 속성은 장치에 의해 설정 되 고 IoT Central 내에서 읽기 전용입니다. 장치에 대 한 **장치 세부 정보** 보기에서 속성을 보고 업데이트할 수 있습니다.

1. 왼쪽 창에서 **장치** 를 선택 합니다.

1. 변경 하려는 속성의 장치 템플릿을 선택 하 고 대상 장치를 선택 합니다.

1. 장치에 대 한 속성을 포함 하는 보기를 선택 합니다 .이 보기를 사용 하 여 값을 입력 하 고 페이지 위쪽에서 **저장** 을 선택할 수 있습니다. 여기에는 장치에 있는 속성 및 현재 값이 표시 됩니다. 클라우드 속성과 쓰기 가능한 속성에는 편집 가능한 필드가 있지만 장치 속성은 읽기 전용입니다. 쓰기 가능한 속성의 경우 필드의 아래쪽에서 동기화 상태를 볼 수 있습니다. 

1. 필요한 값으로 속성을 수정 합니다. 한 번에 여러 속성을 수정 하 고 모두 동시에 업데이트할 수 있습니다.

1. **저장**을 선택합니다. 쓰기 가능한 속성을 저장 한 경우 값이 장치에 전송 됩니다. 장치에서 쓰기 가능한 속성에 대 한 변경 내용을 확인 하면 상태가 **동기화**됨으로 돌아갑니다. 클라우드 속성을 저장 한 경우 값이 업데이트 됩니다.


## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 디바이스를 관리하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [장치 그룹을 사용 하는 방법](tutorial-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->
