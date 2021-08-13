---
title: 지원되는 언어
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 미리 설치된 프로그램 언어 및 관련 도구
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: fcb2a4d9860687fb18409666f81839002329d54e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070901"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine에서 지원되는 언어 

DSVM(Data Science Virtual Machine)은 AI(인공 지능) 애플리케이션을 빌드하기 위한 몇 가지 미리 빌드된 언어 및 개발 도구와 함께 제공됩니다. 몇 가지 주목할 만한 항목은 다음과 같습니다.

## <a name="python"></a>Python

| 범주 | 값 |
|--|--|
| 지원되는 언어 버전 | Python 3.8 |
| 지원되는 DSVM 버전 | Windows Server 2019, Ubuntu 18.04 |
| DSVM에 구성/설치 방법 | 각기 다른 Python 패키지가 사전 설치된 여러 `conda` 환경이 있습니다. 시스템에서 사용 가능한 모든 환경을 나열하려면 `conda env list`를 실행합니다. |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법

* 명령 프롬프트에서 실행:

  명령 프롬프트를 열고 실행하려는 Python 버전에 따라 다음 방법 중 하나를 사용합니다.

    ```
    conda activate <conda_environment_name>
    python --version
    ```
    
* IDE에서 사용:

  DSVM 이미지에는 VS.Code 또는 PyCharm과 같은 여러 ID가 설치되어 있습니다. Python 스크립트를 편집, 실행 및 디버그하는 데 사용할 수 있습니다.

* Jupyter 랩에서 사용:

  Jupyter Lab에서 Launcher 탭을 열고 새 문서의 유형 및 커널을 선택합니다. 문서를 특정 폴더에 배치하려면 먼저 왼쪽의 파일 브라우저에서 해당 폴더로 이동합니다.

* Python 패키지를 설치합니다.

  새 패키지를 설치하려면 먼저 올바른 환경을 활성화해야 합니다. 환경은 새 패키지가 설치되는 위치이며 해당 환경에서만 패키지를 사용할 수 있습니다.

  환경을 활성화하려면 `conda activate <environment_name>`을 실행합니다. 환경이 활성화되면 `conda` 또는 `pip`와 같은 패키지 관리자를 사용하여 패키지를 설치하거나 업데이트할 수 있습니다.

  대안으로 Jupyter를 사용하는 경우 셀에서 `!pip install --upgrade <package_name>`를 실행하여 직접 패키지를 설치할 수도 있습니다.

## <a name="r"></a>R

| 범주 | 값 |
|--|--|
| 지원되는 언어 버전 | CRAN R 4.0.5 |
| 지원되는 DSVM 버전 | Linux, Windows |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법

* 명령 프롬프트에서 실행:

  명령 프롬프트를 열고 `R`를 입력합니다.

* IDE에서 사용:

  IDE에서 R 스크립트를 편집하려면 기본적으로 DSVM 이미지에 설치된 RStudio를 사용할 수 있습니다.

* Jupyter 랩에서 사용

  Jupyter Lab에서 Launcher 탭을 열고 새 문서의 유형 및 커널을 선택합니다. 문서를 특정 폴더에 배치하려면 먼저 왼쪽의 파일 브라우저에서 해당 폴더로 이동합니다.

* R 패키지 설치:

  `install.packages()` 함수를 사용하거나 RStudio를 사용하여 새로운 R 패키지를 설치할 수 있습니다.

## <a name="julia"></a>Julia

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 언어 버전 | 1.0.5 |
| 지원되는 DSVM 버전      | Linux, Windows     |


### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

* 명령 프롬프트에서 실행

  명령 프롬프트를 열고 `julia`를 실행합니다.

* Jupyter에서 사용:

  Jupyter에서 Launcher 탭을 열고 새 문서의 유형 및 커널을 선택합니다. 문서를 특정 폴더에 배치하려면 먼저 왼쪽의 파일 브라우저에서 해당 폴더로 이동합니다.

* 다음과 같이 Julia 패키지를 설치합니다.

  `Pkg.add()`와 같은 Julia 패키지 관리자 명령을 사용하여 패키지를 설치하거나 업데이트할 수 있습니다.


## <a name="other-languages"></a>기타 언어

**C#** : Windows에서 사용할 수 있으며, Visual Studio Community 버전을 통해서 또는 `csc` 명령을 실행할 수 있는 `Developer Command Prompt for Visual Studio`에서 액세스할 수 있습니다.

**Java**: OpenJDK는 DSVM의 Linux 및 Windows 버전 둘 다에서 사용할 수 있고 경로에 설정됩니다. Java를 사용하려면 Windows의 명령 프롬프트 또는 Linux의 bash 셸에 `javac` 또는 `java` 명령을 입력합니다.

**Node.js**: Node.js는 DSVM의 Linux 및 Windows 버전 둘 다에서 사용할 수 있으며 경로에 설정됩니다. Node.js에 액세스하려면 Windows의 명령 프롬프트 또는 Linux의 bash 셸에 `node` 또는 `npm` 명령을 입력합니다. Windows에서는 Node.js 애플리케이션을 개발하기 위한 그래픽 IDE를 제공하기 위해 Node.js 도구용 Visual Studio 확장이 설치됩니다.

**F#** : Windows에서 사용할 수 있으며, Visual Studio Community 버전을 통해 또는 `fsc` 명령을 실행할 수 있는 `Developer Command Prompt for Visual Studio`에서 액세스할 수 있습니다.
