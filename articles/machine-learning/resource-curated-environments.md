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
ms.openlocfilehash: 1bea195d6f08832415a16531212d176ada7402e6
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110612578"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐레이팅된 환경

이 문서에는 Azure Machine Learning의 큐레이팅된 환경이 나열되어 있습니다. 큐레이팅된 환경은 Azure Machine Learning에서 제공하며 기본적으로 작업 영역에서 사용할 수 있습니다. 최신 버전의 Azure Machine Learning SDK를 사용하는 캐시된 Docker 이미지에서 지원되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용하여 다양한 기계 학습 프레임워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> 이 목록은 2021년 4월 기준으로 업데이트됩니다. Python SDK 또는 CLI를 사용하여 최신 업데이트 환경 목록 및 해당 종속성을 가져옵니다. 자세한 내용은 [환경 문서](./how-to-use-environments.md#use-a-curated-environment)를 참조하세요. 이 새 세트의 릴리스 후에는 이전 큐레이팅된 환경이 숨겨지지만 계속 사용할 수 있습니다. 

## <a name="pytorch"></a>PyTorch
- AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu
     - Azure ML SDK 및 추가 python 패키지가 포함된 PyTorch를 사용한 딥 러닝을 위한 환경입니다.
     - PyTorch 버전: 1.7
     - Python 버전: 3.7
     - 기본 이미지: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 버전: 11.0.3
     - OpenMPI 버전: 4.1.0
     - Ubuntu 버전: 18.04

## <a name="sklearn"></a>Sklearn
- AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu
     - Scikit-learn을 사용한 회귀, 클러스터링, 분류 등의 작업을 위한 환경입니다. Azure ML SDK 및 추가 python 패키지가 들어 있습니다.
     - Scikit-learn 버전: 24.1
     - Python 버전: 3.7
     - 기본 이미지: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 버전: 11.0.3
     - OpenMPI 버전: 4.1.0
     - Ubuntu 버전: 18.04

## <a name="tensorflow"></a>TensorFlow
- AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu
     - Azure ML SDK 및 추가 python 패키지가 포함된 Tensorflow를 사용한 딥 러닝을 위한 환경입니다.
     - Tensorflow 버전: 2.4
     - Python 버전: 3.7
     - 기본 이미지: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 버전: 11.0.3
     - OpenMPI 버전: 4.1.0
     - Ubuntu 버전: 18.04

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>큐레이팅된 환경 및 미리 빌드된 Docker 이미지만 유추
- 미리 빌드된 Docker 이미지의 추론 전용 큐레이팅 환경 및 MCR 경로에 대해 알아보려면 [추론을 위해 미리 빌드된 Docker 이미지](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference)를 참조하세요.
