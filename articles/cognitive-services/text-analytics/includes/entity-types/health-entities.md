---
title: 의료 기관에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108506"
---
## <a name="health-entity-categories"></a>상태 엔터티 범주:

[상태에 대 한 Text Analytics](../../how-tos/text-analytics-for-health.md)에 의해 반환 되는 엔터티 범주는 다음과 같습니다.  이 컨테이너 미리 보기에서는 영어 텍스트만 지원 되며 각 컨테이너 이미지에는 단일 모델 버전만 제공 됩니다.

### <a name="named-entity-recognition"></a>명명된 엔터티 인식

|범주  |Description   |
|----------|--------------|
| 발전할 | 에이징을. |
| BODY_STRUCTURE | Organs 및 기타 구조를 포함 한 인간 본문의 일부입니다. | 
| CONDITION_QUALIFIER | *가벼운*, *확장*또는 *확산*등의 조건 수준 | 
| 진단할 | 의료 조건. 예: *hypertension* . | 
| 방향도 | *Left* 또는 *anterior*와 같은 방향입니다. | 
| DOSAGE | 조제의 크기 또는 수량입니다.  | 
| EXAMINATION_NAME | 검사의 방법이 나 프로시저입니다. | 
| EXAMINATION_RELATION | 측정 단위와 검사 간의 연결입니다.  | 
| EXAMINATION_UNIT | 검사를 위한 측정 단위입니다. | 
| EXAMINATION_VALUE | 검사 단위 값입니다. | 
| FAMILY_RELATION | *대체*와 같은 familial 관계입니다.  | 
| 빈도 | 삼분기.   | 
| 구분 | 성별. | 
| GENE | *TP53*와 같은 gene 엔터티입니다.   | 
| MEDICATION_CLASS | 조제 클래스. 예: *항생제*.  | 
| MEDICATION_NAME  | Medications 라는 일반 및 브랜드.| 
| ROUTE_OR_MODE  | 조제를 관리 하는 방법입니다. | 
| SYMPTOM_OR_SIGN  | Illness 증상. | 
| TIME  | 곱한. 예: "8 년" 또는 "2: 오전 30 시 30 분" |
| TREATMENT_NAME  | 처리 이름. | 
| VARIANT  | Gene 엔터티의 유전자 variant | 

### <a name="relation-extraction"></a>관계 추출

관계 추출은 텍스트에 언급 된 개념 간의 의미 있는 연결을 식별 합니다. 예를 들어 "조건 시간" 관계는 조건 이름을 시간에 연결 하 여 찾을 수 있습니다. 상태 Text Analytics는 다음과 같은 관계를 식별할 수 있습니다.

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* 약어 
