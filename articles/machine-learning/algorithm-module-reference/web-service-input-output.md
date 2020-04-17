---
title: 웹 서비스 입력/출력
description: Azure 기계 학습 디자이너의 웹 서비스 모듈에 대해 알아보기(미리 보기)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462088"
---
# <a name="web-service-inputoutput"></a>웹 서비스 입력/출력

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)에서 **웹 서비스 입력** 모듈 및 웹 서비스 **출력** 모듈에 대해 설명합니다.

**웹 서비스 입력** 모듈은 **DataFrameDirectory**형식을 사용하여 입력 포트에만 연결할 수 있습니다. 웹 **서비스 출력** 모듈은 **DataFrameDirectory**형식의 출력 포트에서만 연결할 수 있습니다. 두 모듈은 **웹 서비스** 범주의 모듈 트리에서 찾을 수 있습니다. 

**웹 서비스 입력** 모듈은 사용자 데이터가 파이프라인에 들어오는 위치를 나타내는 데 사용되며 **웹 서비스 출력** 모듈은 실시간 추론 파이프라인에서 사용자 데이터가 반환되는 위치를 나타내는 데 사용됩니다.

## <a name="how-to-use-web-service-inputoutput"></a>웹 서비스 입력/출력 사용 방법

- 학습 파이프라인에서 실시간 추론 파이프라인을 만들면 **웹 서비스 입력** 및 웹 서비스 **출력** 모듈이 자동으로 추가되어 사용자 데이터가 파이프라인에 들어오는 위치와 데이터가 반환되는 위치를 표시합니다. 

    [실시간 추론 파이프라인 만들기에](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)대해 자세히 알아보십시오.

    > [!NOTE]
    > 실시간 추론 파이프라인을 자동으로 생성하는 것은 규칙 기반의 최선의 노력 프로세스이며 정확성을 보장할 수 없습니다. 요구 사항을 충족하기 위해 **웹 서비스 입력/출력** 모듈을 수동으로 추가하거나 제거할 수 있습니다. 실시간 추론 파이프라인에 하나 이상의 **웹 서비스 입력** 모듈과 하나의 웹 서비스 **출력** 모듈이 있는지 확인합니다. 웹 서비스 **입력** 또는 **웹 서비스 출력** 모듈이 여러 개 있는 경우 고유한 이름이 있는지 확인하여 모듈의 오른쪽 패널에 이름을 입력할 수 있습니다.

- 제출되지 않은 파이프라인에 **웹 서비스 입력** 및 웹 서비스 **출력** 모듈을 추가하여 실시간 추론 파이프라인을 수동으로 만들 수도 있습니다.

    > [!NOTE]
    >  파이프라인 유형은 처음 제출할 때 결정됩니다. 따라서 실시간 추론 파이프라인을 만들려면 처음으로 제출하기 전에 **웹 서비스 입력** 및 웹 서비스 **출력** 모듈을 추가해야 합니다.

   다음은 **Python 스크립트 실행** 모듈에서 실시간 추론 파이프라인을 수동으로 만드는 방법을 보여 주며 예제입니다. 

   ![예제](media/module/web-service-input-output-example.png)
   
   파이프라인을 제출하고 실행이 성공적으로 완료되면 실시간 끝점을 배포할 수 있습니다.
   
   > [!NOTE]
   >  위의 예에서 **데이터 입력은** 웹 서비스 입력을 위한 데이터 스키마를 수동으로 제공하며 실시간 엔드포인트를 배포하는 데 필요합니다. 일반적으로 항상 모듈 또는 데이터 집합을 웹 **서비스 입력이** 연결된 포트에 연결하여 데이터 스키마를 제공해야 합니다.
   
## <a name="next-steps"></a>다음 단계
[실시간 엔드포인트 배포에](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)대해 자세히 알아보십시오.
Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오.