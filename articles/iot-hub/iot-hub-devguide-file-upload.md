---
title: Azure IoT Hub 파일 업로드 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub의 파일 업로드 기능을 사용하여 디바이스에서 Azure Storage blob 컨테이너로 파일 업로드를 관리합니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: bb0d39ea9e37f87a465ea5803e004a142c3a3fc6
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715163"
---
# <a name="upload-files-with-iot-hub"></a>IoT Hub를 사용하여 파일 업로드

많은 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 즉시 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 예를 들면 다음과 같습니다.
* 큰 이미지 파일
* 동영상 파일
* 자주 샘플링되는 진동 데이터
* 특정 형태의 전처리된 데이터

디바이스에서 이러한 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다. IoT Hub 자체를 통한 브로커 메시지 대신 IoT Hub는 연결된 Azure Storage 계정에 대한 디스패처로 작동합니다. 디바이스는 디바이스가 업로드하려는 파일에 특정된 IoT Hub의 스토리지 토큰을 요청합니다. 디바이스는 SAS URI를 사용하여 스토리지에 파일을 업로드하고 업로드가 완료되면 디바이스는 IoT Hub에 완료 알림을 보냅니다. IoT Hub는 파일 업로드가 완료되었는지 확인합니다.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>사용 시기

파일 업로드를 사용하여 간헐적으로 연결된 디바이스로 업로드되거나 대역폭을 절약하기 위해 압축된 미디어 파일과 대형 원격 분석 배치를 보냅니다. reported 속성, 디바이스-클라우드 메시지 또는 파일 업로드 사용에 대해 궁금한 점이 있으면 [디바이스-클라우드 통신 지침](iot-hub-devguide-d2c-guidance.md)을 참조하세요.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage 계정을 IoT Hub와 연결

IoT 허브에 연결된 Azure Storage 계정을 만들어야 합니다. 

포털을 사용하여 이러한 계정을 만드는 방법은 [스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요. 

[IoT Hub 리소스 공급자 REST API](/rest/api/iothub/iothubresource)를 사용하여 프로그래밍 방식으로 만들 수도 있습니다. 

IoT Hub와 Azure Storage 계정을 연결하면 IoT Hub는 SAS URI를 생성합니다. 디바이스는 Blob 컨테이너에 안전하게 파일을 업로드하는 데 SAS URI를 사용할 수 있습니다.

## <a name="create-a-container"></a>컨테이너 만들기

 포털을 통해 Blob 컨테이너를 만들려면 다음을 수행합니다.

1. 스토리지 계정의 왼쪽 창에서 **데이터 스토리지** 의 **컨테이너** 를 선택합니다.
1. 컨테이너 블레이드에서 **+ 컨테이너** 를 선택합니다.
1. **새 컨테이너** 창이 열리면 컨테이너의 이름을 입력하고 **만들기** 를 선택합니다.

컨테이너를 생성한 후에는 [Azure Portal을 사용하여 파일 업로드 구성](iot-hub-configure-file-upload.md)의 안내에 따릅니다. Blob 컨테이너가 IoT Hub와 연결되고 파일 알림을 사용하는지 확인합니다.

[IoT Hub 리소스 공급자 REST API](/rest/api/iothub/iothubresource)를 사용하여 IoT Hub 스토리지와 연결된 컨테이너를 만들 수도 있습니다.

## <a name="file-upload-using-an-sdk"></a>SDK를 사용하여 파일 업로드

다음 방법 가이드에서는 다양한 SDK 언어로 파일 업로드 프로세스에 대한 전체 연습을 제공합니다. 이러한 가이드에서는 Azure Portal을 사용하여 스토리지 계정을 IoT Hub와 연결하는 방법을 보여 줍니다. 또한 코드 조각을 포함하거나 업로드 프로세스를 안내하는 샘플을 참조합니다.

* [.NET](iot-hub-csharp-csharp-file-upload.md)
* [Java](iot-hub-java-java-file-upload.md)
* [Node.JS](iot-hub-node-node-file-upload.md)
* [Python](iot-hub-python-python-file-upload.md)

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md)는 공유 액세스 서명 URI 검색, 파일 업로드 및 IoT Hub에 완료된 업로드 알림을 자동으로 처리합니다. 방화벽이 Blob Storage 엔드포인트에 대한 액세스를 차단하지만 IoT Hub 엔드포인트에 대한 액세스를 허용하는 경우 파일 업로드 프로세스가 실패하고 IoT C# 디바이스 SDK에 대해 다음과 같은 오류가 표시됩니다.
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> 파일 업로드 기능이 작동하려면 IoT Hub 엔드포인트와 Blob Storage 엔드포인트 모두에 대한 액세스를 디바이스에서 사용할 수 있어야 합니다.
> 


## <a name="initialize-a-file-upload-rest"></a>파일 업로드 초기화(REST)

SDK 중 하나 대신 REST API를 사용하여 파일을 업로드할 수 있습니다. IoT Hub는 파일을 업로드하기 위해 스토리지에 대한 SAS URI를 요청하는 특히 디바이스에 대한 엔드포인트를 포함합니다. 파일 업로드 프로세스를 시작하기 위해 디바이스에서 다음 JSON 본문이 있는 POST 요청을 `{iot hub}.azure-devices.net/devices/{deviceId}/files`로 보냅니다.

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub는 파일을 업로드하기 위해 디바이스에서 사용할 다음 데이터를 반환합니다.

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>사용되지 않음: GET으로 파일 업로드 초기화

> [!NOTE]
> 이 섹션에서는 IoT Hub에서 SAS URI를 수신하는 방법으로 더 이상 사용되지 않는 기능을 설명합니다. 앞에서 설명한 POST 메서드를 사용합니다.

