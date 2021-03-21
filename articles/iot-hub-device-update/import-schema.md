---
title: IoT Hub 스키마 및 기타 정보에 대 한 업데이트를 장치 업데이트로 가져오기 | Microsoft Docs
description: 업데이트를 IoT Hub 장치 업데이트에 가져올 때 사용 되는 스키마 및 기타 관련 정보 (개체 포함)입니다.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054785"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>IoT Hub 스키마 및 기타 정보에 대 한 업데이트를 장치 업데이트로 가져오기
IoT Hub에 대 한 장치 업데이트로 업데이트를 가져오려면 먼저 [개념](import-concepts.md) 및 [방법 가이드](import-update.md) 를 검토 해야 합니다. 가져오기 매니페스트를 구성할 때 사용 되는 스키마의 세부 정보 및 관련 개체에 대 한 자세한 내용은 아래를 참조 하세요.

## <a name="import-manifest-schema"></a>매니페스트 스키마 가져오기

| Name | Type | 설명 | 제한 |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다. | Id를 업데이트 합니다. |
| UpdateType | 문자열 | 업데이트 형식: <br/><br/> * `microsoft/apt:1` 참조 에이전트를 사용 하 여 패키지 기반 업데이트를 수행 하는 경우를 지정 합니다.<br/> * `microsoft/swupdate:1` 참조 에이전트를 사용 하 여 이미지 기반 업데이트를 수행 하는 경우를 지정 합니다.<br/> * `microsoft/simulator:1` 샘플 에이전트 시뮬레이터를 사용 하는 경우를 지정 합니다.<br/> * 사용자 지정 에이전트를 개발 하는 경우 사용자 지정 형식을 지정 합니다. | 형식: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> 최대 32 문자 합계 |
| InstalledCriteria | 문자열 | 업데이트가 성공적으로 적용 되었는지 여부를 확인 하기 위해 에이전트에서 해석 되는 문자열:  <br/> * 업데이트 형식에 대해 SWVersion **값** 을 지정 `microsoft/swupdate:1` 합니다.<br/> * `{name}-{version}` `microsoft/apt:1` APT 파일에서 가져온 이름 및 버전에 대 한 업데이트 유형을 지정 합니다.<br/> * 업데이트 유형에 대 한 업데이트 파일의 해시를 지정 `microsoft/simulator:1` 합니다.<br/> * 사용자 지정 에이전트를 개발 하는 경우 사용자 지정 문자열을 지정 합니다.<br/> | 최대 64 문자 |
| 호환성 | 개체의 배열 `CompatibilityInfo` | 이 업데이트와 호환 되는 장치의 호환성 정보입니다. | 최대 10 개 항목 |
| CreatedDateTime | 날짜/시간 | 업데이트를 만든 날짜와 시간입니다. | UTC로 구분 된 ISO 8601 날짜 및 시간 형식 |
| ManifestVersion | 문자열 | 매니페스트 스키마 버전을 가져옵니다. `2.0`인터페이스 및 인터페이스와 호환 되는을 지정 `urn:azureiot:AzureDeviceUpdateCore:1` `urn:azureiot:AzureDeviceUpdateCore:4` 합니다. | `2.0`이어야 합니다. |
| 파일 | 개체의 배열 `File` | 페이로드 파일 업데이트 | 최대 5 개 파일 |

## <a name="updateid-object"></a>UpdateId 개체

| Name | Type | 설명 | 제한 |
| --------- | --------- | --------- | --------- |
| 공급자 | 문자열 | 업데이트 id의 공급자 부분입니다. | 1-64 자, 영숫자, 점과 대시가 있습니다. |
| 이름 | string | 업데이트 id의 이름 부분입니다. | 1-64 자, 영숫자, 점과 대시가 있습니다. |
| 버전 | 버전 | 업데이트 id의 버전 부분입니다. | 2 ~ 4 부분으로, 0에서 2147483647 사이의 점으로 구분 된 버전 번호입니다. 앞에 오는 0이 삭제 됩니다. |

## <a name="file-object"></a>File 개체

| Name | Type | 설명 | 제한 |
| --------- | --------- | --------- | --------- |
| 파일 이름 | 문자열 | 파일 이름 | 업데이트 내에서 고유 해야 합니다. |
| SizeInBytes | Int64 | 파일의 크기 (바이트)입니다. | 개별 파일 당 최대 800 MB 또는 초당 800 MB |
| 해시 | `Hashes` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다. | 파일의 해시를 포함 하는 JSON 개체입니다. |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo 개체

| Name | Type | 설명 | 제한 |
| --- | --- | --- | --- |
| DeviceManufacturer | 문자열 | 업데이트와 호환 되는 장치의 제조업체입니다. | 1-64 자, 영숫자, 점과 대시가 있습니다. |
| DeviceModel | 문자열 | 업데이트와 호환 되는 장치의 모델입니다. | 1-64 자, 영숫자, 점과 대시가 있습니다. |

## <a name="hashes-object"></a>해시 개체

| Name | 필수 | Type | 설명 |
| --------- | --------- | --------- | --------- |
| Sha256 | True | 문자열 | SHA-256 알고리즘을 사용 하 여 파일의 Base64 인코딩 해시입니다. |

## <a name="next-steps"></a>다음 단계

[가져오기 개념](./import-concepts.md)에 대해 자세히 알아보세요.

준비가 되 면 가져오기 프로세스를 단계별로 안내 하는 [가져오기 How-To 가이드](./import-update.md)를 사용해 보세요.
