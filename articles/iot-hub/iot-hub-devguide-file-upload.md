---
title: "Azure IoT Hub 파일 업로드 이해 | Microsoft Docs"
description: "개발자 가이드 - IoT Hub의 파일 업로드 기능을 사용하여 장치에서 Azure Storage blob 컨테이너로 파일 업로드를 관리합니다."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: a2da505166f429b2f6a9823f64499a848d6e0153


---
# <a name="file-uploads-with-iot-hub"></a>IoT Hub로 파일 업로드
## <a name="overview"></a>개요
[IoT Hub 끝점][lnk-endpoints] 문서에서 설명한 대로 장치는 장치 지향 끝점(**/devices/{deviceId}/files**)을 통해 알림을 전송하여 파일 업로드를 시작할 수 있습니다.  장치가 IoT Hub에 완료된 업로드를 알리면 IoT Hub는 메시지로 서비스 연결 끝점을 통해 받을 수 있는 파일 업로드 알림을 생성합니다(**/messages/servicebound/filenotifications**).

IoT Hub 자체를 통한 브로커 메시지 대신 IoT Hub는 연결된 Azure 저장소 계정에 대한 디스패처로 작동합니다. 장치는 장치가 업로드하려는 파일에 특정된 IoT Hub의 저장소 토큰을 요청합니다. 장치는 SAS URI를 사용하여 저장소에 파일을 업로드하고 업로드가 완료되면 장치는 IoT Hub에 완료 알림을 보냅니다. IoT Hub는 파일이 업로드되었는지 확인한 다음 새 서비스 연결 파일 알림 메시징 끝점에 파일 업로드 알림을 추가합니다.

장치에서 IoT Hub로 파일을 업로드하려면 먼저 [Azure Storage 계정을 연결][lnk-associate-storage]하여 허브를 구성해야 합니다.

그런 다음 장치에서 [업로드를 초기화][lnk-initialize]한 후 업로드가 완료되면 [IoT Hub에 알릴][lnk-notify] 수 있습니다. 필요에 따라 장치에서 IoT Hub에 업로드가 완료되었음을 알리면 서비스에서 [알림 메시지][lnk-service-notification]를 생성할 수 있습니다.

### <a name="when-to-use"></a>사용하는 경우
파일 업로드를 사용하여 간헐적으로 연결된 장치로 업로드되거나 대역폭을 절약하기 위해 압축된 미디어 파일과 대형 원격 분석 배치를 보냅니다.

reported 속성, 장치-클라우드 메시지 또는 파일 업로드 사용에 대해 궁금한 점이 있으면 [장치-클라우드 통신 지침][lnk-d2c-guidance]을 참조하세요.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage 계정을 IoT Hub와 연결
파일 업로드 기능을 사용하려면 먼저 Azure 저장소 계정을 IoT Hub에 연결해야 합니다. [Azure Portal][lnk-management-portal]을 통하거나 [IoT Hub 리소스 공급자 REST API][lnk-resource-provider-apis]를 통해 프로그래밍 방식으로 이 작업을 수행할 수 있습니다. Azure Storage 계정을 IoT Hub와 연결하면 서비스는 장치가 파일 업로드 요청을 시작할 때 장치에 SAS URI를 반환합니다.

> [!NOTE]
> [Azure IoT SDK][lnk-sdks]는 SAS URI 검색, 파일 업로드 및 IoT Hub에 완료된 업로드 알림을 자동으로 처리합니다.
> 
> 

## <a name="initialize-a-file-upload"></a>파일 업로드 초기화
IoT Hub는 파일을 업로드하기 위해 저장소에 대한 SAS URI를 요청하는 특히 장치에 대한 끝점을 포함합니다. 장치는 `{iot hub}.azure-devices.net/devices/{deviceId}/files`에서 다음 JSON 본문으로 IoT Hub에 POST를 전송하여 파일 업로드 프로세스를 시작합니다.

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub는 파일을 업로드하기 위해 장치에서 사용할 다음 내용을 반환합니다.

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>사용되지 않음: GET으로 파일 업로드 초기화
> [!NOTE]
> 이 섹션에서는 IoT Hub에서 SAS URI를 수신하는 방법으로 더 이상 사용되지 않는 기능을 설명합니다. 위에 설명된 POST 메서드를 사용하세요.
> 
> 

