---
title: Azure PowerShell을 사용하여 파일 업로드 구성 | Microsoft Docs
description: Azure PowerShell cmdlet을 사용하여 연결된 디바이스에서 파일 업로드를 사용하도록 IoT Hub를 구성하는 방법입니다. 대상 Azure Storage 계정 구성에 대한 정보가 포함됩니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318463"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Azure PowerShell을 사용하여 IoT Hub 파일 업로드 구성

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하려면 먼저 Azure 저장소 계정을 IoT Hub에 연결해야 합니다. 기존 저장소 계정을 사용하거나 새 저장소 계정을 만들 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Azure IoT Hub - IoT hub가 없는 경우 사용할 수 있습니다는 [새로 만들기-AzIoTHub cmdlet](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) 만들거나 포털을 사용 하려면 [IoT hub 만들기](iot-hub-create-through-portal.md)합니다.

* Azure 저장소 계정. Azure 스토리지 계정이 없는 경우 [Azure Storage PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.storage/)을 사용하여 스토리지 계정을 만들거나, 포털을 사용하여 [스토리지 계정을 만들](../storage/common/storage-create-storage-account.md) 수 있습니다.

## <a name="sign-in-and-set-your-azure-account"></a>Azure 계정 로그인 및 설정

Azure 계정에 로그인하고 구독을 선택합니다.

1. PowerShell 프롬프트에서 실행 합니다 **Connect AzAccount** cmdlet:

    ```powershell
    Connect-AzAccount
    ```

2. Azure 구독이 여러 개 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 사용할 수 있는 Azure 구독을 나열합니다.

    ```powershell
    Get-AzSubscription
    ```

    다음 명령을 사용하여 IoT Hub를 관리하는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>저장소 계정 세부 정보 검색

다음 단계에서는 **클래식** 배포 모델이 아니라 **Resource Manager** 배포 모델을 사용하여 저장소 계정을 만들었다고 가정합니다.

디바이스에서 파일 업로드를 구성하려면 Azure 저장소 계정에 대한 연결 문자열이 필요합니다. 저장소 계정은 IoT Hub와 동일한 구독 내에 있어야 합니다. 또한 저장소 계정에 Blob 컨테이너의 이름도 필요합니다. 다음 명령을 사용하여 저장소 계정 키를 검색합니다.

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**key1** 저장소 계정 키 값을 기록해 둡니다. 다음 단계에서 필요합니다.

파일 업로드에 기존 Blob 컨테이너를 사용하거나 새 Blob 컨테이너를 만들 수 있습니다.

* 저장소 계정의 기존 Blob 컨테이너를 나열하려면 다음 명령을 사용합니다.

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* 저장소 계정에 Blob 컨테이너를 만들려면 다음 명령을 사용합니다.

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub 구성

이제 저장소 계정 세부 정보를 사용하여 [IoT Hub에 파일을 업로드](iot-hub-devguide-file-upload.md)하도록 IoT Hub를 구성할 수 있습니다.

구성에는 다음 값이 필요합니다.

* **스토리지 컨테이너**: 현재 Azure 구독에 있는 Azure Storage 계정의 Blob 컨테이너로 IoT Hub와 연결됩니다. 이전 섹션에서 필요한 저장소 계정 정보를 검색했습니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다.

* **업로드된 파일에 대한 알림 받기**: 파일 업로드 알림을 사용하거나 사용하지 않도록 설정합니다.

* **SAS TTL**: 이 설정은 IoT Hub에서 디바이스로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정합니다.

* **파일 알림 설정 기본 TTL**: 만료되기 전의 파일 업로드 알림 TTL입니다. 기본적으로 1일로 설정합니다.

* **파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정합니다.

다음 PowerShell cmdlet을 사용하여 IoT Hub의 파일 업로드 설정을 구성합니다.

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>다음 단계

IoT Hub의 파일 업로드 기능에 대한 자세한 내용은 [디바이스에서 파일 업로드](iot-hub-devguide-file-upload.md)를 참조하세요.

Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT 디바이스 대량 관리](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 메트릭](iot-hub-metrics.md)
* [작업 모니터링](iot-hub-operations-monitoring.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
* [처음부터 IoT 솔루션 보안 유지](../iot-fundamentals/iot-security-ground-up.md)
