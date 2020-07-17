---
title: '웹 서비스 입력/출력: 모듈 참조'
description: Azure Machine Learning 디자이너의 웹 서비스 모듈에 대 한 자세한 내용 (미리 보기)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: ded976fc9090535f3c683b5c6351646a55265205
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84751113"
---
# <a name="web-service-input-and-web-service-output-modules"></a>웹 서비스 입력 및 웹 서비스 출력 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 웹 서비스 입력 및 웹 서비스 출력 모듈을 설명 합니다.

웹 서비스 입력 모듈은 **DataFrameDirectory**형식의 입력 포트에만 연결할 수 있습니다. 웹 서비스 출력 모듈은 **DataFrameDirectory**형식의 출력 포트 에서만 연결할 수 있습니다. 모듈 트리의 **웹 서비스** 범주 아래에서 두 모듈을 찾을 수 있습니다. 

웹 서비스 입력 모듈은 사용자 데이터가 파이프라인으로 들어가는 위치를 나타냅니다. 웹 서비스 출력 모듈은 실시간 유추 파이프라인에서 사용자 데이터가 반환 되는 위치를 나타냅니다.

## <a name="how-to-use-web-service-input-and-output"></a>웹 서비스 입력 및 출력을 사용 하는 방법

학습 파이프라인에서 [실시간 유추 파이프라인을 만들](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline) 때 웹 서비스 입력 및 웹 서비스 출력 모듈은 사용자 데이터가 파이프라인으로 들어오고 데이터가 반환 되는 위치를 표시 하기 위해 자동으로 추가 됩니다. 

> [!NOTE]
> 실시간 유추 파이프라인을 자동으로 생성 하는 작업은 규칙 기반의 최상의 프로세스입니다. 정확성은 보장 되지 않습니다. 

웹 서비스 입력 및 웹 서비스 출력 모듈을 수동으로 추가 하거나 제거 하 여 요구 사항을 충족할 수 있습니다. 실시간 유추 파이프라인에 하나 이상의 웹 서비스 입력 모듈과 하나의 웹 서비스 출력 모듈이 있는지 확인 합니다. 웹 서비스 입력 또는 웹 서비스 출력 모듈이 여러 개인 경우에는 고유한 이름을 사용 해야 합니다. 모듈의 오른쪽 패널에 이름을 입력할 수 있습니다.

웹 서비스 입력 및 웹 서비스 출력 모듈을 제출 되지 않은 파이프라인에 추가 하 여 실시간 유추 파이프라인을 수동으로 만들 수도 있습니다.

> [!NOTE]
> 파이프라인 형식은 처음 전송할 때 결정 됩니다. 처음으로 제출 하기 전에 웹 서비스 입력 및 웹 서비스 출력 모듈을 추가 해야 합니다.

다음 예제에서는 Python 스크립트 실행 모듈에서 실시간 유추 파이프라인을 수동으로 만드는 방법을 보여 줍니다. 

![예제](media/module/web-service-input-output-example.png)
   
파이프라인을 제출 하 고 실행이 성공적으로 완료 되 면 실시간 끝점을 배포할 수 있습니다.
   
> [!NOTE]
>  위의 예제에서 **데이터 입력** 은 웹 서비스 입력을 위한 데이터 스키마를 수동으로 제공 하며 실시간 끝점을 배포 하는 데 필요 합니다. 일반적으로 데이터 스키마를 제공 하기 위해 **웹 서비스 입력** 이 연결 된 포트에는 항상 모듈 또는 데이터 집합을 연결 해야 합니다.
   
## <a name="next-steps"></a>다음 단계
[실시간 끝점 배포](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)에 대해 자세히 알아보세요.

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요.