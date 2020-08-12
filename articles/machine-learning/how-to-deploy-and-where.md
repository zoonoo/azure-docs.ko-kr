---
title: 모델을 배포 하는 방법 및 위치
titleSuffix: Azure Machine Learning
description: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 필드 프로그래밍 가능 게이트 배열을 포함 하 여 Azure Machine Learning 모델을 배포 하는 방법 및 위치를 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 439bf51aac4d04ee12968588a4e38c4643b4808a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120351"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 클라우드의 웹 서비스로 machine learning 모델을 배포 하거나 장치를 Azure IoT Edge 하는 방법에 대해 알아봅니다.

워크플로는 모델을 배포하는 위치와 관계없이 유사합니다.

1. 모델을 등록합니다.
1. 유추 구성 준비
1. [코드 배포](how-to-deploy-no-code-deployment.md)를 사용 하지 않는 경우 항목 스크립트 준비
1. 컴퓨팅 대상에 모델을 배포합니다.
1. 웹 서비스 라고도 하는 배포 된 모델을 테스트 합니다.

배포 워크플로와 관련 된 개념에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조 하세요.


::: zone pivot="py-sdk"

[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]

::: zone-end

::: zone pivot="cli"

[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]

::: zone-end

### <a name="understanding-service-state"></a>서비스 상태 이해

모델을 배포 하는 동안 완전히 배포 되는 동안 서비스 상태 변경이 표시 될 수 있습니다.

다음 표에서는 다양 한 서비스 상태에 대해 설명 합니다.

| 웹 서비스 상태 | Description | 최종 상태?
| ----- | ----- | ----- |
| 변환은 | 서비스의 배포를 진행 중입니다. | 아니요 |
| Unhealthy | 서비스가 배포 되었지만 현재 연결할 수 없습니다.  | 아니요 |
| 예약 불가능 | 리소스가 부족 하 여 지금은 서비스를 배포할 수 없습니다. | 아니요 |
| 실패 | 오류 또는 충돌 때문에 서비스를 배포 하지 못했습니다. | 예 |
| 정상 | 서비스가 정상 상태 이며 끝점을 사용할 수 있습니다. | 예 |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>일괄 처리 유추
Azure Machine Learning Azure Machine Learning 계산 대상이 만들어지고 관리 됩니다. Azure Machine Learning 파이프라인에서 일괄 처리 예측에 사용할 수 있습니다.

Azure Machine Learning 계산을 사용한 일괄 처리 유추 연습은 [일괄 처리를 실행 하는 방법](tutorial-pipeline-batch-scoring-classification.md)을 참조 하세요.

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge 유추
에 지에 배포 하는 기능은 미리 보기 상태입니다. 자세한 내용은 [Azure Machine Learning를 IoT Edge 모듈로 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대 한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)

