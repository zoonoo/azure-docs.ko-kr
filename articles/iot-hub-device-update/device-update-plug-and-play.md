---
title: IoT Hub에 대 한 장치 업데이트가 IoT 플러그 앤 플레이를 사용 하는 방법 이해 | Microsoft Docs
description: IoT Hub 용 장치 업데이트는를 사용 하 여 공중파 업데이트를 지 원하는 장치를 검색 하 고 관리 합니다.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 227488f165aaad2f204c647eed17467a4ef561a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663406"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT Hub 및 IoT 플러그 앤 플레이에 대 한 장치 업데이트

IoT Hub 용 장치 업데이트는 [IoT 플러그 앤 플레이](https://docs.microsoft.com/azure/iot-pnp/) 를 사용 하 여 공중파 업데이트를 지 원하는 장치를 검색 하 고 관리 합니다. 장치 업데이트 서비스는 PnP 인터페이스를 사용 하 여 장치에서 속성과 메시지를 보내고 받습니다. IoT Hub에 대 한 장치 업데이트에는 아래에 설명 된 대로 IoT 장치가 다음과 같은 인터페이스와 모델 id를 구현 해야 합니다.

## <a name="adu-core-interface"></a>ADU Core 인터페이스

' ADUCoreInterface ' 인터페이스는 업데이트 작업 및 메타 데이터를 장치로 보내고 장치에서 업데이트 상태를 수신 하는 데 사용 됩니다. ' ADU Core ' 인터페이스는 두 개의 개체 속성으로 분할 됩니다.

모델에 필요한 구성 요소 이름은이 인터페이스를 구현할 때 **"Azuredeviceupdateagent"** 입니다. [Azure IoT PnP 구성 요소에 대 한 자세한 정보](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

### <a name="agent-metadata"></a>에이전트 메타 데이터

에이전트 메타 데이터에는 장치 또는 장치 업데이트 에이전트가 장치 업데이트 서비스에 정보 및 상태를 전송 하는 데 사용 하는 필드가 포함 되어 있습니다.

|Name|스키마|Direction|설명|예제|
|----|------|---------|-----------|-----------|
|resultCode|정수|장치-클라우드|마지막 업데이트 작업의 결과에 대 한 정보를 포함 하는 코드입니다. 성공 또는 실패에 대해 채워질 수 있으며 [http 상태 코드 사양을](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)따라야 합니다.|500|
|extendedResultCode|정수|장치-클라우드|결과에 대 한 추가 정보를 포함 하는 코드입니다. 성공 또는 실패에 대해 채울 수 있습니다.|0x80004005|
|state|정수|장치-클라우드|장치 업데이트 에이전트의 현재 상태를 나타내는 정수입니다. 자세한 내용은 다음을 참조하세요. |유휴 상태|
|installedUpdateId|문자열|장치-클라우드|현재 설치 되어 있는 업데이트 (장치 업데이트를 통해)의 ID입니다. 장치 업데이트를 통해 업데이트를 수행 하지 않은 장치에 대해서는이 값이 null이 됩니다.|Null|
|`deviceProperties`|지도|장치-클라우드|제조업체 및 모델을 포함 하는 속성 집합입니다.|자세한 내용은 다음을 참조하세요.

#### <a name="state"></a>시스템 상태

장치 업데이트 서비스에서 작업을 받은 후 장치 업데이트 에이전트에서 보고 한 상태입니다. `State` 는 `Action` `Actions` 장치 업데이트 서비스에서 장치 업데이트 에이전트로 전송 된 (아래 참조)에 대 한 응답으로 보고 됩니다. 장치 업데이트 서비스와 장치 업데이트 에이전트 간에 흐르는 요청에 대해서는 [개요 워크플로](understand-device-update.md#device-update-agent) 를 참조 하세요.

|Name|값|설명|
|---------|-----|-----------|
|유휴 상태|0|장치가 장치 업데이트 서비스에서 작업을 받을 준비가 되었습니다. 업데이트에 성공 하면 상태가 `Idle` 상태로 돌아갑니다.|
|다운로드 성공|2|다운로드가 완료 되었습니다.|
|InstallSucceeded|4|설치가 완료 되었습니다.|
|실패|255|업데이트 하는 동안 오류가 발생 했습니다.|

#### <a name="device-properties"></a>디바이스 속성

제조업체 및 모델을 포함 하는 속성 집합입니다.

|Name|스키마|Direction|설명|
|----|------|---------|-----------|
|제조업체|문자열|장치-클라우드|를 통해 보고 된 장치의 장치 제조업체 `deviceProperties` 입니다. 이 속성은 두 위치 중 하나에서 읽습니다. ' AzureDeviceUpdateCore ' 인터페이스는 먼저 [구성 파일](device-update-configuration-file.md) 파일에서 ' aduc_manufacturer ' 값을 읽으려고 시도 합니다.  구성 파일에 값이 채워지지 않은 경우 기본적으로 ADUC_DEVICEPROPERTIES_MANUFACTURER에 대 한 컴파일 시간 정의를 보고 합니다. 이 속성은 부팅 시에만 보고 됩니다.|
|model|문자열|장치-클라우드|장치에서 보고 된 장치 모델 `deviceProperties` 입니다. 이 속성은 두 위치 중 하나에서 읽습니다. AzureDeviceUpdateCore 인터페이스는 먼저 [구성 파일](device-update-configuration-file.md) 파일에서 ' aduc_model ' 값을 읽으려고 시도 합니다.  구성 파일에 값이 채워지지 않은 경우 기본적으로 ADUC_DEVICEPROPERTIES_MODEL에 대 한 컴파일 시간 정의를 보고 합니다. 이 속성은 부팅 시에만 보고 됩니다.|
|aduVer|문자열|장치-클라우드|장치에서 실행 되는 장치 업데이트 에이전트의 버전입니다. 이 값은 컴파일 시간 동안 ENABLE_ADU_TELEMETRY_REPORTING 1 (true)로 설정 된 경우에만 빌드에서 읽습니다. 고객은 값을 0 (false)으로 설정 하 여 버전 보고를 옵트아웃 (opt out) 하도록 선택할 수 있습니다. [장치 업데이트 에이전트 속성을 사용자 지정 하는 방법](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|doVer|문자열|장치-클라우드|장치에서 실행 되는 배달 최적화 에이전트의 버전입니다. 컴파일 시간 동안 ENABLE_ADU_TELEMETRY_REPORTING 1 (true)로 설정 된 경우에만 빌드에서 값을 읽습니다. 고객은 값을 0 (false)으로 설정 하 여 버전 보고를 옵트아웃 (opt out) 하도록 선택할 수 있습니다. [배달 최적화 에이전트 속성을 사용자 지정 하는 방법](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>서비스 메타데이터

서비스 메타 데이터에는 장치 업데이트 에이전트에 작업 및 데이터를 전달 하기 위해 장치 업데이트 서비스에서 사용 하는 필드가 포함 되어 있습니다.

|Name|스키마|Direction|설명|
|----|------|---------|-----------|
|작업|정수|클라우드-장치|에이전트에서 수행 해야 하는 작업에 해당 하는 정수입니다. 값은 다음과 같습니다.|
|updateManifest|문자열|클라우드-장치|업데이트의 콘텐츠를 설명 하는 데 사용 됩니다. [가져오기 매니페스트에서](import-update.md#create-device-update-import-manifest) 생성 됨|
|updateManifestSignature|JSON 개체|클라우드-장치|원본 확인에 사용 되는 JSON 웹 키가 있는 JWS (JSON 웹 서명)입니다.|
|fileUrls|지도|클라우드-장치|에 매핑합니다 `FileHash` `DownloadUri` . 는 다운로드할 파일과 파일이 올바르게 다운로드 되었는지 확인 하는 데 사용할 해시를 에이전트에 알려 줍니다.|

#### <a name="action"></a>작업

`Actions` 다음은 장치 업데이트 서비스의 지시에 따라 장치 업데이트 에이전트에서 수행 하는 작업을 나타냅니다. 장치 업데이트 에이전트는 `State` 수신 된를 처리 하는 (위 섹션 참조)를 보고 합니다 `State` `Action` . 장치 업데이트 서비스와 장치 업데이트 에이전트 간에 흐르는 요청에 대해서는 [개요 워크플로](understand-device-update.md#device-update-agent) 를 참조 하세요.

|Name|값|설명|
|---------|-----|-----------|
|다운로드|0|게시 된 콘텐츠 또는 업데이트 및 필요한 기타 콘텐츠 다운로드|
|설치|1|콘텐츠 또는 업데이트를 설치 합니다. 일반적으로이는 설치 관리자가 콘텐츠나 업데이트를 호출 하는 것을 의미 합니다.|
|적용|2|업데이트를 완료 합니다. 필요한 경우 시스템을 다시 부팅 하도록 신호를 보냅니다.|
|취소|255|현재 작업 처리를 중지 하 고로 돌아갑니다 `Idle` . 를 사용 하 여 에이전트를로 다시 돌아갈 수 있습니다 `Failed` `Idle` .|

## <a name="device-information-interface"></a>장치 정보 인터페이스

장치 정보 인터페이스는 [IoT 플러그 앤 플레이 아키텍처](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)내에서 사용 되는 개념입니다. 장치의 하드웨어 및 운영 체제에 대 한 정보를 제공 하는 장치-클라우드 속성을 포함 합니다. IoT Hub에 대 한 장치 업데이트는 DeviceInformation. 제조업체 및 DeviceInformation. 원격 분석 및 진단에 대 한 모델 속성을 사용 합니다. 장치 정보 인터페이스에 대 한 자세한 내용은이 [예제](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)를 참조 하세요.

모델에 필요한 구성 요소 이름은이 인터페이스를 구현할 때 **Deviceinformation** 입니다. [Azure IoT PnP 구성 요소에 대 한 자세한 정보](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

|Name|Type|스키마|Direction|설명|예제|
|----|----|------|---------|-----------|-----------|
|제조업체|속성|문자열|장치-클라우드|장치 제조업체의 회사 이름입니다. OEM (원본 장비 제조업체)의 이름과 같을 수 있습니다.|Contoso|
|model|속성|문자열|장치-클라우드|장치 모델 이름 또는 ID입니다.|IoT Edge 장치|
|swVersion|속성|문자열|장치-클라우드|장치에 있는 소프트웨어 버전입니다. swVersion은 펌웨어의 버전일 수 있습니다.|4.15.0-122|
|osName|속성|문자열|장치-클라우드|장치에 있는 운영 체제의 이름입니다.|Ubuntu Server 18.04|
|processorArchitecture|속성|문자열|장치-클라우드|장치의 프로세서 아키텍처입니다.|ARM64|
|processorManufacturer|속성|문자열|장치-클라우드|장치에 있는 프로세서 제조업체의 이름입니다.|Microsoft|
|totalStorage|속성|문자열|장치-클라우드|장치에서 사용 가능한 총 저장소 (킬로바이트)입니다.|2048|
|totalMemory|속성|문자열|장치-클라우드|장치에서 사용 가능한 총 메모리 (kb)입니다.|256|

## <a name="model-id"></a>모델 ID 

모델 ID는 스마트 장치가 IoT 플러그인을 사용 하 여 Azure IoT 응용 프로그램에 해당 기능을 보급 하는 방법입니다. Play.To Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드하는 방법에 대해 자세히 알아보세요. [iot 플러그 앤 플레이 장치 개발자 가이드](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c)

IoT Hub에 대 한 장치 업데이트에는 IoT 플러그 앤 플레이 스마트 장치가 장치 연결의 일부로 **"dtmi: AzureDeviceUpdate; 1"** 값을 가진 모델 ID를 알리기 위해 필요 합니다. [모델 ID를 발표 하는 방법에 대해 알아봅니다](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c#model-id-announcement).
