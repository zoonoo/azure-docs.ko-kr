---
title: Azure IoT Hub에 대한 디바이스 업데이트의 클라이언트 오류 코드 | Microsoft Docs
description: 이 문서에서는 다양한 디바이스 업데이트 구성 요소에 대한 클라이언트 오류 코드 표를 제공합니다.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200349"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Device Update for IoT Hub 오류 코드

이 문서에서는 다양한 디바이스 업데이트 구성 요소에 대한 오류 코드 표를 제공합니다. 이는 문제를 진단하고 해결하기 위해 자체 오류 코드를 구문 분석하려는 사용자에 대한 참조로 사용됩니다.

오류 코드를 발생시킬 수 있는 두 가지 기본 클라이언트 쪽 구성 요소(디바이스 업데이트 에이전트 및 배달 최적화 에이전트)가 있습니다.

## <a name="device-update-agent"></a>디바이스 업데이트 에이전트

### <a name="resultcode-and-extendedresultcode"></a>ResultCode 및 ExtendedResultCode

Device Update for IoT Hub Core PnP 인터페이스는 실패를 진단하는 데 사용할 수 있는 `ResultCode` 및 `ExtendedResultCode`(을)를 보고합니다. 디바이스 업데이트 Core PnP 인터페이스에 대해 [자세히 알아봅니다](device-update-plug-and-play.md).

#### <a name="resultcode"></a>ResultCode

