---
title: "팀 데이터 과학 프로세스 수명 주기의 데이터 취득 및 이해 단계 - Azure | Microsoft Docs"
description: "데이터 과학 프로젝트의 데이터 취득 및 이해 단계에 대한 목표, 작업 및 결과물입니다."
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
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>데이터 취득 및 이해

이 항목에서는 팀 데이터 과학 프로세스의 **데이터 취득 및 이해 단계**와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

* **비즈니스 이해**
* **데이터 취득 및 이해**
* **모델링**
* **배포웹사이트를**
* **고객 승인**

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP - 수명 주기 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>목표
* 대상 변수와의 관계가 이해되고 모델링할 준비가 된 적절한 분석 환경에 있는 깨끗한 고품질 데이터 집합
* 데이터를 정기적으로 새로 고치고 점수를 매기도록 개발된 데이터 파이프라인의 솔루션 아키텍처

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 세 가지 기본 작업은 다음과 같습니다.

* **데이터를 대상 분석 환경에 수집**합니다.
* **데이터를 탐색**하여 데이터 품질이 질문에 대답하기에 적합한지 판단합니다. 
* **데이터 파이프라인을 설정**하여 새롭거나 정기적으로 새로 고쳐지는 데이터에 대해 점수를 매깁니다.

### <a name="21-ingest-the-data"></a>2.1 데이터 수집
원본 위치에서 분석 작업(예: 학습, 예측)을 실행할 대상 위치로 데이터를 이동하는 프로세스를 설정합니다. 다양한 Azure 데이터 서비스에서 이 작업을 수행할 수 있는 기술 세부 정보 및 옵션에 대해서는 [분석을 위해 저장소 환경에 데이터로드 ](ingest-data.md)를 참조하세요. 

### <a name="22-explore-the-data"></a>2.2 데이터 탐색
모델을 학습하기 전에 데이터에 대한 적절한 이해를 개발해야 합니다. 실제 데이터 집합은 종종 난해하거나 가치가 없거나 상당히 일치하지 않습니다. 데이터를 모델링할 준비가 되기 전에 데이터 요약 및 시각화를 사용하여 데이터 품질을 평가하고, 처리하는 데 필요한 정보를 제공할 수 있습니다. 이 프로세스는 종종 반복적입니다.

TDSP는 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)라고 하는 자동화 유틸리티를 제공하여 데이터를 시각화하고 데이터 요약 보고서를 준비합니다. 먼저 IDEAR로 시작하여 코딩 없이 대화식으로 초기 데이터를 이해할 수 있도록 개발하여 데이터를 탐색한 다음 데이터 탐색 및 시각화를 위한 사용자 지정 코드를 작성하는 것이 좋습니다. 데이터 정리에 대한 지침은 [향상된 기계 학습을 위한 데이터 준비 작업](prepare-data.md)을 참조하세요.  

정리된 데이터의 품질에 만족했으면 다음 단계는 데이터에 내재된 패턴을 더 잘 이해하여 대상에 적합한 예측 모델을 선택하고 개발하는 데 도움이 됩니다. 데이터가 대상에 얼마나 잘 연결되어 있는지와 다음 모델링 단계로 진행할 만큼 충분한 데이터가 있는지를 확인합니다. 다시금 말하지만 이 프로세스는 종종 반복적입니다. 이전 단계에서 처음 식별된 데이터 집합을 보강하기 위해 더 정확하거나 관련성이 더 높은 데이터로 새 데이터 원본을 찾아야 할 수도 있습니다.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 데이터 파이프라인 설정
데이터의 초기 수집 및 정리 외에도 일반적으로 지속적인 학습 프로세스의 일부로 새 데이터에 점수를 매기거나 정기적으로 데이터를 새로 고치는 프로세스를 설정해야 합니다. 데이터 파이프라인 또는 워크플로를 설정하여 이 작업을 수행할 수 있습니다. 여기에는 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)를 사용하여 파이프라인을 설정하는 방법에 대한 [예제](move-sql-azure-adf.md)가 있습니다. 

이 단계에서 데이터 파이프라인의 솔루션 아키텍처가 개발됩니다. 또한 파이프라인은 데이터 과학 프로젝트의 다음 단계와 병행하여 개발되며, 비즈니스 요구 사항 및 이 솔루션이 통합되는 기존 시스템의 제약 사항에 따라 배치 기반, 스트리밍/실시간 또는 하이브리드 파이프라인일 수 있습니다. 

## <a name="artifacts"></a>아티팩트
이 단계의 결과물은 다음과 같습니다.

* [**데이터 품질 보고서**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): 이 보고서에는 데이터 요약, 각 속성과 대상 간의 관계, 변수 순위 등이 포함됩니다. TDSP의 일부로 제공되는 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 도구는 CSV 파일이나 관계형 테이블과 같이 테이블 형식의 데이터 집합에서 이 보고서를 신속하게 생성할 수 있습니다. 
* **솔루션 아키텍처**: 모델을 만든 후에는 새로운 데이터에 대한 점수 매기기 또는 예측을 실행하는 데 사용되는 데이터 파이프라인의 다이어그램 또는 설명이 될 수 있습니다. 또한 새로운 데이터에 기반하여 모델을 다시 학습하는 파이프라인도 포함되어 있습니다. 이 문서는 TDSP 디렉터리 구조 템플릿을 사용할 때 [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) 디렉터리에 저장됩니다.
* **검사점 의사 결정**: 전체 기능 엔지니어링 및 모델 빌드를 시작하기 전에 프로젝트를 계속 추구할 충분한 예상 가치가 있는지 결정하기 위해 프로젝트를 다시 평가할 수 있습니다. 예를 들어 계속 진행할 준비가 되었거나, 더 많은 데이터를 수집해야 하거나, 질문에 대답하기 위한 데이터가 존재하지 않아 프로젝트를 중단할 수 있습니다.

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

* [1. 비즈니스 이해](lifecycle-business-understanding.md)
* [2. 데이터 취득 및 이해](lifecycle-data.md)
* [3. 모델링](lifecycle-modeling.md)
* [4. 배포](lifecycle-deployment.md)
* [5. 고객 승인](lifecycle-acceptance.md)

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다.  

Azure Machine Learning Studio를 사용하는 Team Data Science Process의 단계를 실행하는 예제는 [Azure ML 사용](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요.