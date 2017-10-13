---
title: "팀 데이터 과학 프로세스 수명 주기의 배포 단계 - Azure | Microsoft Docs"
description: "데이터 과학 프로젝트의 배포 단계에 대한 목표, 작업 및 결과물입니다."
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>배포

이 항목에서는 팀 데이터 과학 프로세스의 **배포 단계**와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

* **비즈니스 이해**
* **데이터 취득 및 이해**
* **모델링**
* **배포웹사이트를**
* **고객 승인**

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP - 수명 주기 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>목표
* 데이터 파이프라인이 포함된 모델이 최종 사용자 승인을 위해 프로덕션 또는 이와 유사한 환경에 배포됩니다. 

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 처리되는 주요 작업은 다음과 같습니다.

* **모델 운영**: 모델 및 파이프라인을 프로덕션 또는 이와 유사한 환경에 응용 프로그램을 사용하도록 배포합니다.

### <a name="41-operationalize-a-model"></a>4.1 모델 운영
성능이 우수한 일단의 모델을 확보했으면 다른 응용 프로그램에서 사용하도록 운영할 수 있습니다. 비즈니스 요구 사항에 따라 예측을 실시간 또는 배치 방식으로 수행합니다. 모델은 개방형 API 인터페이스를 통해 공개되어 배포됩니다. 인터페이스는 모델이 여러 응용 프로그램(예: 온라인 웹 사이트, 스프레드시트, 대시보드 또는 기간 업무 및 백 엔드 응용 프로그램)에서 쉽게 사용되도록 합니다. Azure Machine Learning 웹 서비스를 사용하는 모델 운영의 예는 [Azure Machine Learning 웹 서비스 배포](../studio/publish-a-machine-learning-web-service.md)를 참조하세요. 또한 프로덕션 모델 및 배포되는 데이터 파이프라인에 원격 분석 및 모니터링을 구현하는 것이 좋습니다. 이 연습은 후속 시스템 상태 보고 및 문제 해결에 도움이 됩니다.  

## <a name="artifacts"></a>아티팩트
* 시스템 상태 대시 보드 및 주요 메트릭 상태 대시보드
* 배포 세부 정보가 포함된 최종 모델링 보고서
* 최종 솔루션 아키텍처 문서


## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

* [1. 비즈니스 이해](lifecycle-business-understanding.md)
* [2. 데이터 취득 및 이해](lifecycle-data.md)
* [3. 모델링](lifecycle-modeling.md)
* [4. 배포](lifecycle-deployment.md)
* [5. 고객 승인](lifecycle-acceptance.md)

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 Team Data Science Process의 단계를 실행하는 예제는 [Azure ML 사용](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요.