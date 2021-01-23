---
title: FHIR 용 Azure API에서 $export 명령을 호출 하 여 내보내기 실행
description: 이 문서에서는 $export를 사용 하 여 FHIR 데이터를 내보내는 방법을 설명 합니다.
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 48dbd0892c9ec02f203edba55d1104f1ab0118a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737611"
---
# <a name="how-to-export-fhir-data"></a>FHIR 데이터를 내보내는 방법


대량 내보내기 기능을 사용 하면 [fhir 사양](https://hl7.org/fhir/uv/bulkdata/export/index.html)에 따라 Fhir 서버에서 데이터를 내보낼 수 있습니다. 

$Export를 사용 하기 전에 FHIR 용 Azure API가이를 사용 하도록 구성 되어 있는지 확인 해야 합니다. 내보내기 설정 구성 및 Azure storage 계정 만들기 [는 데이터 내보내기 구성 페이지](configure-export-data.md)를 참조 하세요.

## <a name="using-export-command"></a>$export 명령 사용

내보내기에 대 한 Azure API를 구성 하 고 나면 $export 명령을 사용 하 여 서비스에서 데이터를 내보낼 수 있습니다. 데이터는 내보내기를 구성 하는 동안 지정한 저장소 계정에 저장 됩니다. FHIR 서버에서 $export 명령을 호출 하는 방법을 알아보려면 [HL7 fhir $export 사양](https://hl7.org/Fhir/uv/bulkdata/export/index.html)에 대 한 설명서를 참조 하세요. 

FHIR 용 Azure API는 다음 수준에서 $export을 지원 합니다.
* [시스템](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [환자](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [환자 *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -FHIR 용 Azure API는 관련 된 모든 리소스를 내보내며 그룹의 특성은 내보내지 않습니다. `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export``Group/[ID]/$export`리소스가 둘 이상의 리소스 구획에 있거나 여러 그룹에 있는 경우는 중복 리소스를 내보낼 수 있습니다.

또한 큐 중에 위치 헤더에서 반환 된 URL을 통해 내보내기 상태를 확인 하는 작업은 실제 내보내기 작업을 취소 하는 것과 함께 지원 됩니다.

## <a name="settings-and-parameters"></a>설정 및 매개 변수

### <a name="headers"></a>헤더
$Export 작업에 대해 설정 해야 하는 두 개의 필수 헤더 매개 변수가 있습니다. 값은 현재 [$export 사양](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)에 따라 정의 됩니다.
* **Accept** -application/fhir + json
* **선호** -응답-async

### <a name="query-parameters"></a>쿼리 매개 변수
FHIR 용 Azure API는 다음과 같은 쿼리 매개 변수를 지원 합니다. 이러한 매개 변수는 모두 선택 사항입니다.
|쿼리 매개 변수        | FHIR 사양에 정의 되어 있나요?    |  설명|
|------------------------|---|------------|
| \_outputFormat | 예 | 는 현재 응용 프로그램/fhir + ndjson, application/ndjson 또는 단지 ndjson에 맞추기 위한 세 가지 값을 지원 합니다. 모든 내보내기 작업은 `ndjson` 를 반환 하며 전달 된 값은 코드 동작에 영향을 주지 않습니다. |
| \_since | 예 | 제공 된 시간 이후 수정 된 리소스만 내보낼 수 있습니다. |
| \_입력할 | 예 | 포함할 리소스의 형식을 지정할 수 있습니다. 예를 들어, \_ type = 환자는 환자 리소스만 반환 합니다.|
| \_typefilter | 예 | 보다 세분화 된 필터링을 요청 하기 위해 \_ 형식 매개 변수와 함께 typefilter를 사용할 수 있습니다 \_ . _TypeFilter 매개 변수의 값은 결과를 추가로 제한 하는 쉼표로 구분 된 FHIR 쿼리 목록입니다. |
| \_컨테이너 | 아니요 |  구성 된 저장소 계정 내에서 데이터를 내보내야 하는 컨테이너를 지정 합니다. 컨테이너를 지정 하면 이름이 인 새 폴더의 해당 컨테이너로 데이터가 내보내집니다. 컨테이너를 지정 하지 않으면 타임 스탬프 및 작업 ID를 사용 하 여 새 컨테이너로 내보냅니다. |


## <a name="next-steps"></a>다음 단계

이 문서에서는 $export 명령을 사용 하 여 FHIR 리소스를 내보내는 방법을 배웠습니다. 다음으로 식별 되지 않은 데이터를 내보내는 방법에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[식별 되지 않은 데이터 내보내기](de-identified-export.md)
