---
title: 큐레이팅 환경
titleSuffix: Azure Machine Learning
description: 실험 및 배포 준비 시간을 줄이는 데 도움이 되는 미리 구성된 환경 세트인 Azure Machine Learning 큐레이팅된 환경에 대해 알아봅니다.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 4/2/2021
ms.openlocfilehash: 16d5cbc1d48b4559ac34314582b9c01b7c6bb58c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166652"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐레이팅된 환경

이 문서에는 Azure Machine Learning의 큐레이팅된 환경이 나열되어 있습니다. 큐레이팅된 환경은 Azure Machine Learning에서 제공하며 기본적으로 작업 영역에서 사용할 수 있습니다. 최신 버전의 Azure Machine Learning SDK를 사용하는 캐시된 Docker 이미지에서 지원되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용하여 다양한 기계 학습 프레임워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> 이 목록은 2021년 4월 기준으로 업데이트됩니다. Python SDK 또는 CLI를 사용하여 최신 업데이트 환경 목록 및 해당 종속성을 가져옵니다. 자세한 내용은 [환경 문서](./how-to-use-environments.md#use-a-curated-environment)를 참조하세요. 이 새 세트의 릴리스 후에는 이전 큐레이팅된 환경이 숨겨지지만 계속 사용할 수 있습니다. 

## <a name="pytorch"></a>PyTorch
- AzureML-Pytorch1.7-Cuda11-OpenMpi4.1.0-py36
     - Azure ML SDK 및 추가 python 패키지가 포함된 PyTorch를 사용한 딥 러닝을 위한 환경입니다.
     - PyTorch 버전: 1.7
     - Python 버전: 3.6.9
     - 기본 이미지: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 버전: 11.0.3
     - OpenMPI: 4.1.0

## <a name="scikit"></a>Scikit
- AzureML-Scikit-learn0.20.4-Cuda11-OpenMpi4.1.0-py36
     - Scikit-learn을 사용한 회귀, 클러스터링, 분류 등의 작업을 위한 환경입니다. Azure ML SDK 및 추가 python 패키지가 들어 있습니다.
     - Scikit-learn 버전: 20.4
     - Python 버전: 3.6.9
     - 기본 이미지: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 버전: 11.0.3
     - OpenMPI: 4.1.0

## <a name="tensorflow"></a>TensorFlow
- AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36
     - Azure ML SDK 및 추가 python 패키지가 포함된 Tensorflow를 사용한 딥 러닝을 위한 환경입니다.
     - Tensorflow 버전: 2.4
     - Python 버전: 3.6.9
     - 기본 이미지: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 버전: 11.0.3
     - OpenMPI: 4.1.0
