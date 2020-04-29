---
title: 웹 서비스 입력/출력
description: Azure Machine Learning 디자이너의 웹 서비스 모듈에 대 한 자세한 내용 (미리 보기)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462088"
---
# <a name="web-service-inputoutput"></a>웹 서비스 입력/출력

이 문서에서는 Azure Machine Learning designer (미리 보기)의 **웹 서비스 입력** 모듈 및 **웹 서비스 출력** 모듈을 설명 합니다.

**웹 서비스 입력** 모듈은 **DataFrameDirectory**형식의 입력 포트에만 연결할 수 있습니다. 및 **웹 서비스 출력** 모듈은 **DataFrameDirectory**유형의 출력 포트 에서만 연결할 수 있습니다. 두 모듈은 **웹 서비스** 범주 아래의 모듈 트리에서 찾을 수 있습니다. 

**웹 서비스 입력** 모듈은 사용자 데이터가 파이프라인에 입력 되는 위치를 나타내는 데 사용 되 고 **웹 서비스 출력** 모듈은 사용자 데이터가 실시간 유추 파이프라인에서 반환 되는 위치를 나타내는 데 사용 됩니다.

## <a name="how-to-use-web-service-inputoutput"></a>웹 서비스 입력/출력을 사용 하는 방법

- 학습 파이프라인에서 실시간 유추 파이프라인을 만들 때 **웹 서비스 입력** 및 **웹 서비스 출력** 모듈은 사용자 데이터가 파이프라인으로 들어오고 데이터가 반환 되는 위치를 표시 하기 위해 자동으로 추가 됩니다. 

    [실시간 유추 파이프라인 만들기](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)에 대해 자세히 알아보세요.

    > [!NOTE]
    > 실시간 유추 파이프라인을 자동으로 생성 하는 것은 규칙 기반 최상의 프로세스 이며 정확성에 대 한 보장은 없습니다. **웹 서비스 입력/출력** 모듈을 수동으로 추가 하거나 제거 하 여 요구 사항을 충족할 수 있습니다. 실시간 유추 파이프라인에 하나 이상의 **웹 서비스 입력** 모듈과 **웹 서비스 출력** 모듈이 있는지 확인 합니다. **웹 서비스 입력** 또는 **웹 서비스 출력** 모듈이 여러 개 있는 경우 모듈의 오른쪽 패널에 이름을 입력할 수 있는 고유한 이름이 있는지 확인 합니다.

- **웹 서비스 입력** 및 **웹 서비스 출력** 모듈을 제출 되지 않은 파이프라인에 추가 하 여 실시간 유추 파이프라인을 수동으로 만들 수도 있습니다.

    > [!NOTE]
    >  파이프라인 형식은 처음 전송할 때 결정 됩니다. 따라서 실시간 유추 파이프라인을 만들 때 처음으로 제출 하기 전에 **웹 서비스 입력** 및 **웹 서비스 출력** 모듈을 추가 해야 합니다.

   아래 예제에서는 **Python 스크립트 실행** 모듈에서 실시간 유추 파이프라인을 수동으로 만드는 방법을 보여 줍니다. 

   ![예제](media/module/web-service-input-output-example.png)
   
   파이프라인을 제출 하 고 실행이 성공적으로 완료 되 면 실시간 끝점을 배포할 수 있습니다.
   
   > [!NOTE]
   >  위의 예제에서 **데이터 입력** 은 웹 서비스 입력을 위한 데이터 스키마를 수동으로 제공 하며 실시간 끝점을 배포 하는 데 필요 합니다. 일반적으로 데이터 스키마를 제공 하기 위해 **웹 서비스 입력** 이 연결 된 포트에 모듈 또는 데이터 집합을 항상 연결 해야 합니다.
   
## <a name="next-steps"></a>다음 단계
[실시간 끝점 배포](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)에 대해 자세히 알아보세요.
Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요.