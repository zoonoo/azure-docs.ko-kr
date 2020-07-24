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
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 72ccf2a765f50358635e4a803ed0b92e60bd7d19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012215"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐 레이트 환경

이 문서에서는 Azure Machine Learning의 큐 레이트 환경과 해당 환경에 미리 설치 된 패키지 및 채널을 나열 합니다. 큐 레이트 환경은 Azure Machine Learning에서 제공 하며 기본적으로 작업 영역에서 사용할 수 있습니다. 이러한 이미지는 캐시 된 Docker 이미지에 의해 지원 되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용 하 여 다양 한 기계 학습 프레임 워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> 이 목록은 6 월 2020로 업데이트 됩니다. Python SDK를 사용 하 여 가장 많이 업데이트 된 목록을 가져옵니다. 자세한 내용은 [환경 문서](./how-to-use-environments.md#use-a-curated-environment)를 참조 하세요.

## <a name="azure-automl"></a>Azure AutoML

- [AzureML AutoML](#azureml-automl)
- [AzureML AutoML GPU](#azureml-automl-gpu)
- [AzureML AutoML DNN](#azureml-automl-dnn)
- [AzureML AutoML DNN GPU](#azureml-automl-dnn-gpu)
- [Azure AutoML DNN 비전 GPU](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AzureML AutoML

패키지 채널:
- anaconda
- conda-대장간
- pytorch

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-해석 = = 1.8.0
  - azureml-설명-모델 = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1
  - 유추-스키마
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- pandas>= 0.21.0, <= 0.23.4
- py-xgboost<= 0.90
- fbprophet = = 0.5
- setuptools-git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>AzureML AutoML GPU

패키지 채널:
- anaconda
- conda-대장간
- pytorch

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-해석 = = 1.8.0
  - azureml-설명-모델 = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1
  - 유추-스키마
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- pandas>= 0.21.0, <= 0.23.4
- fbprophet = = 0.5
- setuptools-git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>AzureML AutoML DNN

패키지 채널:
- anaconda
- conda-대장간
- pytorch

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-해석 = = 1.8.0
  - azureml-설명-모델 = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1
  - 유추-스키마
  - pytorch = = 1.0.0
  - spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- py-xgboost<= 0.90
- fbprophet = = 0.5
- setuptools-git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>AzureML AutoML DNN GPU

패키지 채널:
- anaconda
- conda-대장간
- pytorch

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-해석 = = 1.8.0
  - azureml-설명-모델 = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1
  - 유추-스키마
  - horovod = = 0.19.4
  - pytorch = = 1.0.0
  - spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- fbprophet = = 0.5
- setuptools-git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>AzureML AutoML DNN 비전 GPU

관계도
- python = 3.7
- 주사위
  - azureml-automl-core = = 1.8.0
  - azureml-코어 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-해석 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-설명-모델 = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1
  - azureml-학습-automl = = 1.8.0
  - azureml-1.8.0 = = =
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - azureml-파이프라인-단계 = = 1.8.0
  - azureml 파이프라인 = = 1.8.0
  - azureml-학습 = = 1.8.0
  - azureml-sdk = = 1.8.0
  - azureml-자동 dnn-비전 = = 1.8.0

## <a name="azure-ml-designer"></a>Azure ML 디자이너

- [AzureML 디자이너](#azureml-designer)
- [AzureML 디자이너 CV](#azureml-designer-cv)
- [AzureML 디자이너 CV 변형](#azureml-designer-cv-transform)
- [AzureML 디자이너 IO](#azureml-designer-io)
- [AzureML 디자이너 NLP](#azureml-designer-nlp)
- [AzureML 디자이너 PyTorch](#azureml-designer-pytorch)
- [AzureML 디자이너 PyTorch 학습](#azureml-designer-pytorch-train)
- [AzureML 디자이너 R](#azureml-designer-r)
- [AzureML 디자이너 추천](#azureml-designer-recommender)
- [AzureML 디자이너 점수](#azureml-designer-score)
- [AzureML 디자이너 변환](#azureml-designer-transform)

### <a name="azureml-designer"></a>AzureML 디자이너

패키지 채널:
- conda-대장간

관계도
- python = 3.6.8
- scikit = 1.0.6
- 주사위
  - azureml-디자이너-클래식-모듈 = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy = = 2.1.7

### <a name="azureml-designer-cv"></a>AzureML 디자이너 CV

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - azureml-디자이너-cv-모듈 = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>AzureML 디자이너 CV 변형

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - azureml-디자이너-cv-모듈 [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>AzureML 디자이너 IO

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - azureml-dataprep>= 1.6
  - azureml-디자이너-dataio-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>AzureML 디자이너 NLP

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - azureml-디자이너-클래식-모듈 = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy = = 2.1.7

### <a name="azureml-designer-pytorch"></a>AzureML 디자이너 PyTorch

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - pytorch = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>AzureML 디자이너 PyTorch 학습

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - pytorch = = 0.0.8

### <a name="azureml-designer-r"></a>AzureML 디자이너 R

패키지 채널:
- conda-대장간

관계도
- python = 3.6.8
- r-캐럿 = 6.0
- r-catools = 1.17.1
- r-cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-forcats = 0.5.0
- r-예측 = 8.12
- r-& m m = 2.0
- r-igraph = 1.2.4
- r-matrix = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nlme = 3.1
- r-nnet = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-reticulate = 1.12
- r-rocr = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-stringr = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-tseries = 0.10
- r = 3.5.1
- 주사위
  - azureml-디자이너-클래식-모듈 = = 0.0.124

### <a name="azureml-designer-recommender"></a>AzureML 디자이너 추천

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - 추천 = = 0.0.5

### <a name="azureml-designer-score"></a>AzureML 디자이너 점수

패키지 채널:
- 기본값

관계도
- conda
- python = 3.6.8
- 주사위
  - azureml-디자이너-점수-모듈 = = 0.0.5

### <a name="azureml-designer-transform"></a>AzureML 디자이너 변환

패키지 채널:
- 기본값

관계도
- python = 3.6.8
- 주사위
  - datatransform = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML Hyperdrive ForecastDNN

관계도
- python = 3.7
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1
  - azureml-dnn-자동 ml-예측 = = 1.8.0

## <a name="azureml-minimal"></a>AzureML 최소

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0

## <a name="azureml-sidecar"></a>AzureML 사이드카

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2

## <a name="azureml-tutorial"></a>AzureML 자습서

패키지 채널:
- anaconda
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - azureml-위젯 = = 1.8.0
  - azureml-파이프라인-코어 = = 1.8.0
  - azureml-파이프라인-단계 = = 1.8.0
  - azureml-opendatasets = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-학습-automl-client = = 1.8.0
  - azureml-학습-automl-runtime = = 1.8.0. post1  
  - azureml-학습-automl = = 1.8.0
  - azureml-학습 = = 1.8.0
  - azureml-sdk = = 1.8.0
  - azureml-해석 = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-mlflow = = 1.8.0
  - mlflow
  - 비 기능 학습-pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>AzureML VowpalWabbit 8.8.0

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-dataprep [퓨즈, pandas]

## <a name="dask"></a>6ask

- [AzureML 요청 CPU](#azureml-dask-cpu)
- [AzureML 요청 GPU](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>AzureML 요청 CPU

패키지 채널:
- conda-대장간
- pytorch
- 기본값

관계도
- python = 3.6.9
- 주사위
  - adlfs
  - azureml-코어 = = 1.8.0
  - azureml-dataprep
  - 6ask [완료]
  - 6ask-ml [완료]
  - 분산
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - Notebook
  - pyarrow

### <a name="azureml-dask-gpu"></a>AzureML 요청 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.9
- 주사위
  - azureml-기본값 = = 1.8.0
  - adlfs
  - azureml-코어 = = 1.8.0
  - azureml-dataprep
  - 6ask [완료]
  - 6ask-ml [완료]
  - 분산
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - Notebook
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [AzureML 체 이너 5.1.0 CPU](#azureml-chainer-510-cpu)
- [AzureML 체 이너 5.1.0 GPU](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>AzureML 체 이너 5.1.0 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - 체 이너 = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>AzureML 체 이너 5.1.0 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - 체 이너 = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0.15

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [AzureML PyTorch 1.0 CPU](#azureml-pytorch-10-cpu)
- [AzureML PyTorch 1.0 GPU](#azureml-pytorch-10-gpu)
- [AzureML PyTorch 1.1 CPU](#azureml-pytorch-11-cpu)
- [AzureML PyTorch 1.1 GPU](#azureml-pytorch-11-gpu)
- [AzureML PyTorch 1.2 CPU](#azureml-pytorch-12-cpu)
- [AzureML PyTorch 1.2 GPU](#azureml-pytorch-12-gpu)
- [AzureML PyTorch 1.3 CPU](#azureml-pytorch-13-cpu)
- [AzureML PyTorch 1.3 GPU](#azureml-pytorch-13-gpu)
- [AzureML PyTorch 1.4 CPU](#azureml-pytorch-14-cpu)
- [AzureML PyTorch 1.4 GPU](#azureml-pytorch-14-gpu)
- [AzureML PyTorch 1.5 CPU](#azureml-pytorch-15-cpu)
- [AzureML PyTorch 1.5 GPU](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>AzureML PyTorch 1.0 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.0
  - torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>AzureML PyTorch 1.0 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.0
  - torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch 1.1 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.1
  - torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>AzureML PyTorch 1.1 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.1
  - torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>AzureML PyTorch 1.2 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.2
  - torchvision = = 0.4.0
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>AzureML PyTorch 1.2 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.2
  - torchvision = = 0.4.0
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>AzureML PyTorch 1.3 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.3
  - torchvision = = 0.4.1
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>AzureML PyTorch 1.3 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.3
  - torchvision = = 0.4.1
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>AzureML PyTorch 1.4 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.4.0
  - torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>AzureML PyTorch 1.4 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.4.0
  - torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>AzureML PyTorch 1.5 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.5.0
  - torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>AzureML PyTorch 1.5 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - torch = = 1.5.0
  - torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard==1.14.0
  - 미래 = = 0.17.1

## <a name="scikit-learn"></a>Scikit 배우기

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit-배우기 0.20.3

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - scikit-learn==0.20.3
  - scipy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [Azure ML TensorFlow 1.10 CPU](#azureml-tensorflow-110-cpu)
- [Azure ML TensorFlow 1.10 GPU](#azureml-tensorflow-110-gpu)
- [Azure ML TensorFlow 1.12 CPU](#azureml-tensorflow-112-cpu)
- [Azure ML TensorFlow 1.12 GPU](#azureml-tensorflow-112-gpu)
- [Azure ML TensorFlow 1.13 CPU](#azureml-tensorflow-113-cpu)
- [Azure ML TensorFlow 1.13 GPU](#azureml-tensorflow-113-gpu)
- [Azure ML TensorFlow 2.0 CPU](#azureml-tensorflow-20-cpu)
- [Azure ML TensorFlow 2.0 GPU](#azureml-tensorflow-20-gpu)
- [Azure ML TensorFlow 2.1 CPU](#azureml-tensorflow-21-cpu)
- [Azure ML TensorFlow 2.1 GPU](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>AzureML TensorFlow 1.10 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow = = 1.10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>AzureML TensorFlow 1.10 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow-gpu = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>AzureML TensorFlow 1.12 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>AzureML TensorFlow 1.12 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow-gpu = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow 1.13 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>AzureML TensorFlow 1.13 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow-gpu = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>AzureML TensorFlow 2.0 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>AzureML TensorFlow 2.0 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow-gpu = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>AzureML TensorFlow 2.1 CPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>AzureML TensorFlow 2.1 GPU

패키지 채널:
- conda-대장간

관계도
- python = 3.6.2
- 주사위
  - azureml-코어 = = 1.8.0
  - azureml-기본값 = = 1.8.0
  - azureml-원격 분석 = = 1.8.0
  - azureml-restclients-hyperdrive = = 1.8.0
  - azureml-학습-코어 = = 1.8.0
  - tensorflow-gpu = = 2.1.0
  - horovod = = 0.19.1

