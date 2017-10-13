---
title: "팀 데이터 과학 프로세스 수명 주기의 고객 승인 단계 - Azure | Microsoft Docs"
description: "데이터 과학 프로젝트의 고객 승인 단계에 대한 목표, 작업 및 결과물입니다."
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>고객 승인

이 항목에서는 팀 데이터 과학 프로세스의 **고객 승인 단계**와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

* **비즈니스 이해**
* **데이터 취득 및 이해**
* **모델링**
* **배포웹사이트를**
* **고객 승인**

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP - 수명 주기 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>목표
* **프로젝트 결과물 최종 확정**: 프로덕션 환경의 파이프라인, 모델 및 배포가 고객 목표를 충족하는지 확인합니다.

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 두 가지 기본 작업은 다음과 같습니다.

* **시스템 유효성 검사**: 배포된 모델과 파이프라인이 고객 요구 사항을 충족하는지 확인합니다.
* **프로젝트 이전**: 프로덕션에서 시스템을 실행할 엔터티로 프로젝트를 이전합니다.

고객이 시스템에서 비즈니스 요구 사항을 충족하고 허용 가능한 정확도로 질문에 대답하는지 확인한 다음 클라이언트 응용 프로그램에서 사용할 수 있도록 시스템을 프로덕션에 배포합니다. 모든 문서를 완성하여 검토합니다. 운영 담당 실체에게 프로젝트를 이전합니다. 예를 들어 이 엔터티는 IT 팀, 고객 데이터 과학 팀 또는 운영 시스템을 운영할 책임이 있는 고객 담당자일 수 있습니다. 

## <a name="artifacts"></a>아티팩트
이 최종 단계에서 생성되는 주요 아티팩트는 **고객을 위한 프로젝트 종료 보고서**입니다. 이는 시스템의 학습 및 운영에 유용한 프로젝트의 모든 세부 정보를 포함하는 기술 보고서입니다. [종료 보고서](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) 템플릿은 TDSP에서 제공하며, 그대로 사용하거나 특정 클라이언트의 요구에 따라 사용자 지정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

* [1. 비즈니스 이해](lifecycle-business-understanding.md)
* [2. 데이터 취득 및 이해](lifecycle-data.md)
* [3. 모델링](lifecycle-modeling.md)
* [4. 배포](lifecycle-deployment.md)
* [5. 고객 승인](lifecycle-acceptance.md)

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 Team Data Science Process의 단계를 실행하는 예제는 [Azure ML 사용](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요.