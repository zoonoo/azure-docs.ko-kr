---
title: 'ML Studio (클래식): Azure Machine Learning로 마이그레이션-다시 빌드 실험'
description: Azure Machine Learning designer에서 Studio (클래식) 실험을 다시 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565168"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Azure Machine Learning에서 Studio (클래식) 실험 다시 빌드

이 문서에서는 Azure Machine Learning에서 Studio (클래식) 실험을 다시 빌드하는 방법에 대해 알아봅니다. Studio (클래식)에서 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조 하세요.

Studio (클래식) **실험** 은 Azure Machine Learning **파이프라인** 과 비슷합니다. 그러나 Azure Machine Learning 파이프라인은 SDK를 구동 하는 동일한 백 엔드를 기반으로 합니다. 즉, 기계 학습 개발을 위한 두 가지 옵션, 즉 끌어서 놓기 디자이너나 코드 우선 Sdk가 있습니다.

SDK를 사용 하 여 파이프라인을 빌드하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 파이프라인 이란?](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk)을 참조 하세요.


## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](../how-to-manage-workspace.md#create-a-workspace).
- 마이그레이션할 Studio (클래식) 실험입니다.
- [데이터 집합](migrate-register-dataset.md) 을 Azure Machine Learning에 업로드 합니다.

## <a name="rebuild-the-pipeline"></a>파이프라인 다시 빌드

[데이터 집합을 Azure Machine Learning로 마이그레이션한](migrate-register-dataset.md)후 실험을 다시 만들 준비가 되었습니다.

Azure Machine Learning에서 시각적 그래프를 **파이프라인 초안** 이라고 합니다. 이 섹션에서는 클래식 실험을 파이프라인 초안으로 다시 만듭니다.

1. Azure Machine Learning studio로 이동 ([ml.azure.com](https://ml.azure.com))
1. 왼쪽 탐색 창에서 **디자이너** 에서 > **사용 하기 쉬운 미리** 작성 된 모듈 스크린샷을 선택 하 여 ![ 새 파이프라인 초안을 만드는 방법을 보여 줍니다.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. 디자이너 모듈로 실험을 수동으로 다시 빌드합니다.
    
    [모듈 매핑 표](migrate-overview.md#studio-classic-and-designer-module-mapping) 를 참조 하 여 대체 모듈을 찾습니다. 대부분의 스튜디오 (클래식) 모듈은 디자이너에서 동일한 버전을 사용 합니다.

    > [!Important]
    > 실험에서 R 스크립트 실행 모듈을 사용 하는 경우 실험을 마이그레이션하기 위한 추가 단계를 수행 해야 합니다. 자세한 내용은 [R 스크립트 모듈 마이그레이션](migrate-execute-r-script.md)을 참조 하세요.

1. 매개 변수를 조정 합니다.
    
    각 모듈을 선택 하 고 모듈 설정 패널에서 오른쪽으로 매개 변수를 조정 합니다. 매개 변수를 사용 하 여 Studio (클래식) 실험의 기능을 다시 만듭니다. 각 모듈에 대 한 자세한 내용은 [모듈 참조](../algorithm-module-reference/module-reference.md)를 참조 하세요.

## <a name="submit-a-run-and-check-results"></a>실행 및 검사 결과 제출

Studio (클래식) 실험을 다시 만든 후에는 **파이프라인 실행** 을 제출할 시간입니다.

파이프라인 실행은 작업 영역에 연결 된 **계산 대상** 에서 실행 됩니다. 전체 파이프라인에 대 한 기본 계산 대상을 설정 하거나 모듈 단위로 계산 대상을 지정할 수 있습니다.

파이프라인 초안에서 실행을 제출 하면 **파이프라인 실행** 으로 전환 됩니다. 각 파이프라인 실행은 기록 되 고 Azure Machine Learning 로그인 됩니다.

전체 파이프라인에 대 한 기본 계산 대상을 설정 하려면 다음을 수행 합니다.
1.  ![ 디자이너에서 파이프라인 이름 옆의 기어 아이콘 기어 아이콘 ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) 을 선택 합니다.
1. **계산 대상 선택** 을 선택 합니다.
1. 기존 계산을 선택 하거나 화면의 지시에 따라 새 계산을 만듭니다.

이제 계산 대상이 설정 되었으므로 파이프라인 실행을 제출할 수 있습니다.

1. 캔버스 위쪽에서 **제출** 을 선택합니다.
1. 새로 **만들기** 를 선택 하 여 새 실험을 만듭니다.
    
    실험은 비슷한 파이프라인을 함께 실행 합니다. 파이프라인을 여러 번 실행하는 경우 연속 실행에 대해 동일한 실험을 선택할 수 있습니다. 이는 로깅 및 추적에 유용 합니다.
1. 실험 이름을 입력 합니다. 그런 다음 **제출** 을 선택 합니다.

    첫 번째 실행은 20 분 정도 걸릴 수 있습니다. 기본 계산 설정의 최소 노드 크기는 0 이므로 디자이너는 유휴 상태가 된 후에 리소스를 할당 해야 합니다. 노드가 이미 할당 되었으므로 연속 실행은 시간이 더 짧습니다. 실행 시간을 단축 하기 위해 최소 노드 크기인 1 이상의 계산 리소스를 만들 수 있습니다.

실행이 완료 되 면 각 모듈의 결과를 확인할 수 있습니다.

1. 출력을 보려는 모듈을 마우스 오른쪽 단추로 클릭 합니다.
1. **시각화**, **출력 보기** 또는 **로그 보기** 중 하나를 선택 합니다.

    - **시각화**: 결과 데이터 집합을 미리 봅니다.
    - **출력 보기**: 출력 저장소 위치에 대 한 링크를 엽니다. 이를 사용 하 여 출력을 탐색 하거나 다운로드 합니다. 
    - **로그 보기**: 드라이버 및 시스템 로그를 확인 합니다. **70_driver_log** 를 사용 하 여 오류 및 예외와 같은 사용자 제출 스크립트와 관련 된 정보를 볼 수 있습니다.

> [!IMPORTANT]
> 디자이너 모듈은 Studio (클래식)의 c # 패키지와 비교 하 여 오픈 소스 Python 패키지를 사용 합니다. 따라서 모듈 출력은 디자이너와 Studio (클래식) 사이에서 약간 다를 수 있습니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning에서 Studio (클래식) 실험을 다시 빌드하는 방법에 대해 알아보았습니다. 다음 단계는 [Azure Machine Learning에서 웹 서비스를 다시 작성](migrate-rebuild-web-service.md)하는 것입니다.


Studio (클래식) 마이그레이션 시리즈의 다른 문서를 참조 하세요.

1. [마이그레이션 개요](migrate-overview.md).
1. [데이터 집합을 마이그레이션합니다](migrate-register-dataset.md).
1. **Studio (클래식) 교육 파이프라인을 다시 빌드합니다**.
1. [Studio (클래식) 웹 서비스를 다시 빌드합니다](migrate-rebuild-web-service.md).
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션합니다](migrate-execute-r-script.md).
