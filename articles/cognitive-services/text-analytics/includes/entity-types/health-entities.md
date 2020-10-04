---
title: 의료 기관에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 563daca1e5179639b8dd3aaf710d92e54faf6aa1
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709525"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>상태 범주, 엔터티 및 특성에 대 한 Text Analytics

[상태에 대 한 Text Analytics](../../how-tos/text-analytics-for-health.md) 는 다음 범주의 의료 개념을 검색 합니다.  이 컨테이너 미리 보기에서는 영어 텍스트만 지원 되며 각 컨테이너 이미지에는 단일 모델 버전만 제공 됩니다.


| 범주  | 설명  |
|---------|---------|
| 표의 | 본문 및 anatomic 시스템, 사이트, 위치 또는 지역에 대 한 정보를 캡처하는 개념입니다. |
 | 통계 | 성별 및 연령에 대 한 정보를 캡처하는 개념입니다. |
 | 자세히 | 진단 절차 및 테스트에 대 한 정보를 캡처하는 개념입니다. |
 | GENOMICS | 유전자 및 변형에 대 한 정보를 캡처하는 개념입니다. |
 | 보건 | 관리 이벤트, 의료 환경 및 의료 professions에 대 한 정보를 캡처하는 개념입니다. |
 | 의료 조건 | 진단, 증상 또는 기호에 대 한 정보를 캡처하는 개념입니다. |
 | 조제 | 조제 이름, 클래스, dosage 및 관리 경로를 포함 하 여 조제에 대 한 정보를 캡처하는 개념입니다. |
 | 소셜 | 가족 관계와 같은 medically 관련 소셜 측면에 대 한 정보를 캡처하는 개념입니다. |
 | 처리 | therapeutic 프로시저에 대 한 정보를 캡처하는 개념입니다. |
  
각 범주에는 두 가지 개념 그룹이 포함 될 수 있습니다.

* **엔터티** -진단, 조제 이름 또는 처리 이름과 같은 의료 개념을 캡처하는 용어입니다.  예를 들어 *bronchitis* 은 진단이 고 *aspirin* 는 조제 이름입니다.  이 그룹의 멘 션은 UMLS 개념 ID에 연결 될 수 있습니다.
* 검색 된 엔터티에 대 한 자세한 정보를 제공 하는 **특성** - *예를 들어* , *bronchitis* 또는 *81 mg* 의 조건 한정자는 *aspirin*에 대 한 dosage입니다.  이 범주의 멘 션은 UMLS 개념 ID에 연결 되지 않습니다.

또한 서비스는 특성 및 엔터티 간의 관계를 포함 하는 다양 한 개념 간의 *관계를 인식* 합니다. 예를 들어 *, dosage* to *조제 name* 및 엔터티 간의 *관계 (예* : 약어 검색)를 포함 합니다.

## <a name="anatomy"></a>구조

### <a name="entities"></a>엔터티

**BODY_STRUCTURE** -본문 시스템, anatomic 위치 또는 지역 및 본문 사이트. 예를 들어 arm, knee, abdomen, 코, liver, head, respiratory system, lymphocytes입니다.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Body structure 엔터티의 예입니다.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="attributes"></a>특성

왼쪽, 오른쪽, 위쪽, 사후 등의 방향 면에서 본문 구조의 특징을 나타내는 **방향** 입니다.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="supported-relations"></a>지원 되는 관계

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>인구 통계

### <a name="entities"></a>엔터티

**Age** -환자, 가족 및 기타 멤버 등의 모든 사용 약관 예를 들어 40-년-이전, 51 yo, 3 개월 이전, 성인, infant, elderly 젊은, 단조, 중간 오래.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Body structure 엔터티의 예입니다.":::


**성별** -주체의 성별을 공개 하는 용어입니다. 예: 남성, 여성, 여자, gentleman, 레이디.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="attributes"></a>특성

인구 통계 엔터티와 추가 정보 간의 관계를 표현 하는 **RELATIONAL_OPERATOR** 구문입니다.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Body structure 엔터티의 예입니다.":::

