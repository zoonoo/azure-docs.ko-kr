---
title: Azure API for FHIR patient-everything 사용
description: 이 문서에서는 Azure API for FHIR patient-everything 연산을 사용하는 방법을 설명합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: fee544c318df318e09c75ebaf18fc0837cb4af0d
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562734"
---
# <a name="patient-everything-in-fhir"></a>FHIR의 Patient-Everything

[$patient-everything](https://www.hl7.org/fhir/patient-operation-everything.html) 작업은 환자에게 전체 레코드에 대한 액세스 권한을 제공하거나 공급자 또는 다른 사용자가 대량 데이터 다운로드를 수행할 수 있도록 하기 위해 만들어졌습니다. 이 작업은 이 작업이 호출되는 리소스 또는 컨텍스트에 설명된 하나 이상의 환자와 관련된 모든 정보를 반환하는 데 사용됩니다.  

## <a name="use-patient-everything"></a>patient-everything 사용
patient-everything를 호출하려면 다음 명령을 사용합니다.

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure API for FHIR 제공된 환자 ID와 일치하는 환자를 찾을 수 있는지 확인합니다. 결과가 발견되면 응답은 다음 정보가 있는 "searchset" 형식의 번들입니다. 
* [환자 리소스](https://www.hl7.org/fhir/patient.html) 
*  환자 리소스에서 직접 참조하는 리소스(링크 제외) 
*  환자의 [구획에](https://www.hl7.org/fhir/compartmentdefinition-patient.html) 있는 리소스
*  환자 [리소스를](https://www.hl7.org/fhir/device.html) 참조하는 디바이스 리소스  

 
> [!Note]
> $patient-everything는 현재 Cosmos DB 통해 제공되는 오픈 소스 FHIR 서버에서 사용할 수 있으며 7월 1일 이전의 Azure API for FHIR 사용할 수 있습니다. FHIR 서버에 대한 기능 문에는 문제 [1989에서](https://github.com/microsoft/fhir-server/issues/1989)추적되는 $patient 대한 지원이 없습니다. 


## <a name="patient-everything-parameters"></a>Patient-everything 매개 변수
Azure API for FHIR 다음 쿼리 매개 변수를 지원합니다. 이러한 매개 변수는 모두 선택 사항입니다.

|쿼리 매개 변수        |  Description|
|-----------------------|------------|
| \_형식 | 응답에 포함할 리소스 유형을 지정할 수 있습니다. 예를 들어 \_ type=Encounter는 `Encounter` 환자와 연결된 리소스만 반환합니다. |
| \_since | 제공된 시간 이후에 수정된 리소스만 반환합니다. |
| start | 시작 날짜를 지정하면 지정된 시작 날짜 이후의 임상 날짜가 있는 리소스가 가져옵니다. 시작 날짜가 제공되지 않으면 종료 날짜 이전의 모든 레코드가 범위에 있습니다. |
| end | 종료 날짜를 지정하면 임상 날짜가 지정된 종료 날짜 이전인 리소스가 가져옵니다. 종료 날짜가 제공되지 않으면 시작 날짜 이후의 모든 레코드가 범위에 있습니다. |

> [!Note]
> 특정 환자의 ID를 지정해야 합니다. 모든 환자의 모든 데이터가 필요한 경우 [$export](export-data.md)참조하세요. 


## <a name="examples-of-patient-everything"></a>$patient 예 

다음은 $patient-everything 작업을 사용하는 몇 가지 추가 예제입니다. 

$patient-everything를 사용하여 2010년에서 2020년 사이에 환자의 "모든 것"을 쿼리하려면 다음 호출을 사용합니다. 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

$patient-everything를 사용하여 환자의 관찰 및 발생을 쿼리하려면 다음 호출을 사용합니다. 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

$patient-everything를 사용하여 2021-05-27T05:00:00Z 이후 환자의 "모든 것"을 쿼리하려면 다음 호출을 사용합니다. 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

이러한 각 호출에 대해 환자가 발견되면 해당 리소스 번들로 200 응답을 다시 받게 됩니다.

## <a name="next-step"></a>다음 단계
이제 patient-everything 작업을 사용하는 방법을 배웠으므로 검색 가이드 개요에서 더 많은 검색 옵션에 대해 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[FHIR 검색 개요](overview-of-search.md)