---
title: Azure IoT Hub에 대 한 장치 업데이트에 대 한 클라이언트 오류 코드 | Microsoft Docs
description: 이 문서에서는 다양 한 장치 업데이트 구성 요소에 대 한 클라이언트 오류 코드 표를 제공 합니다.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200349"
---
# <a name="device-update-for-iot-hub-error-codes"></a>IoT Hub 오류 코드에 대 한 장치 업데이트

이 문서에서는 다양 한 장치 업데이트 구성 요소에 대 한 오류 코드 표를 제공 합니다. 이는 문제를 진단 하 고 해결 하기 위해 자체 오류 코드를 구문 분석 하려는 사용자에 대 한 참조로 사용 됩니다.

오류 코드를 throw 할 수 있는 두 가지 기본 클라이언트 쪽 구성 요소 (장치 업데이트 에이전트 및 배달 최적화 에이전트)가 있습니다.

## <a name="device-update-agent"></a>장치 업데이트 에이전트

### <a name="resultcode-and-extendedresultcode"></a>ResultCode 및 ExtendedResultCode

IoT Hub Core PnP 인터페이스에 대 한 장치 업데이트 `ResultCode` 는 `ExtendedResultCode` 오류를 진단 하는 데 사용할 수 있는 및를 보고 합니다. 장치 업데이트 코어 PnP 인터페이스에 [대해 자세히 알아보세요](device-update-plug-and-play.md) .

#### <a name="resultcode"></a>ResultCode

