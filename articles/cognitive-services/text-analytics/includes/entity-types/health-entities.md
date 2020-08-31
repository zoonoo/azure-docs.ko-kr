---
title: 의료 기관에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122376"
---
## <a name="health-entity-categories"></a>상태 엔터티 범주:

[상태에 대 한 Text Analytics](../../how-tos/text-analytics-for-health.md)에 의해 반환 되는 엔터티 범주는 다음과 같습니다.  이 컨테이너 미리 보기에서는 영어 텍스트만 지원 되며 각 컨테이너 이미지에는 단일 모델 버전만 제공 됩니다.

### <a name="named-entity-recognition"></a>명명된 엔터티 인식

|범주  |Description   |
|----------|--------------|
| 연령 | 에이징을. 예를 들면 *30 년*입니다. |
| AdministrativeEvent | 관리 이벤트입니다. |
| BodyStructure | Organs 및 기타 구조를 포함 한 인간 본문의 일부입니다. 예를 들면 *arm*또는 *손자*입니다. | 
| CareEnvironment | 주의가 나 처리가 관리 되는 환경입니다. 예: *비상 방에* | 
| ConditionQualifier | 조건 수준. 예: *가벼운*, *확장*또는 *확산*. | 
| 진단 | 의료 조건. 예: *hypertension*. | 
| Direction | 운전. 예를 들면 *left* 또는 *anterior*입니다. | 
| Dosage | 조제의 크기 또는 수량입니다. 예: *25mg*  | 
| ExaminationName | 검사의 방법이 나 프로시저입니다. 예를 들면 *X 광선*입니다. | 
| RelationalOperator | 두 엔터티 간의 관계를 정의 하는 연산자입니다. 예를 들어, 또는 *보다 작은* `>=` 경우  | 
| MeasurementUnit | 측정 단위 (예: 백분율)입니다. | 
| MeasurementValue | 측정 단위의 숫자 값입니다. | 
| FamilyRelation | Familial 관계입니다. 예: *대체*.  | 
| 빈도 | 삼분기.   | 
| 성별 | 성별. | 
| Gene | *TP53*와 같은 gene 엔터티입니다.   | 
| HealthcareProfession | 조제를 관리 하는 방법입니다. 예: *구두 administration*. | 
| MedicationClass | 조제 클래스. 예: *항생제*.  | 
| MedicationForm | 조제 형식입니다. 예: *캡슐*. | 
| MedicationName  | Medications 라는 일반 및 브랜드. 예: *ibuprofen*. | 
| MedicationRoute | 조제를 관리 하는 방법입니다. 예: *구두 administration*. | 
| SymptomOrSign  | Illness 증상. 예를 들면 *꼽은 throat*입니다. | 
| Time | 곱한. 예: *8 년* 또는 *2* 일 오전 1 시 30 분 |
| TreatmentName  | 처리 이름. 예를 들면 *therapy*입니다. | 
| 변형 | Gene 엔터티의 유전자 variant입니다. | 

### <a name="relation-extraction"></a>관계 추출

관계 추출은 텍스트에 언급 된 개념 간의 의미 있는 연결을 식별 합니다. 예를 들어 "조건 시간" 관계는 조건 이름을 시간에 연결 하 여 찾을 수 있습니다. 상태 Text Analytics는 다음과 같은 관계를 식별할 수 있습니다.

|범주  |Description   |
|----------|--------------|
| DirectionOfBodyStructure | 본문 구조의 방향입니다. |
| DirectionOfCondition | 조건의 방향입니다. |
| DirectionOfExamination | 검사 방향입니다. |
| DirectionOfTreatment | 처리 방향입니다. |
| TimeOfCondition | 조건의 하기 시작 하면 관련 된 시간입니다. |
| QualifierOfCondition | 조건에 대 한 연결 된 한정자입니다. |
| DosageOfMedication | 조제의 dosage입니다. |
| FormOfMedication | 조제 형식입니다. |
| RouteOfMedication | 의약품를 사용 하는 경로 또는 모드입니다. 예: *구두*. |
| FrequencyOfMedication | 조제 사용 되는 빈도입니다. | 
| ValueOfCondition | 조건과 연결 된 숫자 값입니다. |
| 모든 단위 조건 | 조건에 연결 된 단위 (예: 시간)입니다. |
| TimeOfMedication | 조제 사용 된 시간입니다. |
| TimeOfTreatment | 처리가 관리 된 시간입니다. | 
| FrequencyOfTreatment | 처리가 관리 되는 빈도입니다. |
| ValueOfExamination | 검사에 연결 된 숫자 값입니다. | 
| 검사 단위 | 검사와 관련 된 단위 (예: 백분율)입니다. |
| RelationOfExamination | 엔터티와 검사 간의 관계입니다. | 
| TimeOfExamination | 검사와 관련 된 시간입니다. |
| 약어 | 약어입니다.  | 
