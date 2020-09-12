---
title: 큐 레이트 환경
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 사용할 수 있는 큐 레이트 환경의 컬렉션
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 09/03/2020
ms.openlocfilehash: 4ae96976f81aab9a0949594551c82d3a3fec4f0a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662083"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐 레이트 환경

이 문서에서는 Azure Machine Learning의 큐 레이트 환경과 해당 환경에 미리 설치 된 패키지 및 채널을 나열 합니다. 큐 레이트 환경은 Azure Machine Learning에서 제공 하며 기본적으로 작업 영역에서 사용할 수 있습니다. 이러한 이미지는 캐시 된 Docker 이미지에 의해 지원 되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용 하 여 다양 한 기계 학습 프레임 워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> 이 목록은 9 월 2020 일에 업데이트 됩니다. Python SDK를 사용 하 여 가장 많이 업데이트 된 목록을 가져옵니다. 자세한 내용은 [환경 문서](./how-to-use-environments.md#use-a-curated-environment)를 참조 하세요.

## <a name="azureml-automl"></a>AzureML-AutoML

**패키지 채널:**

* anaconda
* conda-대장간
* pytorch

**Conda 패키지:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* 휴일
* setuptools-git
* psutil

**Pip 패키지:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-기본값
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 유추-스키마
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**패키지 채널:**

* anaconda
* conda-대장간
* pytorch

**Conda 패키지:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* 휴일
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip 패키지:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-기본값
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 유추-스키마
* pytorch-변환기
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN

**패키지 채널:**

* anaconda
* conda-대장간
* pytorch

**Conda 패키지:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* 휴일
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip 패키지:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-기본값
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 유추-스키마
* horovod
* pytorch-변환기
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-데이터 집합-런타임
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-기본값
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-자동 dnn-비전

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**패키지 채널:**

* anaconda
* conda-대장간
* pytorch

**Conda 패키지:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* 휴일
* setuptools-git
* psutil

**Pip 패키지:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-기본값
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 유추-스키마
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-5.1.0-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-5.1.0-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-6Ask-CPU

**패키지 채널:**

* conda-대장간
* pytorch
* 기본값

**Conda 패키지:**

* python

**Pip 패키지:**

* adlfs
* azureml-core
* azureml-데이터 집합-런타임
* 6ask [완료]
* 6ask-ml [완료]
* 분산
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* Notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-6Ask-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python
* matplotlib

**Pip 패키지:**

* azureml-기본값
* adlfs
* azureml-core
* 6ask [완료]
* 6ask-ml [완료]
* 분산
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* Notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-기본값
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-dnn-자동 ml-예측

## <a name="azureml-minimal"></a>AzureML-최소

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값

## <a name="azureml-pyspark-mmlspark-015"></a>PySpark-MmlSpark-0.15

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>PyTorch-1.0-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>PyTorch-1.0-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>PyTorch-1.1-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-11-gpu"></a>PyTorch-1.1-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-12-cpu"></a>PyTorch-1.2-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-12-gpu"></a>PyTorch-1.2-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-13-cpu"></a>PyTorch-1.3-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-13-gpu"></a>PyTorch-1.3-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-14-cpu"></a>PyTorch-1.4-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-14-gpu"></a>PyTorch-1.4-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-15-cpu"></a>PyTorch-1.5-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-15-gpu"></a>PyTorch-1.5-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-16-cpu"></a>PyTorch-1.6-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-pytorch-16-gpu"></a>PyTorch-1.6-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* 생성

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-0.20.3

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>TensorFlow-1.10-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>TensorFlow-1.10-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>TensorFlow-1.12-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>TensorFlow-1.12-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>TensorFlow-1.13-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>TensorFlow-1.13-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>TensorFlow-2.0-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>TensorFlow-2.0-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>TensorFlow-2.1-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>TensorFlow-2.1-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>TensorFlow-2.2-CPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>TensorFlow-2.2-GPU

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tutorial"></a>AzureML-자습서

**패키지 채널:**

* anaconda
* conda-대장간

**Conda 패키지:**

* python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-telemetry
* azureml-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-학습
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml.mlflow
* mlflow
* 비 기능 학습-pandas

## <a name="azureml-vowpalwabbit-880"></a>VowpalWabbit-8.8.0

**패키지 채널:**

* conda-대장간

**Conda 패키지:**

* python

**Pip 패키지:**

* azureml-core
* azureml-기본값
* azureml-데이터 집합-런타임 [퓨즈, pandas]
