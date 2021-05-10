---
title: 의료용으로 명명된 엔터티
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599316"
---
[의료용 Text Analytics](../../how-tos/text-analytics-for-health.md)는 비정형 의료 데이터에서 정보를 처리하고 추출합니다. 서비스는 의료 개념을 검색 및 표시하고, 개념에 어설션을 할당하고, 개념 간의 의미 관계를 유추하고, 공통 의료 온톨로지에 연결합니다.

의료용 Text Analytics는 다음 범주의 의료 개념을 검색합니다. 이 미리 보기에서는 영어 텍스트만 지원되며 단일 모델 버전만 사용할 수 있습니다.

| Category  | Description  |
|---------|---------|
| [해부학](#anatomy) | 신체 및 해부학적 체계, 부위, 위치 또는 영역에 대한 정보를 캡처하는 개념입니다. |
 | [인구 통계](#demographics) | 성별 및 연령에 관한 정보를 캡처하는 개념입니다. |
 | [검사](#examinations) | 진단 절차 및 테스트에 대한 정보를 캡처하는 개념입니다. |
 | [일반 특성](#general-attributes) | 위 범주와 다른 개념에 대한 더 많은 정보를 제공하는 개념입니다. |
 | [유전체학](#genomics) | 유전 및 변형에 관한 정보를 캡처하는 개념입니다. |
 | [의료](#healthcare) | 관리 이벤트, 치료 환경 및 의료업에 관한 정보를 캡처하는 개념입니다. |
 | [의학적 상태](#medical-condition) | 진단, 증상 또는 징후에 대한 정보를 캡처하는 개념입니다. |
 | [약물](#medication) | 약물 이름, 등급, 용량 및 관리 경로를 비롯한 약물에 대한 정보를 캡처하는 개념입니다. |
 | [사회적](#social) | 가족 관계와 같은 의학적으로 관련된 사회적 측면에 대한 정보를 캡처하는 개념입니다. |
 | [치료](#treatment) | 치료 절차에 대한 정보를 캡처하는 개념입니다. |

아래에서 추가 정보 및 예제를 확인할 수 있습니다.

## <a name="anatomy"></a>구조

### <a name="entities"></a>엔터티

**BODY_STRUCTURE** - 신체 체계, 해부학적 위치 또는 영역 및 신체 부위. 예를 들어 팔, 무릎, 복부, 코, 간, 머리, 호흡계, 림프구가 있습니다.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="신체 구조 엔터티의 예제입니다.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="신체 구조 엔터티의 확장된 예제입니다.":::

## <a name="demographics"></a>인구 통계

### <a name="entities"></a>엔터티

**연령** - 환자, 가족 구성원 및 기타에 대한 용어를 포함한 모든 연령 용어 및 구입니다. 예를 들어 40살, 51살, 3개월, 성인, 유아, 노인, 청년, 미성년자, 중년이 있습니다.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="연령 엔터티의 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="연령 엔터티의 다른 예제입니다.":::


**성별** - 대상의 성별을 알리는 용어입니다. 예를 들어 남성, 여성, 여자, 신사, 숙녀가 있습니다.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="성별 엔터티의 예제입니다.":::

## <a name="examinations"></a>검사

### <a name="entities"></a>엔터티

**EXAMINATION_NAME** – 바이탈 사인 및 체격 측정치를 비롯한 진단 절차 및 테스트입니다. 예를 들어 MRI, ECG, HIV 테스트, 헤모글로빈, 혈소판 수, 척도 체계(예: *브리스톨 대변 척도*)가 있습니다.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="검사 엔터티의 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="검사 이름 엔터티의 또 다른 예제입니다.":::

## <a name="general-attributes"></a>일반 특성

### <a name="entities"></a>엔터티

**날짜** - 의학적 상태, 검사, 치료, 약물 또는 관리 이벤트와 관련된 전체 날짜입니다.

**방향** – 신체 구조, 의학적 상태, 검사 또는 치료와 관련될 수 있는 방향성 용어(예: 왼쪽, 측면, 상부, 뒤쪽)입니다.

**주기** - 의학적 상태, 검사, 치료 또는 약물이 발생했거나, 발생하거나, 발생해야 하는 주기를 설명합니다.

**MEASUREMENT_VALUE** – 검사 또는 의학적 상태 측정과 관련된 값입니다.

**MEASUREMENT_UNIT** – 검사 또는 의학적 상태 측정과 관련된 측정 단위입니다.

**RELATIONAL_OPERATOR** - 엔터티와 일부 추가 정보 간의 양적 관계를 표현하는 구입니다.

**시간** - 의학적 상태, 검사, 치료, 약물 또는 관리 이벤트의 시작 및/또는 길이(기간)와 관련된 시간 용어입니다. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>엔터티

**GENE_OR_PROTEIN** – 염색체, 염색체 부분 및 단백질과 함께 인간 유전자의 이름과 상징에 대한 모든 언급입니다. 예를 들어 MTRR, F2가 있습니다.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="유전자 엔터티의 예제입니다.":::

**변이** – 유전자 변형과 돌연변이에 대한 모든 언급입니다. 예를 들어 `c.524C>T`, `(MTRR):r.1462_1557del96`이 있습니다.
  
## <a name="healthcare"></a>의료

### <a name="entities"></a>엔터티
  
**ADMINISTRATIVE_EVENT** – 의료 시스템에 관련된 이벤트이지만 관리/반 관리 특성을 가지고 있습니다. 예를 들어 등록, 허용, 평가판, 연구 항목, 전송, 방전, 입원, 재원 기간이 있습니다. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="의료 이벤트 엔터티의 예제입니다.":::

**CARE_ENVIRONMENT** – 환자를 돌보는 환경 또는 위치입니다. 예를 들어 응급실, 진료실, 심장 관련 장치(cardio unit), 호스피스, 병원이 있습니다.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="이 스크린샷은 의료 환경 엔터티의 예제를 보여 줍니다.":::

**HEALTHCARE_PROFESSION** – 자격을 소유한 의료 전문가 또는 비면허. 예를 들어 치과의, 병리학자, 신경과 전문의, 방사선 전문의, 약사, 영양사, 물리 치료사, 척추 지압사가 있습니다.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="이 스크린샷은 의료 환경 엔터티의 다른 예제를 보여 줍니다.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="의료 환경 엔터티의 다른 예제입니다.":::

## <a name="medical-condition"></a>의학적 상태

### <a name="entities"></a>엔터티

**진단** – 질병, 증후군, 손상. 예를 들어 유방암, 알츠하이머, HTN, CHF, 척추손상이 있습니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="의학적 상태 엔터티의 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="의학적 상태 엔터티의 다른 예제입니다.":::

**SYMPTOM_OR_SIGN** – 질병 또는 기타 진단의 주관적 또는 객관적인 증거입니다. 예를 들어, 흉통, 두통, 현기증, 발진, SOB, 복부 부드러움, 좋은 장음, 영양 상태 좋음이 있습니다.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="의학적 상태 징후 또는 증상 엔터티의 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="의학적 상태 징후 또는 증상 엔터티의 다른 예제입니다.":::

**CONDITION_QUALIFIER** - 의학적 상태를 설명하는 데 사용되는 질적 용어입니다. 다음 하위 범주는 모두 한정자로 간주됩니다.

1.  시간 관련 표현: 급, 급성, 만성, 장기와 같이 시간 차원을 질적으로 설명하는 용어입니다. 
2.  품질 표현: 작열감, 뾰족과 같이 의학적 상태의 “유형”을 설명하는 용어입니다.
3.  중증도 표현: 심한, 가벼운, 약간, 비조절.
4.  포괄성 표현: 국소, 초점, 광범위.
5.  방사선 표현: 발산, 방사선.
6.  상태 등급: 일부 경우 상태는 값의 한정된 순서 목록인 등급으로 특징지어집니다. 예를 들어 췌장암 3기 환자가 있습니다.
7.  상태 과정: 호전, 악화, 해소, 완화와 같은 상태의 과정 또는 진행에 관련된 용어입니다. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="상태 한정자 특성 및 진단 엔터티의 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="상태 한정자 특성 및 진단 엔터티의 다른 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="증상 및 의학 엔터티가 포함된 상태 한정자 특성의 예제입니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="이 스크린샷은 진단 엔터티가 포함된 상태 한정자 특성의 다른 예제를 보여 줍니다.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="이 스크린샷은 진단 엔터티가 포함된 상태 한정자 특성의 다른 예제를 보여 줍니다.":::

## <a name="medication"></a>약물

### <a name="entities"></a>엔터티

**MEDICATION_CLASS** – 작용의 유사한 메커니즘, 관련된 작용 모드, 유사한 화학 구조 및/또는 동일한 질병을 처리하는 데 사용되는 약물의 세트입니다. 예를 들어 ACE 억제제, 오피오이드, 항생제, 통증 완화제가 있습니다.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="약물 등급 엔터티의 예제입니다.":::

**MEDICATION_NAME** – 저작권 보호를 받는 브랜드 이름 및 비 브랜드 이름을 비롯한 약물 언급입니다. 예를 들어 이부프로펜이 있습니다.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="약물 이름 엔터티의 예제입니다.":::

**용량** - 주문된 약물의 양입니다. 예를 들어 염화나트륨 주입 처방 *1000mL* 가 있습니다.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="약물 용량 특성의 예제입니다.":::

**MEDICATION_FORM** - 약물의 형식입니다. 예를 들면 처방, 약, 캡슐, 정제, 패치, 겔, 연고, 포말, 스프레이, 방울, 크림, 시럽이 있습니다.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="약물 형식 특성의 예제입니다.":::

**MEDICATION_ROUTE** - 약물의 관리 방법입니다. 예를 들어 경구, 질정, IV, 경막, 국부, 토픽, 흡입이 있습니다.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="약물 경로 특성의 예제입니다.":::

## <a name="social"></a>소셜

### <a name="entities"></a>엔터티

**FAMILY_RELATION** – 주체의 친족에 관한 언급입니다. 예를 들면 아버지, 딸, 형제, 부모가 있습니다.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="스크린샷은 치료 시간 특성의 다른 예제를 보여 줍니다.":::

## <a name="treatment"></a>처리 방법

### <a name="entities"></a>엔터티

**TREATMENT_NAME** – 치료 절차입니다. 예를 들면 무릎 관절 수술, 골수 이식, TAVI, 식이가 있습니다.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="치료 이름 엔터티의 예제입니다.":::
