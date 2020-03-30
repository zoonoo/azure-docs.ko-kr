---
title: '파이썬 스크립트 실행 : 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 파이썬 스크립트 실행 모듈을 사용하여 파이썬 코드를 실행하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 0f86d1ad03062797764af6a0d49beacaa3458a8f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365563"
---
# <a name="execute-python-script-module"></a>파이썬 스크립트 모듈 실행

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 파이썬 코드를 실행하십시오. Python의 아키텍처 및 디자인 원칙에 대한 자세한 내용은 [다음 문서를](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)참조하십시오.

Python을 사용하면 다음과 같은 기존 모듈에서 현재 지원되지 않는 작업을 수행할 수 있습니다.

+ 다음을 사용하여 데이터 시각화`matplotlib`
+ Python 라이브러리를 사용하여 작업 영역에서 데이터 집합 및 모델 을 개명
+ [데이터 가져오기](./import-data.md) 모듈에서 지원되지 않는 원본의 데이터 읽기, 로드 및 조작
+ 나만의 딥 러닝 코드 실행 


Azure 기계 학습은 데이터 처리를 위한 많은 일반적인 유틸리티를 포함하는 Python의 Anaconda 분포를 사용합니다. 아나콘다 버전이 자동으로 업데이트됩니다. 현재 버전은 다음과 같은 것입니다.
 -  아나콘다 4.5+ 파이썬 3.6 에 대한 배포 

사전 설치된 패키지는 다음과 같습니다.
-    아달==1.2.2
-    애플리케이션 인사이트==0.11.9
-    attrs==19.3.0
-    azure-공통==1.1.25
-    azure 코어==1.3.0
-    azure-graphrbac=0.61.1
-    azure-ID==1.3.0
-    azure-mgmt 권한 부여=0.60.0
-    azure-mgmt-컨테이너 레지스트리==2.8.0
-    azure-mgmt-키볼트==2.2.0
-    azure-mgmt-리소스=8.0.1
-    azure-mgmt 저장소==8.0.0
-    azure 저장소-Blob==1.5.0
-    azure 저장소 공통==1.4.2
-    azureml 코어==1.1.5.5
-    azureml-데이터 준비-네이티브==14.1.0
-    azureml-데이터 준비==1.3.5
-    azureml-기본값==1.1.5.1
-    azureml-디자이너-클래식 모듈==0.0.118
-    azureml-디자이너 코어==0.0.31
-    azureml-디자이너-내부==0.0.18
-    azureml-모델-관리-sdk==1.0.1b6.post1
-    azureml 파이프라인 코어==1.1.5
-    azureml-원격 분석==1.1.5.3
-    백포트.템파일=1.0
-    백포트.약어==1.0.post1
-    보토3==1.12.29
-    보토코어==1.15.29
-    캐시툴==4.0.0
-    인증==2019.11.28
-    cffi==1.12.3
-    chardet=3.0.4
-    클릭==7.1.1
-    클라우드 피클==1.3.0
-    구성 : 3.7.4
-    컨텍스트 lib2==0.6.0.post1
-    암호화==2.8
-    사이클러==0.10.0
-    딜==0.3.1.1
-    배포산만==1.4.0
-    도커=4.2.0
-    교화==0.15.2
-    도트넷코어2=2.1.13
-    플라스크==1.0.3
-    퓨즈피==3.0.1
-    젠심==3.8.1
-    구글 api 코어 = = 1.16.0
-    구글-오트==1.12.0
-    구글 클라우드 코어 = = 1.3.0
-    구글 클라우드 스토리지==1.26.0
-    구글 재개 미디어==0.5.0
-    구글나피-공통 프로토스==1.51.0
-    구니콘==19.9.0
-    idna==2.9
-    불균형 학습 ==0.4.3
-    아이소데이트=0.6.0
-    위험==1.1.0
-    지프니==0.4.3
-    진자2=2.11.1
-    jmespath==0.9.5
-    joblib==0.14.0
-    json-로깅 py=0.2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    키위 솔버==1.1.0
-    liac-arff==2.4.0
-    라이트gbm==2.2.3
-    마크업 세이프==1.1.1
-    매트플롯립==3.1.3
-    더 많은 도구==6.0.0
-    msal 확장==0.1.3
-    msal=1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    님버스ml=1.6.1
-    numpy==1.18.2
-    오우스립==3.1.0
-    팬더==0.25.3
-    경로 사양==0.7.0
-    핍=20.0.2
-    포르탈로커=1.6.0
-    프로토부프==3.11.3
-    파이로우==0.16.0
-    pyasn1 모듈==0.2.8
-    pyasn1=0.4.8
-    파이파서=2.20
-    파이크립토돔스==3.7.3
-    pyjwt==1.7.1
-    파이오슬=19.1.0
-    파이파스싱==2.4.6
-    pyrsistent==0.16.0
-    파이썬 날짜 유틸리티 = = 2.8.1
-    pytz==2019.3
-    요청-authlib==1.3.0
-    요청==2.23.0
-    rsa==4.0
-    ruamel.yaml=0.15.89
-    s3전송=0.3.3
-    사이킷 학습 ==0.22.2
-    scipy=1.4.1
-    비밀 저장==3.1.2
-    설치 도구==46.1.post20200323
-    6=1.14.0
-    스마트 오픈==1.10.0
-    urllib3==1.25.8
-    웹소켓-클라이언트==0.57.0
-    베르크제우그==0.16.1
-    휠=0.34.2

 미리 설치된 목록에 없는 다른 패키지를 설치하려면(예: *scikit-misc)* 스크립트에 다음 코드를 추가합니다. 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>파일 업로드