IoT Hub는 파일 업로드를 지원하는 두 개의 REST 끝점을 가집니다. 하나는 저장소에 대한 SAS URI를 가져오기 위한 것이고 다른 하나는 IoT Hub에 업로드 완료를 알리기 위한 것입니다. 장치는 `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`에서 IoT Hub에 GET을 전송하여 파일 업로드 프로세스를 시작합니다. 업로드가 완료되면 IoT Hub에서 업로드할 파일에 해당하는 SAS URI와 사용할 상관관계 ID를 반환합니다.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>IoT Hub에 완료된 파일 업로드 알림
장치는 Azure 저장소 SDK를 사용하여 저장소에 파일을 업로드하는 일을 담당합니다. 업로드가 완료되면 장치는 `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications`에서 다음 JSON 본문으로 IoT Hub에 POST를 전송합니다.

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` 값은 Boolean이며 파일이 성공적으로 업로드되었는지 여부를 나타냅니다. `statusCode`에 대한 상태 코드는 파일을 저장소에 업로드하기 위한 상태이고 `statusDescription`은 `statusCode`에 해당합니다.

## <a name="reference-topics"></a>참조 항목:
다음 참조 항목에서는 장치에서 파일 업로드에 대한 자세한 정보를 제공합니다.

## <a name="file-upload-notifications"></a>파일 업로드 알림
장치가 파일을 업로드하고 IoT Hub에 업로드 완료를 알리는 경우 서비스는 필요에 따라 파일의 이름 및 저장소 위치를 포함하는 알림 메시지를 생성합니다.

[끝점][lnk-endpoints]에서 설명한 대로 IoT Hub는 서비스 지향 끝점(**/messages/servicebound/fileuploadnotifications**)을 통해 파일 업로드 알림을 메시지로 전달합니다. 파일 업로드 알림에 대한 수신 의미 체계는 클라우드-장치 메시지의 경우와 동일하며 동일한 [메시지 수명 주기][lnk-lifecycle]를 갖습니다. 파일 업로드 알림 끝점에서 검색된 각 메시지는 다음 속성을 가진 JSON 레코드입니다.

| 속성 | 설명 |
| --- | --- |
| EnqueuedTimeUtc |알림을 만든 시간을 나타내는 타임스탬프입니다. |
| deviceId |**DeviceId** 입니다. |
| BlobUri |업로드된 파일의 URI입니다. |
| BlobName |업로드된 파일의 이름입니다. |
| LastUpdatedTime |파일이 마지막으로 업데이트된 시간을 나타내는 타임스탬프입니다. |
| BlobSizeInBytes |업로드된 파일의 크기입니다. |

**예제**. 파일 업로드 알림 메시지의 예제 본문입니다.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>파일 업로드 알림 구성 옵션
각 IoT Hub는 파일 업로드 알림에 다음 구성 옵션을 노출합니다.

| 속성 | 설명 | 범위 및 기본값 |
| --- | --- | --- |
| **enableFileUploadNotifications** |파일 업로드 알림이 파일 알림 끝점에 작성되는지를 제어합니다. |Bool. 기본값은 True입니다. |
| **fileNotifications.ttlAsIso8601** |파일 업로드 알림에 대한 기본 TTL입니다. |최대 48H(최소 1 분)까지 ISO_8601 간격입니다. 기본값은 1시간입니다. |
| **fileNotifications.lockDuration** |파일 업로드 알림 큐에 대한 잠금 기간입니다. |5에서 300초(최소 5초)입니다. 기본값은 60초입니다. |
| **fileNotifications.maxDeliveryCount** |파일 업로드 알림 큐에 대한 최대 배달 횟수입니다. |1에서 100까지입니다. 기본값은 100입니다. |

## <a name="additional-reference-material"></a>추가 참조 자료
이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-endpoints] - 각 IoT Hub에서 런타임 및 관리 작업에 대해 공개하는 다양한 끝점에 대해 설명합니다.
* [제한 및 할당량][lnk-quotas] - IoT Hub 서비스에 적용되는 할당량과 서비스를 사용할 때 예상되는 제한 동작에 대해 설명합니다.
* [Azure IoT 장치 및 서비스 SDK][lnk-sdks] - IoT Hub와 상호 작용하는 장치 및 서비스 앱 모두를 개발할 때 사용하는 다양한 언어 SDK를 나열합니다.
* [장치 쌍 및 작업을 위한 IoT Hub 쿼리 언어][lnk-query] - IoT Hub에서 장치 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 IoT Hub 쿼리 언어에 대해 설명합니다.
* [IoT Hub MQTT 지원][lnk-devguide-mqtt] - MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계
IoT Hub를 사용하여 장치에서 파일을 업로드하는 방법에 대해 알아봤으니 다음 IoT Hub 개발자 가이드 항목을 살펴보세요.

* [IoT Hub에서 장치 ID 관리][lnk-devguide-identities]
* [IoT Hub에 대한 액세스 제어][lnk-devguide-security]
* [장치 쌍을 사용하여 상태 및 구성 동기화][lnk-devguide-device-twins]
* [장치에서 직접 메서드 호출][lnk-devguide-directmethods]
* [여러 장치에서 jobs 예약][lnk-devguide-jobs]

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 살펴보세요.

* [IoT Hub를 사용하여 장치에서 클라우드로 파일을 업로드하는 방법][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md



<!--HONumber=Dec16_HO1-->