`ResultCode` 는 일반적인 상태 코드 이며 http 상태 코드 규칙을 따릅니다.
Http 상태 코드에 [대해 자세히 알아보세요](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` 인코딩된 오류 정보를 포함 하는 정수입니다.

는 대부분 `ExtendedResultCode` PnP 인터페이스에서 부호 있는 정수로 표시 될 가능성이 높습니다. 를 디코딩하는 `ExtendedResultCode` 부호 있는 정수를 부호 없는 16 진수로 변환 합니다. 의 처음 4 바이트만 사용 되며, `ExtendedResultCode` `F` `FFFFFFF` 첫 번째 니블이 **기능 코드** 이 고 나머지 비트는 **오류 코드** 입니다.

**기능 코드**

| 기능 코드     | 설명  |
|-------------------|--------------|
| D                 | DO SDK에서 발생 한 오류|
| E                 | 오류 코드는 errno입니다. |


예를 들면 다음과 같습니다.

`ExtendedResultCode`은 `-536870781`입니다.

의 부호 없는 16 진수 표현은입니다 `-536870781` `FFFFFFFF E0000083` .

| 무시    | 기능 코드  | 오류 코드   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` in hex는 `131` 의 errno 값인 decimal입니다 `ENOLCK` .

## <a name="delivery-optimization-agent"></a>배달 최적화 에이전트
다음 표에는 장치 업데이트 클라이언트의 배달 최적화 (DO) 구성 요소와 관련 된 오류 코드가 나열 되어 있습니다. DO 구성 요소는 IoT 장치에 업데이트 콘텐츠를 다운로드 하는 작업을 담당 합니다.

API 호출에 대 한 응답으로 throw 된 예외를 검사 하 여 DO error 코드를 가져올 수 있습니다. 모든 오류 코드는 0x80D0 접두사로 식별할 수 있습니다.

| 오류 코드  | 문자열 오류                       | Type                 | 설명 |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | 해당 없음                  | 배달 최적화에서 서비스를 제공할 수 없습니다. |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | 다운로드 작업         | 정의 된 기간 내에 진행 되지 않는 파일 다운로드 |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | 다운로드 작업         | 알 수 없는 속성 ID를 사용 하 여 SetProperty () 또는 GetProperty () 호출 |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | 다운로드 작업         | 읽기 전용 속성에서 SetProperty ()를 호출할 수 없습니다. |
| 0x80D02013L | DO_E_INVALID_STATE                 | 다운로드 작업         | 현재 작업 상태에서는 요청 된 작업이 허용 되지 않습니다. 작업이 취소 되었거나 전송이 완료 되었을 수 있습니다. 이제 읽기 전용 상태입니다. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | 다운로드 작업         | 다운로드 싱크 (로컬 파일 또는 스트림 인터페이스)를 지정 하지 않았으므로 다운로드를 시작할 수 없습니다. |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload 인터페이스| URI를 제공 하지 않고 다운로드를 시작 했습니다. |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | 일시적인 장애 | 네트워크 연결 손실로 인해 다운로드 일시 중지 됨 |

## <a name="device-update-content-service"></a>장치 업데이트 콘텐츠 서비스
다음 표에는 장치 업데이트 서비스의 콘텐츠 서비스 구성 요소와 관련 된 오류 코드가 나열 되어 있습니다. 콘텐츠 서비스 구성 요소는 업데이트 콘텐츠 가져오기 처리를 담당 합니다.

| 오류 코드                    | 문자열 오류                                                               | 다음 단계                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | 동일한 id를 가진 업데이트가 이미 있습니다.                              | IoT Hub에 대 한 장치 업데이트 인스턴스로 아직 가져오지 않은 업데이트를 가져오지 않았는지 확인 합니다. |
| "DuplicateContentImport"      | 동일한 콘텐츠를 동시에 여러 번 가져왔습니다.                  | UpdateAlreadyExists와 동일 합니다. |
| "CannotProcessImportManifest" | 가져오기 매니페스트를 처리 하는 동안 오류가 발생 했습니다.                                          | 적절 한 매니페스트 서식 가져오기에 대 한 [개념 가져오기](./import-concepts.md) 및 [업데이트 설명서 가져오기](./import-update.md) 를 참조 하세요. |
| "CannotDownload"              | 가져오기 매니페스트를 다운로드할 수 없습니다.                                           | 매니페스트 파일 가져오기에 대 한 URL이 여전히 유효한 지 확인 합니다. |
| "CannotParse"                 | 가져오기 매니페스트를 구문 분석할 수 없습니다.                                              | 가져오기 [업데이트](./import-update.md) 설명서에 정의 된 스키마에 대해 가져오기 매니페스트를 확인 하 여 정확성을 확인 합니다. |
| "UnsupportedVersion"          | 가져오기 매니페스트 스키마 버전은 지원 되지 않습니다.                           | 가져오기 매니페스트가 [가져오기 업데이트](./import-update.md) 설명서에 정의 된 최신 스키마를 사용 하 고 있는지 확인 합니다. |
| "Update제한을 초과 했습니다."         | 제한 초과로 인해 업데이트를 가져오는 동안 오류가 발생 했습니다.                              | IoT Hub에 대 한 장치 업데이트 인스턴스에서 허용 되는 다양 한 공급자, 이름 또는 버전 수에 대 한 제한에 도달 했습니다. 인스턴스에서 일부 업데이트를 삭제 하 고 다시 시도 하세요. |
| "UpdateProvider"              | 새 업데이트 공급자를 가져올 수 없습니다.                                       | IoT Hub에 대 한 장치 업데이트 인스턴스에서 허용 되는 다양 한 __공급자__ 수에 대 한 제한에 도달 했습니다. 인스턴스에서 일부 업데이트를 삭제 하 고 다시 시도 하세요. |
| "UpdateName"                  | 지정 된 공급자에 대 한 새 업데이트 이름을 가져올 수 없습니다.                | IoT Hub에 대 한 장치 업데이트 인스턴스의 한 공급자에서 허용 되는 다른 __이름__ 수에 대 한 제한에 도달 했습니다. 인스턴스에서 일부 업데이트를 삭제 하 고 다시 시도 하세요. |
| UpdateVersion               | 지정 된 공급자 및 이름에 대 한 새 업데이트 버전을 가져올 수 없습니다.    | IoT Hub에 대 한 장치 업데이트 인스턴스의 한 공급자 및 이름에서 허용 되는 다른 __버전__ 수에 대 한 제한에 도달 했습니다. 인스턴스에서 해당 이름을 가진 일부 업데이트를 삭제 하 고 다시 시도 하세요. |
| "UpdateProviderCompatibility" | 지정 된 호환성이 있는 추가 업데이트 공급자를 가져올 수 없습니다. | 가져오기 매니페스트에서 장치 제조업체 및 장치 모델 호환성 속성을 정의 하는 경우 IoT Hub의 장치 업데이트는 지정 된 제조업체/모델에 대 한 단일 공급자 및 이름 조합을 지원 한다는 점에 유의 하세요. 즉, 둘 이상의 공급자/이름 조합을 사용 하 여 동일한 제조업체/모델 호환성 속성을 사용 하려고 하면 이러한 오류가 표시 됩니다. 이 문제를 해결 하려면 지정 된 장치에 대 한 모든 업데이트 (제조업체/모델에서 정의한 대로)가 동일한 공급자와 이름을 사용 하는지 확인 합니다. 필수는 아니지만 간단 하 게 하기 위해 공급자를 제조업체와 동일 하 게 모델과 동일한 이름으로 만드는 것을 고려할 수 있습니다. |
| "UpdateNameCompatibility"     | 지정 된 호환성을 사용 하 여 추가 업데이트 이름을 가져올 수 없습니다.     | UpdateProviderCompatibility. ContentLimitNamespaceCompatibility와 동일 합니다. |
| "UpdateVersionCompatibility"  | 지정 된 호환성을 사용 하 여 추가 업데이트 버전을 가져올 수 없습니다.  | UpdateProviderCompatibility. ContentLimitNamespaceCompatibility와 동일 합니다. |
| "CannotProcessUpdateFile"     | 소스 파일을 처리 하는 동안 오류가 발생 했습니다.                                              |                                    |
| "ContentFileCannotDownload"   | 원본 파일을 다운로드할 수 없습니다.                                               | 업데이트 파일의 URL이 여전히 유효한 지 확인 합니다. |

**[다음 단계: 장치 업데이트 관련 문제 해결](.\troubleshoot-device-update.md)**