## <a name="examinations"></a>시험

### <a name="entities"></a>엔터티

**EXAMINATION_NAME** – 진단 프로시저 및 테스트. 예를 들어 MRI, ECG, 하이브 test, hemoglobin, platelets count, scale systems (예: *Bristol stool scale*)가 있습니다.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="attributes"></a>특성

**방향** – 검사의 특징을 나타내는 방향 용어입니다.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEASUREMENT_UNIT** – 검사 단위입니다. 예를 들어 *hemoglobin > 9.5 g/dl*에서 *g/dl* 이라는 용어는 *hemoglobin* 테스트에 대 한 단위입니다.

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEASUREMENT_VALUE** – 검사 값입니다. 예를 들어 *hemoglobin > 9.5 g/dL*에서 *9.5* 이라는 용어는 *hemoglobin* 테스트에 대 한 값입니다.

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

**RELATIONAL_OPERATOR** -검사와 추가 정보 간의 관계를 표현 하는 문구입니다. 예를 들어 대상 검사에 필요한 측정 값이 있습니다.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

**시간** -검사의 시작 및/또는 길이 (기간)와 관련 된 임시 용어입니다. 예를 들어 테스트가 발생 했을 때입니다.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="supported-relations"></a>지원 되는 관계

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>엔터티

**GENE** – genes의 모든 멘 션 예: MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Body structure 엔터티의 예입니다.":::

**변형** – gene 변형의 모든 멘 션 >예: MTRR T, (): r.1462_1557del96
  
## <a name="healthcare"></a>의료

### <a name="entities"></a>엔터티
  
**ADMINISTRATIVE_EVENT** – 의료 시스템에 관련 된 이벤트 이지만 관리/반 관리 특성을가지고 있습니다. 예를 들어 등록, 허용, 평가판, 연구 항목, 전송, 방전, 입원, 병원은 유지 됩니다. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

**CARE_ENVIRONMENT** – 환자가 주의 하 여 제공 되는 환경 또는 위치입니다. 예: 비상 실, 의료의 사무실, cardio unit, hospice, 병원.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

**HEALTHCARE_PROFESSION** – 의료 전문가 사용이 허가 되거나 허가 되지 않은 것입니다. 예: dentist, pathologist, neurologist, radiologist, pharmacist, nutritionist, 물리적 therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Body structure 엔터티의 예입니다.":::

## <a name="medical-condition"></a>의료 조건

### <a name="entities"></a>엔터티

**진단** – 질병, 증후군, 손상. 예: 유방암 암, Alzheimer, HTN, CHF, spinal 코드

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Body structure 엔터티의 예입니다.":::

**SYMPTOM_OR_SIGN** – 질병 또는 기타 진단의 주관적 또는 객관적인 증거입니다. 예를 들어, 상자 불만, 골칫거리, dizziness, rash, SOB, abdomen는 소프트, 좋은 bowel 사운드, 잘 nourished입니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="attributes"></a>특성

의료 상태를 설명 하는 데 사용 되는 **CONDITION_QUALIFIER** 품질 용어입니다. 다음 하위 범주는 모두 한정자로 간주 됩니다.

1.  시간 관련 식: 갑작스러운, qualitatively, chronic, 위해 노력해 왔으며 등의 시간 차원에 대해 설명 하는 용어입니다. 
2.  품질 식: 레코딩, sharp 등 의료 조건의 "특성"을 설명 하는 용어입니다.
3.  심각도 식: 심각, 가벼운, 비트, 미제어
4.  Extensivity 식: 로컬, 초점면, 확산.
5.  방사선 식: radiates, 방사선.
6.  조건 크기 조정: 일부 경우에는 조건에 따라 정렬 된 값 목록으로 표시 됩니다. 예를 들어, 환자는 단계 III pancreatic 암입니다.
7.  조건 과정: 개선, 작업, 해결, remission 등 조건의 진행 또는 진행과 관련 된 용어입니다. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Body structure 엔터티의 예입니다.":::

