---
title: "팀 데이터 과학 프로세스 수명 주기 - Azure | Microsoft Docs"
description: "데이터 과학 프로젝트를 실행하는 데 필요한 단계입니다."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-lifecycle"></a>팀 데이터 과학 프로세스 수명 주기

TDSP(Team Data Science Process)는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트가 실행될 때 시작부터 끝까지 따라야 하는 단계를 간략하게 설명합니다. 다른 데이터 과학의 수명 주기(예: [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) 또는 조직의 사용자 지정 프로세스)를 사용하는 경우 이러한 개발 수명 주기로 작업 기반 TDSP를 계속 사용할 수 있습니다 . 

이 수명 주기는 지능형 응용 프로그램의 일부로 제공되는 데이터 과학 프로젝트를 위해 설계되었습니다. 이러한 응용 프로그램은 예측 분석을 위해 기계 학습 또는 인공 지능 모델을 배포합니다. 예비 데이터 과학 프로젝트 및 임시 분석 프로젝트에서 이 프로세스를 사용하여 활용할 수도 있습니다. 하지만 이러한 경우 설명된 몇 가지 단계가 필요하지 않습니다.    

TDSP 수명 주기는 반복적으로 실행되는 5가지 주요 단계로 구성됩니다. 내용은 다음과 같습니다.

* [1.비즈니스 이해](lifecycle-business-understanding.md)
* [2.데이터 취득 및 이해](lifecycle-data.md)
* [3.모델링](lifecycle-modeling.md)
* [4.배포](lifecycle-deployment.md)
* [5.고객 승인](lifecycle-acceptance.md)

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


팀 데이터 과학 프로세스 수명 주기는 예측 모델을 사용하는 데 필요한 작업에 대한 지침을 제공하는 일련의 반복 단계로 모델링됩니다. 이러한 모델을 프로덕션 환경에 배포하여 지능형 응용 프로그램을 구현하는 데 활용할 수 있습니다. 이 프로세스 수명 주기의 목표는 명확한 약속 종점을 향해 데이터 과학 프로젝트를 계속 이동해 가는 것입니다. 데이터 과학이 연구 및 발견에 있어 실용적인 것은 사실입니다. 그렇지만 직원이 템플릿을 표준화한 잘 정의된 아티팩트 집합을 사용하여 팀과 고객에게 이러한 작업을 명확하게 알릴 수 있다면 오해를 피하는데 도움이 되고 복잡한 데이터 과학 프로젝트를 성공적으로 완료할 수 있는 가능성을 높일 수 있습니다.

각 단계마다 다음 정보를 제공합니다.

* **목표**: 특정 목표입니다.
* **수행 방법**: 설명된 특정 작업 및 지침을 수행하기 위해 제공됩니다.
* **아티팩트**: 결과물 및 이를 제작하기 위한 지원입니다.

## <a name="next-steps"></a>다음 단계
**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 Team Data Science Process의 단계를 실행하는 예제는 [Azure ML 사용](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요.

