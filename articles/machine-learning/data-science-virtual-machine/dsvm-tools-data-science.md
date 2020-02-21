---
title: 기계 학습 및 데이터 과학 도구
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 사전 설치 된 기계 학습 도구 및 프레임 워크에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c6b7e3da6cb6fd87e3b43d6f310e3b76f8fc4d30
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526096"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure 데이터 과학 Virtual Machines의 기계 학습 및 데이터 과학 도구
Azure DSVMs (데이터 과학 Virtual Machines)에는 Python, R, 줄리아 등 인기 있는 언어로 제공 되는 기계 학습을 위한 다양 한 도구 및 라이브러리 집합이 있습니다.

다음은 DSVMs의 몇 가지 기계 학습 도구 및 라이브러리입니다.

## <a name="azure-machine-learning-sdk-for-python"></a>Python용 Azure Machine Learning SDK

[Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)에 대 한 전체 참조를 참조 하세요.

|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   Azure Machine Learning은 기계 학습 모델을 개발 하 고 배포 하는 데 사용할 수 있는 클라우드 서비스입니다. Python SDK를 사용하여 모델을 빌드, 학습, 확장 및 관리하는 대로 추적할 수 있습니다. 모델을 컨테이너로 배포하고 클라우드, 온-프레미스 또는 Azure IoT Edge에서 실행합니다.   |
| 지원되는 버전     | Windows(Conda 환경: AzureML), Linux(Conda 환경: py36)    |
| 일반적인 사용 용도      | 일반 기계 학습 플랫폼      |
| 구성/설치 방법      |  GPU 지원과 함께 설치됨   |
| 사용/실행 방법      | Python SDK와 Azure CLI에 있습니다. Windows 버전에서 conda 환경 `AzureML`로 ‘또는’ Linux 버전에서 *으로 활성화합니다.* `py36`      |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북은 노트북 아래의 `AzureML` 디렉터리에 포함되어 있습니다.  |
| 관련 도구      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 메모리 내, 분산, 빠르고 확장 가능한 기계 학습을 지 원하는 오픈 소스 AI 플랫폼입니다.  |
| 지원되는 버전      | Linux   |
| 일반적인 사용 용도      | 범용 분산 확장 가능 기계 학습   |
| 구성/설치 방법      | H2O는 `/dsvm/tools/h2o`에 설치됩니다.      |
| 사용/실행 방법      | X2Go를 사용하여 VM에 연결합니다. 새 터미널을 시작하고 `java -jar /dsvm/tools/h2o/current/h2o.jar`을 실행합니다. 그런 다음 웹 브라우저를 시작하고 `http://localhost:54321`에 연결합니다.      |
| 샘플에 대한 링크      | 샘플은 VM의 `h2o` 디렉터리 아래 Jupyter에서 사용할 수 있습니다.      |
| 관련 도구      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Dsvms에는 DSVMs에 대 한 Anaconda Python 배포의 일부인 인기 있는 `scikit-learn` 패키지와 같은 몇 가지 다른 기계 학습 라이브러리가 있습니다. Python, R, 및 Julia에서 사용할 수 있는 패키지 목록을 확인하려면 개별 패키지 관리자를 실행하세요.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 의사 결정 트리 알고리즘을 기반으로 하는 신속 하 고 분산 된 고성능 그라데이션 향상 (GBDT, GBRT, G강화 또는 마트) 프레임 워크입니다. 순위, 분류 및 기타 많은 기계 학습 작업에 사용 됩니다.    |
| 지원되는 버전      | Windows, Linux    |
| 일반적인 사용 용도      | 범용 그라데이션-프레임 워크 올리기      |
| 구성/설치 방법      | Windows에서 LightGBM가 Python 패키지로 설치됩니다. Linux에서 명령줄 실행 파일은 `/opt/LightGBM/lightgbm`에 있으며, R 패키지가 설치되고, Python 패키지도 설치됩니다.     |
| 샘플에 대한 링크      | [LightGBM 가이드](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| 관련 도구      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   R을 사용 하 여 데이터 마이닝을 위한 그래픽 사용자 인터페이스입니다.   |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | R에 대 한 일반 UI 데이터 마이닝 도구    |
| 사용/실행 방법      | UI 도구로 Windows에서 명령 프롬프트를 시작 하 고 R을 실행 한 다음 R 내에서 `rattle()`를 실행 합니다. Linux에서 X2Go를 사용 하 여 연결 하 고, 터미널을 시작 하 고, R을 실행 한 후 R 내에서 `rattle()`를 실행 합니다. |
| 샘플에 대한 링크      | [Rattle](https://togaware.com/onepager/) |
| 관련 도구      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   고속 오픈 소스, 핵심 학습 시스템 라이브러리    |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 일반 기계 학습 라이브러리      |
| 구성/설치 방법      |  Windows: msi 설치 관리자<br/>Linux: apt-get |
| 사용/실행 방법      | 경로 명령줄 도구로(Windows인 경우 `C:\Program Files\VowpalWabbit\vw.exe`, Linux인 경우 `/usr/bin/vw`)    |
| 샘플에 대한 링크      | [VowPal Wabbit 샘플](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| 관련 도구      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |  데이터 마이닝 작업에 대 한 기계 학습 알고리즘의 컬렉션입니다. 알고리즘은 데이터 집합에 직접 적용하거나 사용자 고유의 Java 코드에서 호출할 수 있습니다. Weka는 데이터 전처리 작업, 분류, 회귀, 클러스터링, 연결 규칙 및 시각화를 위한 도구를 포함합니다. |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 일반 기계 학습 도구     |
| 사용/실행 방법      | Windows의 **시작** 메뉴에서 weka을 검색 합니다. Linux의 경우 X2Go로 로그인한 다음, **Applications** >  **Development** > **Weka**로 이동합니다. |
| 샘플에 대한 링크      | [Weka 샘플](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| 관련 도구      |LightGBM, Rattle, Xgboost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   Python, R, Java, Scala, C++등을 위한 신속 하 고 이식 가능 하며 분산 된 그라데이션 향상 (gbdt, GBRT 또는 g) 라이브러리입니다. 단일 컴퓨터에서 실행 되 고 Apache Hadoop 및 Spark에서 실행 됩니다.    |
| 지원되는 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 일반 기계 학습 라이브러리      |
| 구성/설치 방법      |  GPU 지원과 함께 설치됨   |
| 사용/실행 방법      | Python 라이브러리 (2.7 및 3.5), R 패키지 및 경로 명령줄 도구 (Windows 용`C:\dsvm\tools\xgboost\bin\xgboost.exe` 및 Linux 용 `/dsvm/tools/xgboost/xgboost`)    |
| 샘플에 대한 링크      | 샘플은 VM에, linux인 경우 `/dsvm/tools/xgboost/demo`, Windows인 경우 `C:\dsvm\tools\xgboost\demo`에 포함됩니다.   |
| 관련 도구      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 빅 데이터에 대 한 오픈 소스 SQL 쿼리 엔진    |
| 지원 되는 DSVM 버전      | Windows 2019 (미리 보기), Linux  |
| DSVM에 구성 및 설치 하는 방법      |  `/dsvm/tools/drill*`에 포함 모드 전용으로만 설치됩니다.   |
| 일반적인 사용 용도      |  ETL (추출, 변환, 로드)을 요구 하지 않고 내부 데이터 탐색을 수행 합니다. CSV, JSON, 관계형 테이블 및 Hadoop을 비롯 한 다양 한 데이터 원본 및 형식을 쿼리 합니다.     |
| 사용 및 실행 방법      | 바탕 화면 바로 가기  <br/> [10분 내에 드릴 시작](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM의 관련 도구      |   Rattle, Weka, SQL Server Management Studio      |