IoT Hub는 파일 업로드를 지원하는 두 개의 REST 엔드포인트를 가집니다. 하나는 스토리지에 대한 SAS URI를 가져오기 위한 것이고 다른 하나는 IoT Hub에 업로드 완료를 알리기 위한 것입니다. 디바이스에서 `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`의 IoT Hub에 GET을 보내 파일 업로드 프로세스를 시작합니다. IoT Hub는 다음을 반환합니다.

* 업로드되는 파일에 해당하는 SAS URI.

* 업로드가 완료되면 사용될 상관 관계 ID.

## <a name="notify-iot-hub-of-a-completed-file-upload-rest"></a>IoT Hub에 완료된 파일 업로드 알림(REST)

디바이스는 Azure Storage SDK를 사용하여 스토리지에 파일을 업로드합니다. 업로드가 완료되면 디바이스는 다음 JSON 본문으로 `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications`에 POST 요청을 전송합니다.

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` 값은 파일이 성공적으로 업로드되었는지 여부를 나타내는 부울입니다. `statusCode`에 대한 상태 코드는 파일을 스토리지에 업로드하기 위한 상태이고 `statusDescription`은 `statusCode`에 해당합니다.

## <a name="reference-topics"></a>참조 항목:

다음 참조 항목에서는 디바이스에서 파일 업로드에 대한 자세한 정보를 제공합니다.

### <a name="file-upload-notifications"></a>파일 업로드 알림

필요에 따라 디바이스에서 업로드가 완료되었음을 IoT Hub에 알리면 IoT Hub에서 알림 메시지를 생성합니다. 이 메시지에는 파일의 이름과 스토리지 위치가 포함되어 있습니다.

[엔드포인트](iot-hub-devguide-endpoints.md)에 설명된 대로 IoT Hub는 서비스 연결 엔드포인트(**/messages/servicebound/fileuploadnotifications**)를 통해 파일 업로드 알림을 메시지로 전달합니다. 파일 업로드 알림에 대한 수신 의미 체계는 클라우드-디바이스 메시지의 경우와 동일하며 동일한 [메시지 수명 주기](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)를 갖습니다. 파일 업로드 알림 엔드포인트에서 검색된 각 메시지는 다음 속성을 가진 JSON 레코드입니다.

| 속성 | Description |
| --- | --- |
| EnqueuedTimeUtc |알림을 만든 시간을 나타내는 타임스탬프입니다. |
| DeviceId |**DeviceId** 입니다. |
| BlobUri |업로드된 파일의 URI입니다. |
| BlobName |업로드된 파일의 이름입니다. |
| LastUpdatedTime |파일이 마지막으로 업데이트된 시간을 나타내는 타임스탬프입니다. |
| BlobSizeInBytes |업로드된 파일의 크기입니다. |

**예제**. 이 예제는 파일 업로드 알림 메시지의 본문을 보여 줍니다.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

### <a name="file-upload-notification-configuration-options"></a>파일 업로드 알림 구성 옵션

각 IoT 허브에는 파일 업로드 알림에 대한 다음과 같은 구성 옵션이 있습니다.

| 속성 | Description | 범위 및 기본값 |
| --- | --- | --- |
| **enableFileUploadNotifications** |파일 업로드 알림이 파일 알림 엔드포인트에 작성되는지를 제어합니다. |Bool. 기본값은 True입니다. |
| **fileNotifications.ttlAsIso8601** |파일 업로드 알림에 대한 기본 TTL입니다. |최대 48H(최소 1 분)까지 ISO_8601 간격입니다. 기본값은 1시간입니다. |
| **fileNotifications.lockDuration** |파일 업로드 알림 큐에 대한 잠금 기간입니다. |5에서 300초(최소 5초)입니다. 기본값은 60초입니다. |
| **fileNotifications.maxDeliveryCount** |파일 업로드 알림 큐에 대한 최대 배달 횟수입니다. |1에서 100까지입니다. 기본값은 100입니다. |

Azure Portal, Azure CLI 또는 PowerShell을 사용하여 IoT 허브에서 이러한 속성을 설정할 수 있습니다. 방법을 알아보려면 [파일 업로드 구성](iot-hub-configure-file-upload.md) 아래의 항목을 참조하세요.

## <a name="additional-reference-material"></a>추가 참조 자료

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 엔트포인트](iot-hub-devguide-endpoints.md)는 런타임 및 관리 작업에 대한 다양한 IoT Hub 엔트포인트를 설명합니다.

* [제한 및 할당량](iot-hub-devguide-quotas-throttling.md)은 IoT Hub 서비스에 적용되는 할당량과 제한 동작에 대해 설명합니다.

* [Azure IoT 디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)는 IoT Hub와 상호 작용하는 디바이스 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.

* [IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)는 IoT Hub에서 디바이스 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 쿼리 언어에 대해 설명합니다.

* [IoT Hub MQTT 지원](iot-hub-mqtt-support.md)은 MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

IoT Hub를 사용하여 디바이스에서 파일을 업로드하는 방법에 대해 알아보았으므로 다음 IoT Hub 개발자 가이드 항목에 관심이 있을 수 있습니다.

* [IoT Hub에서 디바이스 ID 관리](iot-hub-devguide-identity-registry.md)

* [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)

* [디바이스 쌍을 사용하여 상태 및 구성 동기화](iot-hub-devguide-device-twins.md)

* [디바이스에서 직접 메서드 호출](iot-hub-devguide-direct-methods.md)

* [여러 디바이스에서 작업 예약](iot-hub-devguide-jobs.md)

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 참조하세요.

* [IoT Hub를 사용하여 디바이스에서 클라우드로 파일을 업로드하는 방법](iot-hub-csharp-csharp-file-upload.md)
