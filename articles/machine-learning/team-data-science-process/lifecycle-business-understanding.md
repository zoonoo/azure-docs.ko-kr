---
title: "팀 데이터 과학 프로세스 수명 주기의 비즈니스 이해 단계 - Azure | Microsoft Docs"
description: "데이터 과학 프로젝트의 비즈니스 이해 단계에 대한 목표, 작업 및 결과물입니다."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="business-understanding"></a>비즈니스 이해

이 항목에서는 팀 데이터 과학 프로세스의 **비즈니스 이해 단계**와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 프로젝트에서 일반적으로, 종종 반복적으로 실행하는 주요 단계를 설명합니다.

* **비즈니스 이해**
* **데이터 취득 및 이해**
* **모델링**
* **배포웹사이트를**
* **고객 승인**

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP-수명 주기2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>목표
* **주요 변수**: **모델 대상**의 역할을 하며 관련 메트릭을 사용하여 프로젝트의 성공 여부를 결정합니다.
* 비즈니스가 액세스할 수 있거나 가져와야 하는 관련 **데이터 원본**이 식별됩니다.

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 두 가지 기본 작업은 다음과 같습니다. 

* **목표 정의**: 고객 및 기타 이해 관계자와 협력하여 비즈니스 문제를 이해하고 파악합니다. 비즈니스 목표를 정의하고 데이터 과학 기술이 목표로 삼을 수 있는 질문을 작성합니다.
* **데이터 원본 식별**: 프로젝트 목표를 정의하는 질문에 답하는 데 도움이 되는 관련 데이터를 찾습니다.

### <a name="11-define-objectives"></a>1.1 목표 정의

1. 이 단계의 주요 목적은 분석에서 예측해야 하는 주요 **비즈니스 변수**를 식별하는 것입니다. 이러한 변수를 **모델 대상**이라고 하며, 관련 메트릭을 사용하여 프로젝트의 성공 여부를 결정합니다. 판매 예측이나 사기성 주문의 확률이 이러한 대상의 두 가지 예입니다.

2. 적절하고 명확하며 모호하지 않은 "예리한" 질문을 요청하고 수정하여 **프로젝트 목표**를 정의합니다. 데이터 과학은 이러한 질문에 대답하기 위해 이름과 숫자를 사용하는 프로세스입니다. 날카로운 질문하기에 대한 자세한 내용은 [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/)(데이터 과학 수행 방법) 블로그를 참조하세요. 데이터 과학/기계 학습은 일반적으로 다음과 같은 다섯 가지 유형의 질문에 대답하는 데 사용됩니다.
 
   * 양 또는 개수는 얼마인가요? (회귀)
   * 어떤 범주? (분류)
   * 어떤 그룹? (클러스터링)
   * 이것은 이상한가요? (이상 감지)
   * 수행해야 할 옵션은? (권장)

    이러한 질문 중 어떤 질문을 하고 비즈니스 목표 달성을 위해 질문에 어떻게 대답할지를 결정합니다.

3. 구성원의 역할과 책임을 지정하여 **프로젝트 팀**을 정의합니다. 더 많은 정보를 찾아내면 반복하는 높은 수준의 획기적인 계획을 개발합니다.  

4. **성공 메트릭 정의**를 수행합니다. 예를 들어 이번 3개월 프로젝트가 끝날 때까지 X% 고객 이탈 예측 정확도에 도달하면 이탈을 줄이기 위한 프로모션을 제공할 수 있습니다. 메트릭은 **SMART**여야 합니다. 
   * **S**pecific(특정) 
   * **M**easurable(측정 가능)
   * **A**chievable(달성 가능) 
   * **R**elevant(관련성) 
   * **T**ime-bound(시간 제한) 

### <a name="12-identify-data-sources"></a>1.2 데이터 원본 식별
예리한 질문에 대한 답변의 알려진 예가 들어있는 데이터 원본을 확인합니다. 다음 데이터를 찾습니다.

* 질문과 **관련된** 데이터입니다. 대상과 관련된 측정값과 기능이 있습니까?
* 모델 대상 및 관련 기능의 **정확한 측정값** 데이터입니다.

예를 들어 문제를 해결하고 프로젝트 목표를 달성하기 위해 다른 종류의 데이터를 더 수집하고 기록해야 한다고 확인되는 경우는 일반적으로 많지 않습니다. 이 경우 외부 데이터 원본을 찾거나 새 데이터를 수집하도록 시스템을 업데이트할 수 있습니다.

## <a name="artifacts"></a>아티팩트
이 단계의 결과물은 다음과 같습니다.

* [**선언 문서**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): TDSP 프로젝트 구조 정의에서 표준 템플릿을 제공하며, 새로운 검색과 변화하는 비즈니스 요구 사항에 따라 프로젝트 전체에서 업데이트되는 실시간 문서입니다. 검색 프로세스를 진행하면서 이 문서에서 반복적으로 더 자세히 추가하는 것이 핵심입니다. 고객 및 기타 이해 관계자가 변경 작업을 수행하도록 만들고 변경 이유를 명확하게 전달해야 합니다.  
* [**데이터 원본**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): TDSP 프로젝트 **데이터 보고서** 폴더에 있는 **데이터 정의** 보고서의 **원시 데이터 원본** 섹션입니다. 원시 데이터의 원본 및 대상 위치를 지정합니다. 이후 단계에서 추가 세부 정보(예: 스크립트)를 입력하여 데이터를 분석 환경으로 옮깁니다.  
* [**데이터 사전**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): 이 문서는 클라이언트가 제공하는 데이터를 설명합니다. 이러한 설명에는 스키마(데이터 형식, 유효성 검사 규칙(있는 경우)에 대한 정보) 및 엔터티-관계 다이어그램(해당되는 경우)에 대한 정보가 포함됩니다.

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

* [1. 비즈니스 이해](lifecycle-business-understanding.md)
* [2. 데이터 취득 및 이해](lifecycle-data.md)
* [3. 모델링](lifecycle-modeling.md)
* [4. 배포](lifecycle-deployment.md)
* [5. 고객 승인](lifecycle-acceptance.md)

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계들은 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 팀 데이터 과학 프로세스의 단계를 실행하는 예제는 [Azure ML을 사용하여](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요.