`ResultCode`은(는) 일반적인 상태 코드이며 http 상태 코드 규칙을 따릅니다.
상태 코드에 대해 [자세히 알아봅니다](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode`은(는) 인코딩된 오류 정보를 포함하는 정수입니다.

`ExtendedResultCode`은(는) PnP 인터페이스에서 부호 있는 정수로 표시될 가능성이 높습니다. `ExtendedResultCode`을(를) 디코딩하려면 부호 있는 정수를 부호 없는 16진수로 변환합니다. `ExtendedResultCode`의 처음 4바이트만 사용되며 `F` `FFFFFFF` 형식입니다. 여기서 첫 번째 니블은 **기능 코드** 이고 나머지 비트는 **오류 코드** 입니다.

**기능 코드**

| 기능 코드     | 설명  |
|-------------------|--------------|
| D                 | DO SDK에서 발생한 오류|
| E                 | 오류 코드는 errno임 |


예를 들면 다음과 같습니다.

`ExtendedResultCode`은 `-536870781`입니다.

`-536870781`의 부호 없는 16진수 표현은 `FFFFFFFF E0000083`입니다.

| 무시    | 기능 코드  | 오류 코드   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

16진수 `0x83`은(는) 10진수로 `131`(이)며 `ENOLCK`에 대한 errno 값입니다.

## <a name="delivery-optimization-agent"></a>배달 최적화 에이전트
다음 표에는 디바이스 업데이트 클라이언트의 DO(배달 최적화) 구성 요소와 관련된 오류 코드가 나열되어 있습니다. DO 구성 요소는 IoT 디바이스에 업데이트 콘텐츠를 다운로드하는 작업을 담당합니다.

API 호출에 대한 응답으로 throw된 예외를 검사하여 DO 오류 코드를 가져올 수 있습니다. 모든 DO 오류 코드는 0x80D0 접두사로 식별할 수 있습니다.

| 오류 코드  | 문자열 오류                       | 유형                 | 설명 |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | OM_E_NO_SERVICE                    | 해당 없음                  | 배달 최적화에서 서비스를 제공할 수 없음 |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | 다운로드 작업         | 배달 최적화: 파일 다운로드가 정의된 기간 내에 진행되지 않았음 |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | 다운로드 작업         | 알 수 없는 속성 ID를 사용하여 호출된 SetProperty() 또는 GetProperty() |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | 다운로드 작업         | 읽기 전용 속성에서 SetProperty()를 호출할 수 없음 |
| 0x80D02013L | DO_E_INVALID_STATE                 | 다운로드 작업         | 요청한 작업은 현재 작업 상태에서 허용되지 않습니다. 작업이 취소되었거나 전송이 완료되었을 수 있습니다. 이제 읽기 전용 상태입니다. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | 다운로드 작업         | 다운로드 싱크(로컬 파일 또는 스트림 인터페이스)를 지정하지 않았으므로 다운로드를 시작할 수 없음 |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload 인터페이스| URI를 제공하지 않고 다운로드 시작됨 |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | 일시적인 장애 | 네트워크 연결 손실로 인해 다운로드 일시 중지됨 |

## <a name="device-update-content-service"></a>디바이스 업데이트 콘텐츠 서비스
다음 표에는 디바이스 업데이트 서비스의 콘텐츠 서비스 구성 요소와 관련된 오류 코드가 나열되어 있습니다. 콘텐츠 서비스 구성 요소는 업데이트 콘텐츠 가져오기 처리를 담당합니다.

| 오류 코드                    | 문자열 오류                                                               | 다음 단계                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | ID가 동일한 업데이트가 이미 있습니다.                              | 아직 이 Device Update for IoT Hub 인스턴스로 가져오지 않은 업데이트를 가져오고 있는지 확인합니다. |
| "DuplicateContentImport"      | 동일한 콘텐츠를 동시에 여러 번 가져왔습니다.                  | UpdateAlreadyExists와 동일합니다. |
| "CannotProcessImportManifest" | 가져오기 매니페스트를 처리하는 동안 오류가 발생했습니다.                                          | 적절한 매니페스트 서식 가져오기에 대한 [개념 가져오기](./import-concepts.md) 및 [업데이트 가져오기](./import-update.md) 설명서를 참조하세요. |
| "CannotDownload"              | 가져오기 매니페스트를 다운로드할 수 없습니다.                                           | 매니페스트 파일 가져오기에 대한 URL이 여전히 유효한지 확인합니다. |
| "CannotParse"                 | 가져오기 매니페스트를 구분 분석할 수 없습니다.                                              | [업데이트 가져오기](./import-update.md) 설명서에 정의된 스키마에 대해 가져오기 매니페스트의 정확성을 확인합니다. |
| "UnsupportedVersion"          | 매니페스트 스키마 버전 가져오기는 지원되지 않습니다.                           | 가져오기 매니페스트가 [업데이트 가져오기](./import-update.md) 설명서에 정의된 최신 스키마를 사용하고 있는지 확인합니다. |
| "UpdateLimitExceeded"         | 한도 초과로 인해 업데이트를 가져오는 동안 오류가 발생했습니다.                              | Device Update for IoT Hub 인스턴스에서 허용되는 서로 다른 공급자, 이름 또는 버전의 수 제한에 도달했습니다. 인스턴스에서 일부 업데이트를 삭제하고 다시 시도하세요. |
| "UpdateProvider"              | 새 업데이트 공급자를 가져올 수 없습니다.                                       | Device Update for IoT Hub 인스턴스에서 허용되는 서로 다른 __공급자__ 의 수 제한에 도달했습니다. 인스턴스에서 일부 업데이트를 삭제하고 다시 시도하세요. |
| "UpdateName"                  | 지정한 공급자에 대한 새 업데이트 이름을 가져올 수 없습니다.                | Device Update for IoT Hub 인스턴스에서 하나의 공급자에 허용되는 서로 다른 __이름__ 의 수 제한에 도달했습니다. 인스턴스에서 일부 업데이트를 삭제하고 다시 시도하세요. |
| "UpdateVersion"               | 지정한 공급자 및 이름에 대한 새 업데이트 버전을 가져올 수 없습니다.    | Device Update for IoT Hub 인스턴스에서 하나의 공급자와 이름에 허용되는 서로 다른 __버전__ 의 수 제한에 도달했습니다. 인스턴스에서 해당 이름을 가진 일부 업데이트를 삭제하고 다시 시도하세요. |
| "UpdateProviderCompatibility" | 지정된 호환성 수준의 추가 업데이트 공급자를 가져올 수 없습니다. | 가져오기 매니페스트에서 디바이스 제조업체 및 디바이스 모델 호환성 속성을 정의하는 경우 Device Update for IoT Hub는 지정된 제조업체/모델에 대한 단일 공급자 및 이름 조합을 지원한다는 점에 유의하세요. 즉, 둘 이상의 공급자/이름 조합으로 동일한 제조업체/모델 호환성 속성을 사용하려고 하면 이러한 오류가 표시됩니다. 이 문제를 해결하려면 지정된 디바이스에 대한 모든 업데이트가 (제조업체/모델에서 정의한 대로) 동일한 공급자와 이름을 사용하는지 확인합니다. 필수는 아니지만 단순화를 위해 공급자와 제조업체를 동일하게, 모델과 이름을 동일하게 만드는 것을 고려할 수 있습니다. |
| "UpdateNameCompatibility"     | 지정된 호환성 수준의 추가 업데이트 이름을 가져올 수 없습니다.     | UpdateProviderCompatibility.ContentLimitNamespaceCompatibility와 동일합니다. |
| "UpdateVersionCompatibility"  | 지정된 호환성 수준의 추가 업데이트 버전을 가져올 수 없습니다.  | UpdateProviderCompatibility.ContentLimitNamespaceCompatibility와 동일합니다. |
| "CannotProcessUpdateFile"     | 원본 파일을 처리하는 동안 오류가 발생했습니다.                                              |                                    |
| "ContentFileCannotDownload"   | 원본 파일을 다운로드할 수 없습니다.                                               | 업데이트 파일의 URL이 여전히 유효한지 확인합니다. |

**[다음 단계: 디바이스 업데이트 관련 문제 해결](.\troubleshoot-device-update.md)**
