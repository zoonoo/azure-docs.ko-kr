---
title: 의료 기관에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599316"
---
[상태 프로세스에 대 한 Text Analytics](../../how-tos/text-analytics-for-health.md) 비구조적 의료 데이터에서 정보를 추출 합니다. 이 서비스는 의료 개념을 검색 및 표시 하 고, 개념에 어설션을 할당 하 고, 개념 간의 의미 관계를 유추 하 고, 공통 의료 온톨로지에 연결 합니다.

상태에 대 한 Text Analytics는 다음 범주의 의료 개념을 검색 합니다. 이 미리 보기에서는 영어 텍스트만 지원 되며 단일 모델 버전만 사용할 수 있습니다.

| 범주  | 설명  |
|---------|---------|
| [표의](#anatomy) | 본문 및 anatomic 시스템, 사이트, 위치 또는 지역에 대 한 정보를 캡처하는 개념입니다. |
 | [통계](#demographics) | 성별 및 연령에 대 한 정보를 캡처하는 개념입니다. |
 | [자세히](#examinations) | 진단 절차 및 테스트에 대 한 정보를 캡처하는 개념입니다. |
 | [일반 특성](#general-attributes) | 위 범주의 다른 개념에 대 한 자세한 정보를 제공 하는 개념입니다. |
 | [GENOMICS](#genomics) | 유전자 및 변형에 대 한 정보를 캡처하는 개념입니다. |
 | [보건](#healthcare) | 관리 이벤트, 의료 환경 및 의료 professions에 대 한 정보를 캡처하는 개념입니다. |
 | [의료 조건](#medical-condition) | 진단, 증상 또는 기호에 대 한 정보를 캡처하는 개념입니다. |
 | [조제](#medication) | 조제 이름, 클래스, dosage 및 관리 경로를 포함 하 여 조제에 대 한 정보를 캡처하는 개념입니다. |
 | [소셜](#social) | 가족 관계와 같은 medically 관련 소셜 측면에 대 한 정보를 캡처하는 개념입니다. |
 | [처리](#treatment) | therapeutic 프로시저에 대 한 정보를 캡처하는 개념입니다. |

아래에서 추가 정보 및 예제를 확인할 수 있습니다.

## <a name="anatomy"></a>구조

### <a name="entities"></a>엔터티

**BODY_STRUCTURE** -본문 시스템, anatomic 위치 또는 지역 및 본문 사이트. 예를 들어 arm, knee, abdomen, 코, liver, head, respiratory system, lymphocytes입니다.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Body structure 엔터티의 예입니다.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Body structure 엔터티의 확장 된 예제입니다.":::

## <a name="demographics"></a>인구 통계

### <a name="entities"></a>엔터티

**Age** -환자, family members 및 기타 사용 약관을 포함 하는 모든 사용 약관 및 문구. 예를 들어 40-년-이전, 51 yo, 3 개월 이전, 성인, infant, elderly 젊은, 단조, 중간 오래.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Age 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Age 엔터티의 또 다른 예입니다.":::


**성별** -주체의 성별을 공개 하는 용어입니다. 예: 남성, 여성, 여자, gentleman, 레이디.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="성별 엔터티의 예입니다.":::

## <a name="examinations"></a>시험

### <a name="entities"></a>엔터티

**EXAMINATION_NAME** – 중요 한 기호 및 본문 측정치를 비롯 한 진단 절차 및 테스트입니다. 예를 들어 MRI, ECG, 하이브 test, hemoglobin, platelets count, scale systems (예: *Bristol stool scale*)가 있습니다.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="시험 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="검사 이름 엔터티의 또 다른 예입니다.":::

## <a name="general-attributes"></a>일반 특성

### <a name="entities"></a>엔터티

**날짜** -의료 조건, 검사, 처리, 조제 또는 관리 이벤트와 관련 된 전체 날짜입니다.

**방향** – 본문 구조, 의료 조건, 검사 또는 처리와 관련 될 수 있는 방향 용어 (예: left, 좌우, upper, 사후)입니다.

**FREQUENCY** -의료 조건, 검사, 처리 또는 조제 발생 빈도 또는 발생 빈도를 설명 합니다.

**MEASUREMENT_VALUE** – 검사 또는 의료 조건 측정과 관련 된 값입니다.

**MEASUREMENT_UNIT** – 검사 또는 의료 조건 측정과 관련 된 측정 단위입니다.

엔터티와 일부 추가 정보 간의 양적 관계를 표현 하는 **RELATIONAL_OPERATOR** 문구.

의료 조건, 검사, 처리, 조제 또는 관리 이벤트의 시작 및/또는 길이 (기간)와 관련 된 **시간** 임시 용어입니다. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>엔터티

**GENE_OR_PROTEIN** – chromosomes 및 단백질의 chromosomes 및 및 사람의 이름 및 기호에 대 한 모든 언급입니다. 예: MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Gene 엔터티의 예입니다.":::

**변형** – gene 변형과 변경이의 모든 멘 션 예 `c.524C>T` :, `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>의료

### <a name="entities"></a>엔터티
  
**ADMINISTRATIVE_EVENT** – 의료 시스템에 관련 된 이벤트 이지만 관리/반 관리 특성을가지고 있습니다. 예를 들어 등록, 허용, 평가판, 연구 항목, 전송, 방전, 입원, 병원은 유지 됩니다. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="의료 이벤트 엔터티의 예입니다.":::

**CARE_ENVIRONMENT** – 환자가 주의 하 여 제공 되는 환경 또는 위치입니다. 예: 비상 실, 의료의 사무실, cardio unit, hospice, 병원.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="이 스크린샷은 의료 환경 엔터티의 예를 보여 줍니다.":::

**HEALTHCARE_PROFESSION** – 의료 전문가 사용이 허가 되거나 허가 되지 않은 것입니다. 예: dentist, pathologist, neurologist, radiologist, pharmacist, nutritionist, 물리적 therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="이 스크린샷은 의료 환경 엔터티의 또 다른 예를 보여 줍니다.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="의료 환경 엔터티의 또 다른 예입니다.":::

## <a name="medical-condition"></a>의료 조건

### <a name="entities"></a>엔터티

**진단** – 질병, 증후군, 손상. 예: 유방암 암, Alzheimer, HTN, CHF, spinal 코드

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="의료 조건 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="의료 조건 엔터티의 또 다른 예입니다.":::

**SYMPTOM_OR_SIGN** – 질병 또는 기타 진단의 주관적 또는 객관적인 증거입니다. 예를 들어, 상자 불만, 골칫거리, dizziness, rash, SOB, abdomen는 소프트, 좋은 bowel 사운드, 잘 nourished입니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="의료 조건 서명 또는 증상 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="의료 조건 서명 또는 증상 엔터티의 또 다른 예입니다.":::

의료 상태를 설명 하는 데 사용 되는 **CONDITION_QUALIFIER** 정 성적 용어입니다. 다음 하위 범주는 모두 한정자로 간주 됩니다.

1.  시간 관련 식: 갑작스러운, qualitatively, chronic, 위해 노력해 왔으며 등의 시간 차원에 대해 설명 하는 용어입니다. 
2.  품질 식: 레코딩, sharp 등 의료 조건의 "특성"을 설명 하는 용어입니다.
3.  심각도 식: 심각, 가벼운, 비트, 미제어
4.  Extensivity 식: 로컬, 초점면, 확산.
5.  방사선 식: radiates, 방사선.
6.  조건 크기 조정: 일부 경우에는 조건에 따라 정렬 된 값 목록으로 표시 됩니다. 예를 들어, 환자는 단계 III pancreatic 암입니다.
7.  조건 과정: 개선, 작업, 해결, remission 등 조건의 진행 또는 진행과 관련 된 용어입니다. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="조건 한정자 특성 및 진단 엔터티의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="조건 한정자 특성 및 진단 엔터티의 또 다른 예입니다.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="증상 및 조제 엔터티가 포함 된 조건 한정자 특성의 예입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="이 스크린샷에서는 진단 엔터티를 사용 하는 조건 한정자 특성의 또 다른 예를 보여 줍니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="이 스크린샷에서는 진단 엔터티를 사용 하는 조건 한정자 특성의 추가 예를 보여 줍니다.":::

## <a name="medication"></a>조제

### <a name="entities"></a>엔터티

**MEDICATION_CLASS** – 작업의 비슷한 메커니즘, 관련 된 작업 모드, 유사한 화학 구조, 동일한 질병을 처리 하는 데 사용 되는 medications 집합입니다. 예를 들어 ACE inhibitor, opioid, 항생제, 패인 relievers입니다.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="조제 class 엔터티의 예입니다.":::

**MEDICATION_NAME** – 저작권의 브랜드 이름 및 비 브랜드 이름을 포함 하 여 조제 멘 션 합니다. 예를 들면 Ibuprofen입니다.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="조제 name 엔터티의 예입니다.":::

**DOSAGE** -조제의 크기를 지정 합니다. 예: 의사 소통 방식 Sodium Chloride solution *1000 mL*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="조제 dosage 특성의 예입니다.":::

**MEDICATION_FORM** -조제의 형식입니다. 예를 들면 solution, 약, 캡슐, 태블릿, patch, gel, paste, 폼, 스프레이, drop, syrup입니다.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="조제 form 특성의 예입니다.":::

**MEDICATION_ROUTE** -조제의 관리 방법입니다. 예: 구두, vaginal, IV, epidural, 토픽, inhaled.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="조제 route 특성의 예입니다.":::

## <a name="social"></a>소셜

### <a name="entities"></a>엔터티

**FAMILY_RELATION** – 주체의 가족 친척에 대해 언급 합니다. 예를 들면 아버지, 딸, 형제, 부모입니다.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="스크린샷 처리 시간 특성의 또 다른 예를 보여 줍니다.":::

## <a name="treatment"></a>처리 방법

### <a name="entities"></a>엔터티

**TREATMENT_NAME** – Therapeutic 프로시저 예를 들면 knee 바꾸기 수술, 뼈, transplant, TAVI, diet입니다.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="처리 이름 엔터티의 예입니다.":::
