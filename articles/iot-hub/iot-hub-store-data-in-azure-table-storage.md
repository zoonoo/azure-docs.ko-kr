---
title: Azure 데이터 저장소에 IoT Hub 메시지 저장 | Microsoft Docs
description: IoT Hub 메시지 라우팅을 사용하여 Azure BLOB 저장소에 IoT Hub 메시지를 저장합니다. IoT Hub 메시지에는 IoT 장치에서 보낸 센서 데이터와 같은 정보가 있습니다.
author: rangv
manager: ''
keywords: IoT 데이터 저장소, IoT 센서 데이터 저장소
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856293"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Azure BLOB 저장소에 센서 데이터를 포함한 IoT Hub 메시지 저장

![종단 간 다이어그램](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>학습 내용

Azure 저장소 계정 및 Azure Functions 앱을 만들어 Azure Blob Storage에 IoT Hub 메시지를 저장하는 방법에 대해 알아봅니다.

## <a name="what-you-do"></a>수행할 작업

- Azure 저장소 계정 만들기
- 저장소에 메시지를 라우트하도록 IoT Hub를 설정합니다.

## <a name="what-you-need"></a>필요한 항목

- 다음 요구 사항에 맞게 [장치를 설정](iot-hub-raspberry-pi-kit-node-get-started.md)합니다.
  - 활성 Azure 구독
  - 구독 중인 IoT Hub 
  - 메시지를 IoT Hub로 보내는 실행 중인 응용 프로그램

## <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **저장소** > **저장소 계정**을 클릭합니다.

2. 저장소 계정에 필요한 정보를 입력합니다.

   ![Azure Portal에서 저장소 계정 만들기](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **이름** - 저장소 계정의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   * **계정 종류**: `Storage (general purpose v1)`를 선택합니다.

   * **위치**: IoT Hub에서 사용하는 것과 같은 위치를 선택합니다.

   * **복제**: `Locally-redundant storage (LRS)`를 선택합니다.

   * **성능**: `Standard`를 선택합니다.

   * **보안 전송 필요**: `Disabled`를 선택합니다.

   * **구독**: Azure 구독을 선택합니다.

   * **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   * **대시보드에 고정**: 대시보드에서 IoT Hub에 쉽게 액세스하려면 이 옵션을 선택합니다.

3. **만들기**를 클릭합니다.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>저장소에 메시지를 라우팅하도록 IoT Hub 준비

IoT Hub는 기본적으로 Azure 저장소에 메시지를 라우팅하는 기능을 지원합니다. Azure IoT Hub 사용자 지정 끝점에 대해 자세히 알아보려면 [기본 제공 IoT Hub 끝점 목록이](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints)을 참조할 수 있습니다.

### <a name="add-storage-as-a-custom-endpoint"></a>사용자 지정 끝점으로 저장소 추가

1. Azure Portal에서 IoT Hub로 이동합니다. 

2. **끝점** > **추가**를 클릭합니다. 

3. 끝점 이름을 지정하고 끝점 형식으로 **Azure Storage 컨테이너**를 선택합니다. 

4. 선택기를 사용하여 이전 섹션에서 만든 저장소 계정을 선택합니다. 저장소 컨테이너를 만들고 선택한 다음 **확인**을 클릭합니다.

   ![Azure IoT Hub에서 끝점 만들기](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>저장소에 데이터를 라우팅하는 경로 추가

1. **경로** > **추가**를 클릭하고 경로 이름을 입력합니다. 

2. 데이터 원본으로 **장치 메시지**를 선택하고, 방금 만든 저장소 끝점을 경로의 끝점으로 선택합니다. 

3. 쿼리 문자열로 `true`를 입력하고 **저장**을 클릭합니다.

   ![Azure IoT Hub에서 경로 만들기](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>실행 부하 과다 경로 원격 분석에 대한 경로 추가(선택 사항)

Azure IoT Hub는 기본적으로 다른 경로와 일치하지 않는 모든 메시지를 기본 끝점으로 라우팅합니다. 이제 모든 원격 분석 데이터 메시지가 저장소에 메시지를 라우팅하는 규칙과 일치하므로 기본 끝점에 메시지를 기록할 또 다른 경로를 추가해야 합니다. 메시지를 여러 끝점에 라우팅하더라도 추가 비용은 없습니다.

> [!NOTE]
> 원격 분석 데이터 메시지에 대한 추가 처리를 수행하지 않는 경우 이 단계를 건너뛰어도 됩니다.

1. 경로 창에서 **추가**를 클릭하고 경로 이름을 입력합니다. 

2. 데이터 원본으로 **장치 메시지**를 선택하고 끝점으로 **이벤트**를 선택합니다. 

3. 쿼리 문자열로 `true`를 입력하고 **저장**을 클릭합니다.

  ![Azure IoT Hub에서 실행 부하 과다 경로 만들기](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>저장소 컨테이너에 있는 메시지 확인

1. 장치에서 샘플 응용 프로그램을 실행하여 IoT Hub로 메시지를 보냅니다.

2. [Azure Storage 탐색기를 다운로드하고 설치](http://storageexplorer.com/)합니다.

3. Storage 탐색기를 열고 **Azure 계정 추가** > **로그인**을 차례로 클릭한 다음 Azure 계정에 로그인합니다.

4. Azure 구독> **저장소 계정** > 저장소 계정> **BLOB 컨테이너**> 컨테이너를 차례로 클릭합니다.

   장치에서 IoT Hub로 보낸 메시지가 BLOB 컨테이너에 기록되어 있어야 합니다.

## <a name="clean-up-resources"></a>리소스 정리 

이 자습서에서는 저장소 계정을 추가한 다음, 저장소 계정에 기록할 IoT Hub의 메시지에 대한 라우팅을 추가했습니다. 생성한 자원을 정리하려면 라우팅 정보를 제거한 후 저장소 계정을 삭제합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **리소스 그룹**을 클릭하고 사용한 리소스 그룹을 선택합니다. 그룹의 리소스 목록이 표시됩니다. 

   > [!NOTE]
   > 리소스 그룹의 모든 리소스를 제거하려면 **삭제**를 클릭하여 리소스 그룹을 삭제한 후 지시에 따릅니다. 이렇게 하면 해당 리소스 그룹의 모든 항목이 제거되므로 리소스 정리가 완료되고 다음 섹션으로 건너뛸 수 있습니다.

3. 이 자습서에 사용한 IoT Hub를 클릭합니다. 

4. IoT Hub 창에서 **경로**를 클릭합니다. 추가한 라우팅 규칙 옆에 있는 확인란을 클릭한 다음, **삭제**를 클릭합니다. 해당 경로를 삭제할지 묻는 메시지가 표시되면 **예**를 클릭합니다.

   ![라우팅 규칙 제거](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   라우팅 창을 닫습니다. 리소스 그룹 창으로 돌아갑니다.

5. IoT Hub를 다시 클릭합니다. 

6. IoT Hub 창에서 **끝점**을 클릭합니다. 저장소 컨테이너에 대해 추가한 끝점 옆의 확인란을 클릭하고 **삭제**를 클릭합니다. 선택한 끝점을 삭제할지 묻는 메시지가 표시되면 **예**를 클릭합니다.

    ![끝점 제거](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    끝점 창을 닫습니다. 리소스 그룹 창으로 돌아갑니다. 

7.  이 자습서에서 설정한 저장소 계정을 클릭합니다. 

8.  저장소 계정 창에서 **삭제**를 클릭하여 저장소 계정을 삭제합니다. **저장소 계정 삭제** 창으로 이동합니다.

   ![저장소 계정 제거](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  저장소 계정 이름을 입력한 다음, 창 맨 아래에 있는 **삭제**를 클릭합니다. 

## <a name="next-steps"></a>다음 단계

Azure 저장소 계정을 만들고 IoT Hub에서 해당 저장소 계정의 BLOB 컨테이너로 메시지를 전달했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
