---
title: 'MLOps: 관리, 배포 및 ML 모델 모니터링'
titleSuffix: Azure Machine Learning service
description: 'MLOps에 대 한 Azure Machine Learning 서비스를 사용 하는 방법 알아보기: 배포, 관리 및 지속적으로 개선 하려면 모델을 모니터링 합니다. Azure Machine Learning Service를 사용하여 학습시킨 모델을 로컬 컴퓨터 또는 다른 원본에서 배포할 수 있습니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: d02318db4deae6f0a6acdae9382ce47116c95110
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800862"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링

이 문서에서는 Azure Machine Learning Service를 통해 모델을 배포, 관리 및 모니터링하여 지속적으로 개선하는 방법을 알아볼 수 있습니다. Azure Machine Learning을 사용하여 학습한 모델을 로컬 머신 또는 다른 원본에서 배포할 수 있습니다. 

다음 다이어그램은 전체 배포 워크플로를 보여 줍니다. [![Azure Machine Learning에 대 한 배포 워크플로](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

MLOps 배포 워크플로 다음 단계를 포함 하는 /:
1. Azure Machine Learning Service 작업 영역에서 호스팅되는 레지스트리에 **모델 등록**
1. **사용 하 여** 클라우드, IoT 장치에서 Power BI를 사용 하 여 분석에 대 한 웹 서비스에서 모델입니다.
1. **모니터링 및 데이터 수집**
1. 새 이미지를 사용하도록 배포를 **업데이트**합니다.

단일 명령의 일부로 또는 독립적으로 각 단계를 수행할 수 있습니다. 또한 만들 수는 **CI/CD 워크플로에서** 이 그래픽에서 볼 수 있듯이 합니다.

[![' Azure Machine Learning 지속적인 통합/지속적인 배포 (CI/CD) 주기 '](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="step-1-register-model"></a>1단계: 모델 등록

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.

> [!TIP]
> 또한 Azure Machine Learning 서비스 외부에서 학습 모델을 등록할 수 있습니다.
 
등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 또한 모델을 검색할 때 사용할 수 있는 추가 메타데이터 태그를 등록 중에 제공할 수도 있습니다. Azure Machine Learning 서비스는 Python 3.5.2를 사용 하 여 로드 이상이 될 수 있는 모든 모델을 지원 합니다.

활성 배포에서 사용 되는 모델을 삭제할 수 없습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

pickle 형식으로 저장된 모델을 등록하는 예제는 [자습서: 이미지 분류 모델 학습시키기](tutorial-deploy-models-with-aml.md)를 참조하세요.

## <a name="step-2-use-the-model"></a>2단계: 모델 사용

기계 학습 모델을 웹 서비스로, Power BI와 같은 서비스의 분석 또는 IoT Edge 장치에서 사용할 수 있습니다.

### <a name="web-service"></a>웹 서비스

모델을 사용할 수 있습니다 **웹 서비스** 다음을 사용 하 여 계산 대상:

* Azure Container Instances
* Azure Kubernetes Service

웹 서비스로 모델을 배포 하려면 다음을 제공 해야 합니다.

* 모델 또는 모델의 앙상블입니다.
* 모델을 사용 하는 데 필요한 종속성입니다. 예를 들어 요청을 수락 하 고 모델, conda 종속성을 호출 하는 스크립트 등입니다.
* 배포 구성 방법 및 위치를 설명 하는 모델을 배포 하려면.

자세한 내용은 [모델을 배포할](how-to-deploy-and-where.md)합니다.

### <a name="iot-edge-devices"></a>IoT Edge 디바이스

통해 IoT 장치를 사용 하 여 모델을 사용할 수 있습니다 **Azure IoT Edge 모듈**합니다. IoT Edge 모듈은 장치에 대 한 추론을 사용 하도록 설정 하는 하드웨어 장치에 배포 됩니다.

자세한 내용은 [모델을 배포할](how-to-deploy-and-where.md)합니다.

### <a name="analytics"></a>분석

Microsoft Power BI는 데이터 분석을 위해 기계 학습 모델을 사용 하 여 지원 합니다. 자세한 내용은 [Power BI (미리 보기)에서 Azure Machine Learning 통합](https://docs.microsoft.com/power-bi/service-machine-learning-integration)합니다.

## <a name="step-3-monitor-models-and-collect-data"></a>3단계: 모델 모니터링 및 데이터 수집

모니터링을 사용 하면 반환 되는 예측 모델을 전송 되는 어떤 데이터를 이해할 수 있습니다.

이 정보가 모델에 사용 되는 방식을 이해 하도록 도와줍니다. 수집 된 입력된 데이터를 모델의 학습 이후 버전에서는 유용할 수도 있습니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.

## <a name="step-4-update-the-deployment"></a>4단계: 배포 업데이트

배포를 명시적으로 업데이트 되어야 합니다. 자세한 내용은 [모델 배포](how-to-deploy-and-where.md#update)의 업데이트 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스를 사용하여 [모델을 배포할 수 있는 방법과 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다. 배포의 예제를 참조 하세요. [자습서: Azure Container Instances에서 이미지 분류 모델을 배포할](tutorial-deploy-models-with-aml.md)합니다.

만드는 방법을 알아봅니다 [Azure 파이프라인을 사용 하 여 기계 학습 모델의 연속 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning)합니다. 

[웹 서비스로 배포된 모델을 사용](how-to-consume-web-service.md)하는 클라이언트 애플리케이션과 서비스를 만드는 방법을 알아봅니다.
