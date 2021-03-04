---
title: IoT Hub 업데이트 매니페스트에 대 한 장치 업데이트 | Microsoft Docs
description: 업데이트를 수행 하는 동안 장치 업데이트 서비스에서 장치로 속성을 보내는 방법에 대해 알아봅니다.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030684"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>IoT Hub 업데이트 매니페스트에 대 한 장치 업데이트

## <a name="overview"></a>개요

IoT Hub에 대 한 장치 업데이트는 _업데이트 매니페스트_ 를 사용 하 여 작업을 전달 하 고 [OrchestratorMetadata: 4](./device-update-plug-and-play.md)인터페이스 속성을 통해 해당 작업을 지 원하는 메타 데이터를 전달 합니다.
이 문서에서는 `updateManifest` `AzureDeviceUpdateCore.OrchestratorMetadata:4` 장치 업데이트 에이전트에서 인터페이스의 속성을 사용 하는 방법에 대 한 기본 사항을 설명 합니다. `AzureDeviceUpdateCore.OrchestratorMetadata:4`인터페이스 속성은 IoT Hub 서비스에 대 한 장치 업데이트에서 장치 업데이트 에이전트로 전송 됩니다. 는 `updateManifest` 장치 업데이트 에이전트에서 구문 분석 하는 직렬화 된 JSON 개체입니다.

### <a name="an-example-update-manifest"></a>예제 업데이트 매니페스트

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

업데이트 매니페스트의 목적은 업데이트 내용, 해당 id, 유형, 설치 된 조건 및 업데이트 파일 메타 데이터를 설명 하는 것입니다. 또한 업데이트 매니페스트는 장치 업데이트 에이전트에서 해당 신뢰성을 확인할 수 있도록 암호화 서명 되어 있습니다. 업데이트 매니페스트를 사용 하 여 콘텐츠를 안전 하 게 가져오는 방법에 대 한 자세한 내용은 [장치 업데이트 보안](./device-update-security.md) 설명서를 참조 하세요.

## <a name="import-manifest-vs-update-manifest"></a>매니페스트 및 업데이트 매니페스트 가져오기

IoT Hub에 대 한 장치 업데이트에서 가져오기 매니페스트와 업데이트 매니페스트 개념 간의 차이점을 이해 하는 것이 중요 합니다. 
* [가져오기 매니페스트](./import-concepts.md) 는 해당 업데이트를 만드는 사람에 의해 만들어집니다. IoT Hub에 대 한 장치 업데이트로 가져올 업데이트의 콘텐츠를 설명 합니다. 
* 업데이트 매니페스트는 가져오기 매니페스트에 정의 된 일부 속성을 사용 하 여 IoT Hub 서비스에 대 한 장치 업데이트에 의해 자동으로 생성 됩니다. 업데이트 프로세스 중에 장치 업데이트 에이전트와 관련 정보를 전달 하는 데 사용 됩니다. 

각 매니페스트 형식에는 자체 스키마 및 스키마 버전이 있습니다.

## <a name="update-manifest-properties"></a>매니페스트 속성 업데이트

업데이트 매니페스트 속성의 상위 수준 정의는 [여기](./device-update-plug-and-play.md)에 나와 있는 인터페이스 정의에서 찾을 수 있습니다. 보다 심층적인 컨텍스트를 제공 하기 위해 속성 및 시스템에서 사용 하는 방법을 자세히 살펴보겠습니다.

### <a name="updateid"></a>updateId

`provider` `name` `version` 업데이트에 대해 호환 되는 장치를 확인 하는 데 사용 되는 IoT Hub 업데이트 id에 대 한 정확한 장치 업데이트를 나타내는, 및를 포함 합니다.

### <a name="updatetype"></a>updateType

특정 형식의 업데이트 처리기에서 처리 되는 업데이트 형식을 나타냅니다. `microsoft/swupdate:1`이미지 기반 업데이트 및 `microsoft/apt:1` 패키지 기반 업데이트 (아래 섹션 참조)의 형식을 따릅니다 `Update Handler Types` .

### <a name="installedcriteria"></a>installedCriteria

장치에 업데이트가 설치 되어 있는지 여부를 확인 하기 위해 장치 업데이트 에이전트의 업데이트 처리기에 필요한 정보가 포함 된 문자열입니다. `Update Handler Types`이 섹션에서는 `installedCriteria` IoT Hub에 대 한 장치 업데이트에서 지원 되는 각 업데이트 유형에 대 한의 형식을 설명 합니다.

### <a name="files"></a>파일

장치 업데이트 에이전트에 다운로드할 파일 및 파일을 제대로 다운로드 했는지 확인 하는 데 사용 하는 해시를 알려 줍니다.
다음은 속성 내용을 자세히 확인 하는 것입니다 `files` .

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

외부은 `updateManifest` `fileUrls` JSON 개체의 배열입니다.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

에서, `FILE_ID_STRING` 및는 모두 `fileUrls` `files` 동일 합니다. 예를 들어의 "0000"은의 `files` "0000"에 url이 있습니다 `fileUrls` .

### <a name="manifestversion"></a>manifestVersion

스키마 버전을 나타내는 문자열입니다.

## <a name="update-handler-types"></a>업데이트 처리기 형식

|Update 메서드|업데이트 처리기 형식|업데이트 유형|설치 된 조건|게시에 필요한 파일|
|-------------|-------------------|----------|-----------------|--------------|
|이미지 기반|SWUpdate|"microsoft/swupdate: 버전"|참조 이미지는 해당 버전의 힌트를/etc/adu-version 파일에 저장 합니다.  |Swu 이미지를 포함 하는 swu 파일|
|패키지 기반|APT|"microsoft/apt: version"|`<name>` + "-" + `<version>` (APT 매니페스트 파일의 정의 된 속성)|`<APT Update Manifest>`APT 구성 및 패키지 목록을 포함 하는 json입니다.|

