---
title: Azure IoT Central 응용 프로그램에서 장치 관리 | Microsoft Docs
description: 운영자로서 Azure IoT Central 응용 프로그램에서 장치를 관리하는 방법을 알아봅니다.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: dc241612149de5c4ea5c1d2e698741e77d429fc7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004887"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에서 장치 관리

이 문서에서는 운영자로서 Azure IoT Central 애플리케이션에서 디바이스를 관리하는 방법을 설명합니다. 운영자로서 다음과 같은 일을 할 수 있습니다.

- **Device Explorer** 페이지를 사용하여 Azure IoT Central 애플리케이션에 연결될 디바이스를 보고, 추가하며 삭제할 수 있습니다.
- 장치 인벤토리를 최신 상태로 유지합니다.
- 장치 속성에 저장된 값을 변경하여 장치 메타데이터를 최신 상태로 유지합니다.
- **설정** 페이지에서 특정 장치의 설정을 업데이트하여 장치 동작을 제어합니다.

## <a name="view-your-devices"></a>장치 보기

개별 장치를 보려면:

1. 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다. 여기서 [장치 템플릿](howto-set-up-template.md) 목록을 볼 수 있습니다.

1. 왼쪽 창의 템플릿에서 디바이스 템플릿을 선택합니다.

1. Device Explorer 페이지의 오른쪽 창에 이 디바이스 템플릿으로 만든 디바이스 목록이 아래 그림과 같이 표시됩니다. 개별 디바이스를 선택하면 해당 디바이스의 디바이스 세부 정보 페이지를 볼 수 있습니다.

    [![장치 세부 정보 페이지](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>디바이스 추가

Azure IoT Central 응용 프로그램에 장치를 추가하려면:

1. 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.

1. 장치를 만들 장치 템플릿을 선택합니다.

1. + **새로 만들기**를 선택합니다.

1. **실제 장치** 또는 **시뮬레이션된 장치**를 선택합니다. 실제 장치는 Azure IoT Central 응용 프로그램에 연결하는 물리적 장치입니다. 시뮬레이션된 장치에는 Azure IoT Central에서 생성한 샘플 데이터가 있습니다. 이 예제에서는 실제 장치를 사용합니다. **실제 장치**를 선택하여 새 장치의 **장치 세부 정보** 페이지로 이동합니다.


## <a name="import-devices"></a>디바이스 가져오기

대량의 장치를 응용 프로그램에 연결할 수 있도록 Azure IoT Central은 CSV 파일을 통해 장치 대량 가져오기 기능을 제공합니다. CSV 파일에는 다음 열 및 헤더가 있어야 합니다.
1.  IOTC_DeviceID**<span style="color:Red">(소문자여야 함)</span>**
1.  IOTC_DeviceName(선택 사항)


장치를 응용 프로그램에 대량으로 등록하려면:

1. 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.

1. 왼쪽 패널에서 장치를 대량으로 만들 장치 템플릿을 선택합니다.

 >   [!NOTE] 
    아직 장치 템플릿이 없는 경우 **연결되지 않은 장치** 아래에서 장치를 가져와서 아무런 템플릿 없이 등록할 수 있습니다. 장치를 가져오면 후속 단계로 템플릿과 장치를 연결할 수 있습니다.

1. **가져오기**를 클릭합니다.

    [![가져오기 작업](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. 가져올 장치 ID 목록이 들어 있는 CSV 파일을 선택합니다.

1. 파일이 업로드되면 장치 가져오기가 시작됩니다. 장치 그리드 위쪽에서 가져오기 상태를 추적할 수 있습니다.

1. 가져오기가 완료되면 장치 그리드에 성공 메시지가 표시됩니다.

    [![가져오기 성공](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

장치 가져오기 작업이 실패하면 장치 그리드에 오류가 표시됩니다. 모든 오류를 캡처하는 로그 파일이 생성되며 오류 메시지를 클릭하여 다운로드할 수 있습니다.


**템플릿과 장치 연결**

**연결되지 않은 장치** 아래에서 가져오기를 시작하여 장치를 등록하는 경우 해당 장치는 어떤 장치 템플릿 연결 없이도 생성됩니다. 데이터 및 장치에 대한 기타 세부 정보를 탐색하려면 장치는 템플릿과 연결되어 있어야 합니다. 템플릿과 장치를 연결하려면 이러한 단계를 수행합니다.
1. 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.
1. 왼쪽 패널에서 **연결되지 않은 장치**를 선택합니다.
    [![연결되지 않은 장치](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. 템플릿과 연결하려는 장치를 선택합니다.
1. **연결** 옵션을 클릭합니다.
    [![장치 연결](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. 사용 가능한 템플릿 목록에서 템플릿을 선택한 다음, **연결** 단추를 클릭합니다.
1. 선택한 장치는 해당 장치 템플릿 아래로 이동합니다.

 >   [!NOTE] 
    장치가 템플릿과 연결되면 다른 템플릿과 연결되거나 연결 해제될 수 없습니다.

## <a name="export-devices"></a>내보내기 디바이스

장치를 프로비전하여 IoT Central에 연결하려면 IoT Central에서 생성되는 장치의 연결 문자열이 필요합니다. 내보내기 기능을 사용하여 응용 프로그램에서 대량으로 장치의 연결 문자열 및 다른 속성을 가져올 수 있습니다. 내보내기를 수행하면 선택한 모든 장치에 대한 장치 ID, 장치 이름 및 기본 연결 문자열이 포함된 CSV 파일이 생성됩니다.

응용 프로그램에서 장치를 대량으로 내보내려면 다음을 수행합니다.
1. 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.

1. 왼쪽 패널에서 장치를 내보낼 장치 템플릿을 선택합니다.

1. 내보낼 장치를 선택한 다음, **내보내기** 동작을 클릭합니다.

    [![내보내기](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. 내보내기 프로세스가 시작되고 표의 맨 위에서 상태를 추적할 수 있습니다. 

1. 내보내기가 완료되면 성공 메시지가 생성된 파일을 다운로드할 수 있는 링크와 함께 표시됩니다.

1. 디스크의 로컬 폴더에 파일을 다운로드하려면 **성공 메시지**를 클릭합니다.

    [![내보내기 성공](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. 내보낸 CSV 파일에는 다음의 열 정보(**디바이스 ID, 디바이스 이름, 디바이스 기본/보조 키, 및 기본/보조 인증서 지문**)가 있습니다.
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

## <a name="delete-a-device"></a>디바이스 삭제

Azure IoT Central 응용 프로그램에서 실제 장치 또는 시뮬레이션된 장치를 삭제하려면:

1. 탐색 메뉴에서 **Device Explorer**를 선택합니다.

1. 삭제할 장치의 장치 템플릿을 선택합니다.

1. 삭제할 장치 옆에 있는 확인란을 선택합니다.

1. **삭제**를 선택합니다.

## <a name="change-a-device-setting"></a>장치 설정 변경

설정은 장치의 동작을 제어합니다. 다시 말해서, 설정을 통해 장치에 입력을 제공할 수 있습니다. **장치 세부 정보** 페이지에서 장치 설정을 살펴보고 업데이트할 수 있습니다.

1. 탐색 메뉴에서 **Device Explorer**를 선택합니다.

1. 설정을 변경하려는 장치의 장치 템플릿을 선택합니다.

1. **설정** 탭을 선택합니다. 장치에 있는 모든 설정과 현재 값이 표시됩니다. 각 설정에 대해 장치가 여전히 동기화 중인지 확인할 수 있습니다.

1. 원하는 값으로 설정을 수정합니다. 한 번에 여러 설정을 수정하고 동시에 업데이트할 수 있습니다.

1. **업데이트**를 선택합니다. 값이 장치로 전송됩니다. 장치에서 설정 변경을 확인하면 설정 상태가 **동기화됨**으로 돌아갑니다.

## <a name="change-a-property"></a>속성 변경

속성은 도시나 일련 번호처럼 장치와 연결된 장치 메타데이터입니다. **장치 세부 정보** 페이지에서 속성을 살펴보고 업데이트할 수 있습니다.

1. 탐색 메뉴에서 **Device Explorer**를 선택합니다.

1. 속성을 변경하려는 장치의 장치 템플릿을 선택합니다.

1. **속성** 탭을 선택하면 모든 속성이 표시됩니다.

1. 원하는 값으로 애플리케이션 속성을 수정합니다. 한 번에 여러 속성을 수정하고 동시에 업데이트할 수 있습니다. **업데이트**를 선택합니다.

> [!NOTE]
> _장치 속성_의 값은 변경할 수 없습니다 장치 속성은 장치에서 설정하며 Azure IoT Central 응용 프로그램 내에서 읽기 전용입니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 장치를 관리하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [장치 집합을 사용하는 방법](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