본문 의료 조건의 특징을 나타내는 **방향** 방향 용어입니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

**빈도** -의료 상황이 발생 하거나 발생 하는 빈도입니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEASUREMENT_UNIT** -의료 조건의 특징을 나타내는 단위입니다. 예를 들어 *1.5 x2x1 cm tumor*에서 *cm* 이라는 용어는 *tumor*에 대 한 측정 단위입니다. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEASUREMENT_VALUE** -의료 조건의 특징을 나타내는 값입니다. 예를 들어 *1.5 x2x1 cm tumor*에서 *1.5 x2x1* 이라는 용어는 *tumor*의 측정 값입니다. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

의료 조건 추가 정보 간의 관계를 표현 하는 **RELATIONAL_OPERATOR** 구문입니다. 예를 들어 시간 또는 측정 값이 있습니다. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Body structure 엔터티의 예입니다.":::

의료 조건의 시작 및/또는 길이 (기간)와 관련 된 **시간** 임시 용어입니다. 예를 들어 증상이 시작 될 때 (하기 시작 하면) 또는 질병이 발생 한 경우입니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="supported-relations"></a>지원 되는 관계

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>조제

### <a name="entities"></a>엔터티

**MEDICATION_CLASS** – 작업의 비슷한 메커니즘, 관련 된 작업 모드, 유사한 화학 구조, 동일한 질병을 처리 하는 데 사용 되는 medications 집합입니다. 예를 들어 ACE inhibitor, opioid, 항생제, 패인 relievers입니다.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEDICATION_NAME** – 저작권의 브랜드 이름 및 비 브랜드 이름을 포함 하 여 조제 멘 션 합니다. 예: Advil, Ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="attributes"></a>특성

**DOSAGE** -조제의 크기를 지정 합니다. 예: 의사 소통 방식 Sodium Chloride solution *1000 mL*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Body structure 엔터티의 예입니다.":::

**빈도** -조제 수행 해야 하는 빈도입니다.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEDICATION_FORM** -조제의 형식입니다. 예를 들면 solution, 약, 캡슐, 태블릿, patch, gel, paste, 폼, 스프레이, drop, syrup입니다.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Body structure 엔터티의 예입니다.":::

**MEDICATION_ROUTE** -조제의 관리 방법입니다. 예: 구두, vaginal, IV, epidural, 토픽, inhaled.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Body structure 엔터티의 예입니다.":::

조제와 추가 정보 간의 관계를 표현 하는 **RELATIONAL_OPERATOR** 구문입니다. 예를 들어 필수 측정 값입니다.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Body structure 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="supported-relations"></a>지원 되는 관계

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**
  
## <a name="treatment"></a>처리 방법

### <a name="entities"></a>엔터티

**TREATMENT_NAME** – Therapeutic 프로시저 예를 들면 knee 바꾸기 수술, 뼈, transplant, TAVI, diet입니다.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Body structure 엔터티의 예입니다.":::

### <a name="attributes"></a>특성

처리의 특징을 나타내는 **방향** 방향 용어입니다.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Body structure 엔터티의 예입니다.":::

**빈도** -처리가 발생 하거나 발생 해야 하는 빈도입니다.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Body structure 엔터티의 예입니다.":::
 
처리와 추가 정보 간의 관계를 표현 하는 **RELATIONAL_OPERATOR** 구문입니다.  예를 들어 이전 절차에서 전달 된 시간입니다.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Body structure 엔터티의 예입니다.":::

처리의 시작 및/또는 길이 (기간)와 관련 된 **시간** 임시 용어입니다. 예를 들어 처리가 제공 된 날짜입니다.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Body structure 엔터티의 예입니다.":::


### <a name="supported-relations"></a>지원 되는 관계

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**

## <a name="social"></a>소셜

### <a name="entities"></a>엔터티

**FAMILY_RELATION** – 주체의 가족 친척에 대해 언급 합니다. 예를 들면 아버지, 딸, 형제, 부모입니다.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Body structure 엔터티의 예입니다.":::
