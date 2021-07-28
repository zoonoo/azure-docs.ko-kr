---
title: '웹 서비스 입출력: 모듈 참조'
description: Azure Machine Learning 디자이너에서 웹 서비스 모듈을 사용하여 입력 및 출력을 관리하는 방법을 알아봅니다.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421127"
---
# <a name="web-service-input-and-web-service-output-modules"></a>웹 서비스 입력 및 웹 서비스 출력 모듈

이 문서에서는 Azure Machine Learning 디자이너의 웹 서비스 입력 및 웹 서비스 출력 모듈에 관해 설명합니다.

웹 서비스 입력 모듈은 **DataFrameDirectory** 형식의 입력 포트로만 연결할 수 있습니다. 웹 서비스 출력 모듈은 **DataFrameDirectory** 형식의 출력 포트에서만 연결할 수 있습니다. 모듈 트리의 **웹 서비스** 범주 아래에서 두 모듈을 찾을 수 있습니다. 

웹 서비스 입력 모듈은 사용자 데이터가 파이프라인으로 들어가는 위치를 나타냅니다. 웹 서비스 출력 모듈은 실시간 유추 파이프라인에서 사용자 데이터가 반환되는 위치를 나타냅니다.

## <a name="how-to-use-web-service-input-and-output"></a>웹 서비스 입력 및 출력을 사용하는 방법

학습 파이프라인에서 [실시간 유추 파이프라인을 만들](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) 때 웹 서비스 입력 및 웹 서비스 출력 모듈은 사용자 데이터가 파이프라인으로 들어오고 데이터가 반환되는 위치를 표시하기 위해 자동으로 추가됩니다. 

> [!NOTE]
> 실시간 유추 파이프라인의 자동 생성은 규칙 기반의 최상의 프로세스입니다. 정확성은 보장되지 않습니다. 

웹 서비스 입력 및 웹 서비스 출력 모듈을 수동으로 추가하거나 제거하여 요구 사항을 충족할 수 있습니다. 실시간 유추 파이프라인에 하나 이상의 웹 서비스 입력 모듈과 하나의 웹 서비스 출력 모듈이 있는지 확인합니다. 웹 서비스 입력 또는 웹 서비스 출력 모듈이 여러 개인 경우에는 고유한 이름을 사용해야 합니다. 모듈의 오른쪽 패널에 이름을 입력할 수 있습니다.

웹 서비스 입력 및 웹 서비스 출력 모듈이 제출되지 않은 파이프라인에 추가하여 실시간 유추 파이프라인을 수동으로 만들 수도 있습니다.

> [!NOTE]
> 파이프라인 형식은 처음 제출할 때 결정됩니다. 처음으로 제출하기 전에 웹 서비스 입력 및 웹 서비스 출력 모듈을 추가해야 합니다.

다음 예제에서는 Python 스크립트 실행 모듈에서 실시간 유추 파이프라인을 수동으로 만드는 방법을 보여 줍니다. 

![예제](media/module/web-service-input-output-example.png)
   
파이프라인을 제출하고 실행이 성공적으로 완료되면 실시간 엔드포인트를 배포할 수 있습니다.
   
> [!NOTE]
>  앞의 예제에서 **수동으로 데이터 입력** 은 웹 서비스 입력을 위한 데이터 스키마를 제공하며 실시간 엔드포인트를 배포하는 데 필요합니다. 일반적으로 데이터 스키마를 제공하기 위해서는 **웹 서비스 입력** 이 연결된 포트에 항상 모듈 또는 데이터 세트를 연결해야 합니다.
   
## <a name="next-steps"></a>다음 단계
[실시간 엔드포인트 배포](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint)에 대해 자세히 알아보세요.

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요.