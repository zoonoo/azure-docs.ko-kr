---
title: Python 스크립트를 실행 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 Python 코드를 실행할 Python 스크립트 실행 모듈을 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029132"
---
# <a name="execute-python-script-module"></a>Python 스크립트 모듈 실행

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 Python 코드를 실행 합니다. Python의 아키텍처 및 디자인 원칙에 대 한 자세한 내용은 참조 하세요. [문서.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Python을 사용 하 여 현재 지원 되지 않습니다 기존 모듈에서 같은 작업을 수행할 수 있습니다.

+ 사용 하 여 데이터 시각화 `matplotlib`
+ Python 라이브러리를 사용 하 여 데이터 집합 및 작업 영역에서 모델을 열거 하려면
+ 읽기, 로드 및에서 지원 되지 않습니다 원본의 데이터를 조작 합니다 [데이터 가져오기](./import-data.md) 모듈
+ 사용자 고유의 심층 학습 코드를 실행 합니다. 


Azure Machine Learning에서는 데이터 처리를 위한 많은 공통 유틸리티를 포함 하는 Python의 Anaconda 배포를 사용 합니다. Anaconda 버전 자동으로 업데이트 됩니다. 현재 버전은:
 -  Python 3.6에 대 한 anaconda 4.5 + 배포 

미리 설치 된 패키지를 다음과 같습니다.
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- 요청 2.21.0 = =
- scikit-알아보기 0.20.3 = =
- scipy==1.2.1
- setuptools 40.8.0 = =
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 예를 들어 미리 설치 된 목록에 없는 다른 패키지를 설치 하려면 *scikit 기타*, 스크립트에 다음 코드를 추가 합니다. 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>사용 방법

합니다 **Python 스크립트 실행** 모듈 시작 점으로 사용할 수 있는 샘플 Python 코드가 포함 되어 있습니다. 구성 하는 **Python 스크립트 실행** 모듈을 실행할 Python 코드와 입력 집합을 제공 합니다 **Python 스크립트** 입력란입니다.

1. 추가 된 **Python 스크립트 실행** 모듈을 실험 합니다.

2. 추가 하 고에 연결 **Dataset1** 입력에 대 한 사용 하려는 인터페이스에서 모든 데이터 집합입니다. 이 데이터 집합으로 Python 스크립트에서 참조할 **DataFrame1**합니다.

    Python을 사용 하 여 데이터를 생성 하려면 또는 Python 코드를 사용 하 여 모듈에 직접 데이터를 가져올 경우 데이터 집합의 사용은 선택 사항입니다.

    이 모듈에서 두 번째 데이터 집합의 추가 지원 **Dataset2**합니다. DataFrame2로 Python 스크립트의 두 번째 데이터 집합을 참조 합니다.

    Azure Machine Learning에서 저장 된 데이터 집합은 자동으로 변환 **pandas** 있더라도이 모듈을 사용 하 여 로드 하는 경우.

    ![Python 입력된 지도 실행 합니다.](media/module/python-module.png)

4. 새 Python 패키지 또는 코드를 포함 하려면 이러한 사용자 지정 리소스가 포함 된 zip 파일을 추가 **스크립트 번들**합니다. 에 대 한 입력 **스크립트 번들** 압축 된 파일을 이미 업로드 해야 작업 영역에 있습니다. 

    실험 실행 하는 동안 업로드 된 압축된 보관 파일에 포함 된 모든 파일을 사용할 수 있습니다. 디렉터리 구조를 포함 하는 보관, 구조가 유지 되 있지만 라는 디렉터리를 추가 해야 합니다 **src** 경로에 있습니다.

5. 에 **Python 스크립트** 텍스트 상자에 입력 하거나 유효한 Python 스크립트를 붙여 넣습니다.

    합니다 **Python 스크립트** 입력란 설명 및 데이터 액세스 및 출력에 대 한 샘플 코드의 몇 가지 지침을 사용 하 여 미리 채워집니다. **편집 하거나이 코드를 대체 해야 합니다.** 들여쓰기 및 대/소문자 구분에 대 한 Python 규칙을 준수 해야 합니다.

    + 스크립트 라는 함수를 포함 해야 합니다 `azureml_main` 이 모듈에 대 한 진입점으로 합니다.
    + 진입점 함수는 최대 2 개의 입력된 인수를 포함할 수 있습니다: `Param<dataframe1>` 및 `Param<dataframe2>`
    + 세 번째 입력된 포트에 연결 하는 zip 파일 압축을 해제 되 고 디렉터리에 저장 `.\Script Bundle`에서 Python에도 추가 됩니다는 `sys.path`합니다. 

    따라서 zip 파일에 있으면 `mymodule.py`를 사용 하 여 가져옵니다 `import mymodule`합니다.

    + 형식의 시퀀스 여야 하는 인터페이스에 두 개의 데이터 집합을 반환할 수 있습니다 `pandas.DataFrame`합니다. Python 코드에서 다른 출력을 만들 수 있으며 Azure storage에 직접 씁니다.

6. 또는 실험을 실행 하 고, 모듈을 선택 하 고, 클릭 **선택 항목 실행** 방금 Python 스크립트를 실행 합니다.

    데이터 및 코드의 모든 가상 컴퓨터에 로드 되 고 지정 된 Python 환경을 사용 하 여 실행 합니다.

## <a name="results"></a>결과

포함된 된 Python 코드에서 수행 하는 모든 계산의 결과 pandas로 제공 되어야 합니다. 골라서 실험의 다른 모듈을 사용 하 여 결과 사용할 수 있도록 Azure Machine Learning 데이터 집합 형식으로 자동으로 변환 됩니다.

모듈이 두 데이터 집합을 반환합니다.  
  
+ **결과 데이터 집합 1**Python 스크립트에서 첫 번째 반환 된 pandas 데이터 프레임으로 정의 된

+ **결과 데이터 집합 2**Python 스크립트에서 두 번째 반환 된 pandas 데이터 프레임으로 정의 된


## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 