**실행 파이썬 스크립트는** [Azure 기계 학습 파이썬 SDK를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)사용하여 파일 업로드를 지원합니다.

다음 예제에서는 **파이썬 스크립트 실행** 모듈에서 이미지 파일을 업로드하는 방법을 보여 주며 있습니다.

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

파이프라인 실행이 완료되면 모듈의 오른쪽 패널에서 이미지를 미리 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![업로드된 이미지](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>파이썬 스크립트 실행을 구성하는 방법

**파이썬 스크립트 실행** 모듈에는 시작점으로 사용할 수있는 샘플 파이썬 코드가 포함되어 있습니다. **파이썬 스크립트** 모듈 을 실행하려면 **파이썬 스크립트** 텍스트 상자에서 실행할 입력 및 파이썬 코드 집합을 제공합니다.

1. 파이프라인에 **파이썬 스크립트 실행** 모듈을 추가합니다.

2. **Dataset1에** 추가하고 연결하여 입력에 사용할 디자이너의 데이터 집합을 추가합니다. 파이썬 스크립트에서 이 데이터 집합을 **DataFrame1로**참조합니다.

    Python을 사용하여 데이터를 생성하거나 파이썬 코드를 사용하여 모듈로 직접 데이터를 가져오려는 경우 데이터 집합을 사용하는 것은 선택 사항입니다.

    이 모듈은 **Dataset2에서**두 번째 데이터 집합추가를 지원합니다. 파이썬 스크립트의 두 번째 데이터 집합을 DataFrame2로 참조합니다.

    Azure 기계 학습에 저장된 데이터 집합은 이 모듈을 로드하면 **팬더** data.frames로 자동으로 변환됩니다.

    ![파이썬 입력 맵 실행](media/module/python-module.png)

4. 새 파이썬 패키지 또는 코드를 포함하려면 스크립트 번들에 이러한 사용자 지정 리소스가 포함된 압축 파일을 **추가합니다.** **스크립트 번들에** 대한 입력은 파일 형식 데이터 집합으로 작업 영역에 업로드된 압축 파일이어야 합니다. **Dataset** 자산 페이지에서 데이터 집합을 업로드할 수 있으며 디자이너 작성 페이지의 왼쪽 모듈 트리에 있는 **내 데이터 집합** 목록에서 데이터 집합 모듈을 끌어서 놓을 수 있습니다. 

    업로드된 압축 된 아카이브에 포함 된 모든 파일은 파이프 라인 실행 중에 사용할 수 있습니다. 아카이브에 디렉터리 구조가 포함된 경우 구조는 유지되지만 **경로에 src라는** 디렉터리를 미리 준비해야 합니다.

5. **파이썬 스크립트** 텍스트 상자에서 유효한 파이썬 스크립트를 입력하거나 붙여 넣습니다.

    **Python 스크립트** 텍스트 상자에는 주석의 몇 가지 지침과 데이터 액세스 및 출력을 위한 샘플 코드가 미리 채워집니다. 이 코드를 편집하거나 교체해야 합니다. 들여쓰기 및 대/소문자에 대한 Python 규칙을 따라야 합니다.

    + 스크립트에는 이 모듈의 `azureml_main` 진입점으로 명명된 함수가 포함되어야 합니다.
    + 진입점 함수에는 최대 2개의 입력 `Param<dataframe1>` 인수가 포함될 수 있습니다.`Param<dataframe2>`
    + 세 번째 입력 포트에 연결된 압축 파일은 압축을 풀고 디렉토리에 저장되며 파이썬에 `.\Script Bundle` `sys.path`추가됩니다. 

    따라서 zip 파일에 포함 `mymodule.py`된 경우 `import mymodule`을 사용하여 가져옵니다.

    + 두 데이터 집합을 디자이너에 `pandas.DataFrame`반환할 수 있습니다. Python 코드에서 다른 출력을 만들고 Azure 저장소에 직접 쓸 수 있습니다.

6. 파이프라인을 제출하거나 모듈을 선택하고 **선택한 실행을** 클릭하여 Python 스크립트만 실행합니다.

    모든 데이터와 코드는 가상 시스템에 로드되고 지정된 Python 환경을 사용하여 실행됩니다.

## <a name="results"></a>결과

포함된 파이썬 코드에서 수행되는 모든 계산 결과는 팬더로 제공되어야 합니다. Azure 기계 학습 데이터 집합 형식으로 자동으로 변환되는 DataFrame을 사용하여 파이프라인의 다른 모듈과 함께 결과를 사용할 수 있습니다.

모듈은 두 개의 데이터 집합을 반환합니다.  
  
+ **결과 데이터 집합 1,** 파이썬 스크립트에서 첫 번째 반환 된 팬더 데이터 프레임에 의해 정의

+ **결과 데이터 집합 2**, 파이썬 스크립트에서 두 번째 반환 팬더 데이터 프레임에 의해 정의


## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 