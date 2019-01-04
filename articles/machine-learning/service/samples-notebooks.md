---
title: 예제 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: Jupyter Notebook 예제를 찾아 사용하여 Python에서 Azure Machine Learning 서비스를 검색합니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0d74f731d0a7eca25238344e36838dc6c806c788
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434531"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook을 사용하여 Azure Machine Learning 서비스 검색


사용자 편의를 위해 Azure Machine Learning 서비스를 검색하는 데 사용할 수 있는 일련의 Jupyter Python Notebook을 개발했습니다. 

이 사이트에 대한 설명서로 서비스를 사용하고 상황에 맞게 사용자 지정하려면 이러한 Notebook을 사용하는 방법을 알아봅니다. 

## <a name="prerequisite"></a>필수 요소

[Azure Machine Learning Python 빠른 시작](quickstart-get-started.md)을 완료하여 작업 영역을 만들고 Azure Notebooks를 시작합니다.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Azure Notebooks 체험: 클라우드의 무료 Jupyter Notebook

Azure Notebooks로 시작하는 것이 쉽습니다! [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)는 Azure Notebooks에 이미 설치되고 구성되어 있습니다. 설치 및 향후 업데이트를 Azure 서비스를 통해 자동으로 관리합니다.
  
+ **core 자습서 Notebooks**를 실행하려면
  1. [Azure Notebooks](https://notebooks.azure.com/)로 이동합니다.
    
  1. 필수 구성 요소 빠른 시작 중에 만든 **시작** 라이브러리에서 **자습서** 폴더를 찾습니다.
    
  1. 실행하려는 Notebook을 엽니다.
    
+ **다른 Notebooks**를 실행하려면

  1. Azure Notebooks로 [샘플 Notebook을 가져옵니다](https://aka.ms/aml-clone-azure-notebooks).

  1. 이러한 방법 중 하나를 사용하여 라이브러리에 작업 영역 구성 파일을 추가합니다.
     + **시작** 라이브러리에서 **config.json** 파일을 복제된 새 라이브러리로 복사합니다.

     + [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb)의 코드를 사용하여 새 작업 영역을 만듭니다.
    
  1. 실행하려는 Notebook을 엽니다.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>DSVM(Data Science Virtual Machine) 사용

[Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 및 Notebook 서버는 이미 DSVM에 설치 및 구성되어 있습니다. 이러한 단계를 사용하여 Notebook을 실행합니다.

1. [DSVM을 만듭니다](how-to-configure-environment.md#dsvm).

1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

1. 이러한 방법 중 하나를 사용하여 라이브러리에 작업 영역 구성 파일을 추가합니다.
    * 필수 구성 요소 빠른 시작을 사용하여 만든 **aml_config\config.json** 파일을 복제된 디렉터리에 복사합니다.

    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb)의 코드를 사용하여 새 작업 영역을 만듭니다.

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.

## <a name="use-your-own-jupyter-notebook-server"></a>사용자 고유 Jupyter Notebook 서버 사용

사용자 컴퓨터에 로컬 Jupyter Notebook 서버를 만들려면 이러한 단계를 사용합니다.

1. Azure Machine Learning SDK를 설치한 필수 구성 요소 빠른 시작을 완료했는지 확인합니다.

1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

1. 이러한 방법 중 하나를 사용하여 라이브러리에 작업 영역 구성 파일을 추가합니다.
    * 필수 구성 요소 빠른 시작을 사용하여 만든 **aml_config\config.json** 파일을 복제된 디렉터리에 복사합니다.
    
    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb)의 코드를 사용하여 새 작업 영역을 만듭니다.

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.

1. Notebook이 포함된 폴더로 이동합니다.

1. Notebook을 엽니다.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>자동화된 ML 설치 

**이러한 단계는 `automated-machine-learning` 폴더에 있는 Notebook에만 적용됩니다.**

위의 옵션 중 하나를 사용할 수 있는 반면 다음 지침을 사용하면 동시에 환경을 설치하고 작업 영역을 만들 수도 있습니다. 

1. [미니 Conda](https://conda.io/miniconda.html)를 설치합니다. 3.7 이상을 선택합니다. 설치하려면 프롬프트를 따릅니다. 
   >[!NOTE]
   >버전 4.4.10 이상인 경우 기존 Conda를 사용할 수 있습니다. `conda -V`를 사용하면 버전이 표시됩니다. `conda update conda` 명령을 사용하여 Conda 버전을 업데이트할 수 있습니다. 특히 미니 Conda를 설치할 필요가 없습니다.

1. [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
)에서 Zip으로 샘플 Notebook을 다운로드하고 로컬 디렉터리에 콘텐츠를 추출합니다. 자동화된 기계 학습 Notebook은 `how-to-use-azureml/automated-machine-learning` 폴더에 있습니다.

1. 새 Conda 환경을 설정합니다. 
   1. 로컬 머신에서 Conda 프롬프트를 엽니다.
   
   1. 로컬 머신에 추출한 파일로 이동합니다.
   
   1. `automated-machine-learning` 폴더를 엽니다.
   
   1. Windows용 Conda 프롬프트에서 `automl_setup.cmd`를 실행하거나 사용자 운영 체제용 `.sh` 파일을 실행합니다. 실행하는 데 10분 정도 걸릴 수 있습니다.

      설정 스크립트.
      + 새 Conda 환경 만들기
      + 필요한 패키지 설치
      + 위젯 구성
      + Jupyter Notebook 시작
      
   >[!NOTE]
   > 스크립트는 Conda 환경 이름을 선택적 매개 변수로 사용합니다. 기본 Conda 환경 이름은 `azure_automl`입니다. 정확한 명령은 운영 체제에 따라 달라 집니다. 새 환경을 작성하거나 새 버전으로 업그레이드하는 경우에 유용합니다. 예를 들어 ‘automl_setup.cmd azure_automl_sandbox’를 사용하여 환경 이름 azure_automl_sandbox를 만들 수 있습니다. 
      
1. 스크립트가 완료되면 브라우저에 Jupyter Notebook 홈페이지가 표시됩니다.

1. Notebook을 저장한 경로로 이동합니다. 

1. automated-machine-learning 폴더를 연 다음, `configuration.ipynb` Notebook을 엽니다. 

1. Machine Learning Services 리소스 공급 기업을 등록하고 작업 영역을 만들려면 Notebook의 셀을 실행합니다.

이제를 로컬 머신에 저장된 Notebook을 열고 실행할 준비가 되었습니다.


## <a name="next-steps"></a>다음 단계

[Azure Machine Learning 서비스에 대한 GitHub Notebook 리포지토리](https://aka.ms/aml-notebooks) 검색

이러한 자습서를 시도합니다.
+ [MNIST를 사용하여 이미지 분류 모델 학습 및 배포](tutorial-train-models-with-aml.md)

+ [데이터를 준비하고 자동화된 기계 학습을 사용하여 NYC 택시 데이터 세트로 회귀 모델 학습](tutorial-data-prep.md)
