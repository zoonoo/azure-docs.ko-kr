---
title: 'ML Studio (클래식): Azure Machine Learning로 마이그레이션 파이프라인 끝점을 사용 합니다.'
description: 파이프라인 끝점을 Azure Machine Learning의 클라이언트 응용 프로그램과 통합 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565245"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>클라이언트 응용 프로그램에서 파이프라인 끝점 사용

이 문서에서는 Azure Machine Learning 끝점과 클라이언트 응용 프로그램을 통합 하는 방법에 대해 알아봅니다. 응용 프로그램 코드를 작성 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 끝점 사용](../how-to-consume-web-service.md)을 참조 하세요.

이 문서는 Azure Machine Learning 마이그레이션 시리즈에 대 한 스튜디오 (클래식)의 일부입니다. Azure Machine Learning로 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](../how-to-manage-workspace.md#create-a-workspace).
- [Azure Machine Learning 실시간 끝점 또는 파이프라인 끝점](migrate-rebuild-web-service.md)입니다.


## <a name="consume-a-real-time-endpoint"></a>실시간 끝점 사용 

모델을 **실시간 끝점** 으로 배포한 경우 c #, Python 및 R에서 해당 REST 끝점과 미리 생성 된 소비 코드를 찾을 수 있습니다.

1. Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com))로 이동 합니다.
1. **끝점** 탭으로 이동 합니다.
1. 실시간 끝점을 선택 합니다.
1. **사용** 을 선택 합니다.

> [!NOTE]
> **세부 정보** 탭에서 끝점에 대 한 Swagger 사양을 찾을 수도 있습니다. Swagger 정의를 사용 하 여 끝점 스키마를 파악 합니다. Swagger 정의에 대 한 자세한 내용은 [swagger 공식 설명서](https://swagger.io/docs/specification/2-0/what-is-swagger/)를 참조 하세요.


## <a name="consume-a-pipeline-endpoint"></a>파이프라인 엔드포인트 사용

파이프라인 끝점을 사용 하는 방법에는 두 가지가 있습니다.

- REST API 호출
- Azure Data Factory와 통합

### <a name="use-rest-api-calls"></a>REST API 호출 사용

클라이언트 응용 프로그램에서 REST 끝점을 호출 합니다. 끝점에 대해 Swagger 사양을 사용 하 여 해당 스키마를 이해할 수 있습니다.

1. Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com))로 이동 합니다.
1. **끝점** 탭으로 이동 합니다.
1. **파이프라인 엔드포인트** 를 선택합니다.
1. 파이프라인 끝점을 선택 합니다.
1. **파이프라인 끝점 개요** 창의 **REST 끝점 설명서** 에서 링크를 선택 합니다.

### <a name="use-azure-data-factory"></a>Azure Data Factory 사용

Azure Data Factory 파이프라인의 단계로 Azure Machine Learning 파이프라인을 호출할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 Azure Machine Learning 파이프라인 실행](../../data-factory/transform-data-machine-learning-service.md)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

이 문서에서는 파이프라인 끝점에 대 한 스키마 및 샘플 코드를 찾는 방법을 배웠습니다. 클라이언트 응용 프로그램에서 끝점을 사용 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 끝점 사용](../how-to-consume-web-service.md)을 참조 하세요.

Azure Machine Learning 마이그레이션 시리즈의 나머지 문서를 참조 하세요. 
1. [마이그레이션 개요](migrate-overview.md).
1. [데이터 집합을 마이그레이션합니다](migrate-register-dataset.md).
1. [Studio (클래식) 교육 파이프라인을 다시 빌드합니다](migrate-rebuild-experiment.md).
1. [Studio (클래식) 웹 서비스를 다시 빌드합니다](migrate-rebuild-web-service.md).
1. **Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합** 합니다.
1. [R 스크립트 실행을 마이그레이션합니다](migrate-execute-r-script.md).