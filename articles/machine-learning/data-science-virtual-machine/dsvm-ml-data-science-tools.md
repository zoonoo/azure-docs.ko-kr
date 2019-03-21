---
title: 기계 학습 및 데이터 과학 도구 ‑ Azure 기계 | Microsoft Docs
description: 기계 학습 도구 및 Data Science Virtual Machine에 사전 설치된 프레임워크에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: e8876306e4ffbd0fa9a8aafc6d5d757fd3c9c614
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847368"
---
# <a name="machine-learning-and-data-science-tools"></a>기계 학습 및 데이터 과학 도구
Azure Data Science Virtual Machines에는 Python, R, Julia와 같이 인기 있는 언어에서 사용할 수 있는 ML(기계 학습)을 위한 다양한 도구 및 라이브러리 집합이 있습니다. 

다음은 Data Science Virtual Machines의 ML 도구 및 라이브러리입니다. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   Azure Machine Learning은 ML 모델을 개발하고 배포하는 데 사용할 수 있는 클라우드 서비스입니다. Python SDK를 사용하여 모델을 빌드, 학습, 확장 및 관리하는 대로 추적할 수 있습니다. 모델을 컨테이너로 배포하고 클라우드, 온-프레미스 또는 Azure IoT Edge에서 실행합니다.   |
| 지원되는 버전     | Windows(conda 환경: AzureML), Linux(conda 환경: py36)    |
| 일반적인 사용 용도      | 일반 ML 플랫폼      |
| 구성/설치 방법      |  GPU 지원과 함께 설치됨   |
| 사용/실행 방법      | Python SDK 및 Azure CLI의 경우. Windows 버전에서 conda 환경 `AzureML`로 ‘또는’ Linux 버전에서 `py36`으로 활성화합니다.      |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북은 노트북 아래의 `AzureML` 디렉터리에 포함되어 있습니다.  |
| 관련 도구      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |    XGBoost는 Python R, Java, Scala, C++ 등에 대한 빠르고, 이식 가능하며, 분산된 그래디언트 부스팅(GBDT, GBRT, 또는 GBM) 라이브러리입니다. 단일 머신, Hadoop 및 Spark에서 실행됩니다.    |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 범용 ML 라이브러리      |
| 구성/설치 방법      |  GPU 지원과 함께 설치됨   |
| 사용/실행 방법      | Python 라이브러리(2.7 및 3.5)인 R 패키지 및 경로 명령줄 도구(`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows용, `/dsvm/tools/xgboost/xgboost` Linux 용)    |
| 샘플에 대한 링크      | 샘플은 VM에, linux인 경우 `/dsvm/tools/xgboost/demo`, Windows인 경우 `C:\dsvm\tools\xgboost\demo`에 포함됩니다.   |
| 관련 도구      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   Vowpal Wabbit(“VW”로도 알려짐)는 빠른 out-of-core 오픈 소스 학습 시스템 라이브러리입니다.    |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 범용 ML 라이브러리      |
| 구성/설치 방법      |  Windows--msi 설치 프로그램, Linux--apt-get |
| 사용/실행 방법      | 경로 명령줄 도구로(Windows인 경우 `C:\Program Files\VowpalWabbit\vw.exe`, Linux인 경우 `/usr/bin/vw`)    |
| 샘플에 대한 링크      | [VowPal Wabbit 샘플](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| 관련 도구      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |  Weka는 데이터 마이닝 작업의 ML 알고리즘 컬렉션입니다. 알고리즘은 데이터 집합에 직접 적용하거나 사용자 고유의 Java 코드에서 호출할 수 있습니다. Weka는 데이터 전처리 작업, 분류, 회귀, 클러스터링, 연결 규칙 및 시각화를 위한 도구를 포함합니다. |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 범용 ML 도구     |
| 사용/실행 방법      | Windows인 경우 시작 메뉴에서 Weka를 검색하세요. Linux의 경우 X2Go로 로그인한 다음, **Applications** >   **Development**  >  **Weka**로 이동합니다. |
| 샘플에 대한 링크      | [Weka 샘플](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| 관련 도구      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   Rattle는 R을 사용하여 데이터 마이닝을 위한 그래픽 사용자 인터페이스입니다.   |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | R에 대한 일반 UI 데이터 마이닝 도구    |
| 사용/실행 방법      | UI 도구. Windows에서 명령 프롬프트를 시작하고, R을 실행한 후, R 내부에서 `rattle()`을 실행합니다. Linux에서 X2Go와 연결하여 터미널을 시작하고, R을 실행한 후, R 내부에서 `rattle()`을 실행합니다. |
| 샘플에 대한 링크      | [Rattle](https://togaware.com/onepager/) |
| 관련 도구      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| 무엇인가요?   | LightGBM은 의사 결정 트리 알고리즘을 기반으로 빠르고 분산된 고성능 그라데이션 승격(GBDT, GBRT, GBM 또는 MART) 프레임워크입니다. 이는 순위, 분류 및 기타 ML 작업에 사용됩니다.    |
| 지원되는 버전      | Windows, Linux    |
| 일반적인 사용 용도      | 범용 그라데이션 강화 프레임워크      |
| 구성/설치 방법      | Windows에서 LightGBM가 Python 패키지로 설치됩니다. Linux에서 명령줄 실행 파일은 `/opt/LightGBM/lightgbm`에 있으며, R 패키지가 설치되고, Python 패키지도 설치됩니다.     |
| 샘플에 대한 링크      | [LightGBM 가이드](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| 관련 도구      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| 무엇인가요?   | H2O는 메모리 내 분산되고, 빠르고 확장 가능한 ML을 지원하는 오픈 소스 AI 플랫폼입니다.  |
| 지원되는 버전      | Linux   |
| 일반적인 사용 용도      | 일반 용도로 배포된, 확장 가능한 ML   |
| 구성/설치 방법      | H2O는 `/dsvm/tools/h2o`에 설치됩니다.      |
| 사용/실행 방법      | X2Go를 사용하여 VM에 연결합니다. 새 터미널을 시작하고 `java -jar /dsvm/tools/h2o/current/h2o.jar`을 실행합니다. 그런 다음 웹 브라우저를 시작하고 `http://localhost:54321`에 연결합니다.      |
| 샘플에 대한 링크      | 샘플은 VM의 `h2o` 디렉터리 아래 Jupyter에서 사용할 수 있습니다.      |
| 관련 도구      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Data Science Virtual Machines에 설치된 Anaconda Python 배포의 일부로 제공된 많이 사용되는 `scikit-learn` 패키지와 같은 다른 여러 ML 라이브러리가 Data Science Virtual Machines에 있습니다. Python, R, 및 Julia에서 사용할 수 있는 패키지 목록을 확인하려면 개별 패키지 관리자를 실행하세요.
