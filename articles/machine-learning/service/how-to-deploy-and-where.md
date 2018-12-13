---
title: Azure Machine Learning Services로 모델을 배포할 대상 | Microsoft Docs
description: Azure Machine Learning Services를 사용하여 프로덕션에 모델을 배포할 수 있는 다양한 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 97ac405db3de4fa2c6f1173f813eafd41a5361ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209448"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

Azure Machine Learning Services는 학습된 모델을 배포할 수 있는 몇 가지 방법을 제공합니다. 이 문서에서는 모델을 Azure 클라우드에서 또는 IoT Edge 디바이스에 웹 서비스로 배포하는 방법을 알아봅니다.

모델은 다음과 같은 계산 대상에 배포할 수 있습니다.

- [ACI(Azure Container Instances)](#aci): 빠른 배포. 개발 또는 테스트에 적합합니다.
- [AKS(Azure Kubernetes Service)](#aks): 대규모 프로덕션 워크로드에 적합합니다. 자동 크기 조정 및 빠른 응답 시간을 제공합니다.
- [Azure IoT Edge](#iotedge): IoT 장치에서 모델을 배포합니다. 디바이스에서 추론이 발생합니다.
- [FPGA(Field-programmable Gate Array)](#fpga): 실시간 추론에 따른 대기 시간이 매우 짧습니다.

이 문서의 나머지 부분에서는 각각의 옵션에 대한 세부 사항을 설명합니다.

## <a id="aci"></a>Azure Container Instances

다음 조건 중 하나 이상에 해당하는 경우 Azure Container Instances를 사용하여 모델을 REST API 엔드포인트로 배포합니다.
- 모델을 빠르게 배포하고 유효성을 검사해야 합니다. ACI 배포는 5분 이내에 완료됩니다.
- 개발 또는 테스트 환경에 모델을 배포하려고 합니다. ACI를 사용하면 구독당 20개의 컨테이너 그룹을 배포할 수 있습니다. 자세한 내용은 [Azure Container Instances에 대한 할당량 및 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 문서를 참조하세요.

자세한 내용은 [Azure Container Instances에 모델 배포](how-to-deploy-to-aci.md) 문서를 참조하세요.

## <a id="aks"></a>Azure Kubernetes Service

대규모 프로덕션 시나리오에서는 AKS(Azure Kubernetes Service)를 사용합니다. 기존 AKS 클러스터를 사용하거나 Azure Machine Learning SDK, CLI 또는 Azure Portal을 사용하여 새로운 클러스터를 만들 수 있습니다.

AKS 클러스터 만들기는 작업 영역에 대한 일회성 프로세스입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터를 삭제하면, 다음에 배포할 때 새 클러스터를 만들어야 합니다.

Azure Kubernetes Service는 다음과 같은 기능을 제공합니다.

* 자동 확장
* 로깅
* 모델 데이터 수집
* 웹 서비스에 대한 빠른 응답 시간

AKS 클러스터를 만드는 프로세스는 약 20분이 걸립니다.

자세한 내용은 [Azure Kubernetes Service에 모델 배포](how-to-deploy-to-aks.md) 문서를 참조하세요.

## <a id="iotedge"></a>Azure IoT Edge

IoT 디바이스를 사용하면 채점을 위해 데이터를 클라우드로 보내는 대신 디바이스에서 채점을 수행하는 것이 더 빠릅니다. Azure IoT Edge를 사용하면 Edge 디바이스에서 모델을 호스트할 수 있습니다. 다음 기능 중 하나 이상이 필요한 경우 IoT Edge에 모델을 배포합니다.
- 클라우드에 연결하지 않고 우선 순위 작업을 로컬에서 처리
- 너무 커서 클라우드에서 신속하게 끌어오기에 어려운 생성된 데이터로 작업
- 로컬 디바이스 내 또는 근처의 인텔리전스를 통해 실시간 처리를 사용하도록 설정
- 데이터 프라이버시 관련 요구 사항에 맞게 조정 

자세한 내용은 [Azure IoT Edge에 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) 문서를 참조하세요.

IoT Edge 서비스에 대한 자세한 내용은 [Azure IoT Edge 설명서](https://docs.microsoft.com/azure/iot-edge/)를 참조하세요.


## <a id="fpga"></a>FPGA(Field-programmable gate arrays)

Project Brainwave로 구동되는 하드웨어 가속 모델은 실시간 추론 요청에 대해 매우 짧은 대기 시간을 구현할 수 있습니다. Project Brainwave는 Azure 클라우드의 FPGA(Field-programmable gate arrays)에 배포된 DNN(심층 신경망)을 가속화합니다. 일반적으로 사용되는 DNN은 전달 학습을 위한 보조 기능으로 사용하거나 자신의 데이터로 학습된 가중치로 사용자 지정할 수 있습니다.

자세한 내용은 [FPGA에 배포](how-to-deploy-fpga-web-service.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

특정 계산 대상에 모델을 배포하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Container Instances에 모델 배포](how-to-deploy-to-aci.md)
* [Azure Kubernetes Service에 모델 배포](how-to-deploy-to-aks.md)
* [Azure IoT Edge에 모델 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [FPGA에 모델 배포](how-to-deploy-fpga-web-service.md)
