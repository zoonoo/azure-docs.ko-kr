---
title: 지원되는 언어
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 미리 설치 되어 있는 지원 되는 프로그램 언어 및 관련 도구입니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: tracking-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 7d3ab058d76186f73fa1a985d7e8af38dbd7a6f8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536184"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine에서 지원되는 언어 

DSVM (Data Science Virtual Machine)은 AI (인공 지능) 응용 프로그램을 빌드하기 위한 몇 가지 미리 작성 된 언어 및 개발 도구와 함께 제공 됩니다. 다음은 주목할 만한 몇 가지 사항입니다.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 버전)

| 범주 | 값 |
| ------------- | ------------- |
| 지원 되는 언어 버전 | Python 2.7 및 3.7 |
| 지원 되는 DSVM 버전      | Windows Server 2016     |
| DSVM에 구성/설치 방법  | 두 개의 글로벌 `conda` 환경을 만듭니다. <br /> * `root` 에 있는 환경은 `/anaconda/` Python 3.7입니다. <br/> * `python2` 에 있는 환경은 `/anaconda/envs/python2` Python 2.7입니다.       |
| 샘플에 대한 링크      | Python 용 샘플 Jupyter 노트북이 포함 되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, 줄리아.      |

> [!NOTE]
> 2016 년 3 2018 월 이전에 만든 Windows Server의 빌드에 Python 3.5 및 Python 2.7이 포함 됩니다. Python 2.7은 conda **루트** 환경으로, **py37** 는 python 3.7 환경입니다.

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

* 명령 프롬프트에서를 실행 합니다.

  명령 프롬프트를 열고 실행 하려는 Python 버전에 따라 다음 방법 중 하나를 사용 합니다.

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* IDE에서 사용:

  Visual Studio Community edition에 설치 된 PTVS (Visual Studio용 Python 도구)를 사용 합니다. 기본적으로 PTVS에서 자동으로 설정 되는 유일한 환경은 Python 3.6입니다. 

    > [!NOTE]
    > PTVS에서 Python 2.7을 가리키도록 하려면 PTVS에서 사용자 지정 환경을 만들어야 합니다. Visual Studio Community Edition에서이 환경 경로를 설정 하려면 **도구**  ->  **python 도구**  ->  **python 환경** 으로 이동한 다음 **+ 사용자 지정**을 선택 합니다. 그런 다음 위치를 **c:\anaconda\envs\python2** 로 설정 하 고 **자동 검색**을 선택 합니다.

* Jupyter에서 사용:

  Jupyter를 열고 **새로** 만들기를 선택 하 여 새 노트북을 만듭니다. 커널 형식은 python 3.7에 대해 python _[Conda Root]_ 로 설정 하 고 python 2.7의 경우 Python _[Conda env: python2]_ 로 설정할 수 있습니다.

* Python 패키지 설치:

  DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성 하 고 설치할 수 있습니다. 전역 환경에 패키지를 설치 하려면 `activate` 관리자 권한으로 명령을 사용 하 여 root 또는 python2 환경을 활성화 합니다. 그런 다음 또는와 같은 패키지 관리자를 사용 `conda` `pip` 하 여 패키지를 설치 하거나 업데이트할 수 있습니다.

## <a name="python-linux-edition"></a>Python (Linux 버전)

| 범주 | 값 |
| ------------- | ------------- |
| 지원 되는 언어 버전 | Python 2.7 및 3.5 |
| 지원 되는 DSVM 버전      | Linux   |
| DSVM에 구성/설치 방법  | 두 개의 글로벌 `conda` 환경을 만듭니다. <br /> * `root`에 있는 환경은 `/anaconda/` Python 2.7입니다. <br/> * `py35`에 있는 환경은 `/anaconda/envs/py35` Python 3.5입니다.       |
| 샘플에 대한 링크      | Python 용 샘플 Jupyter 노트북이 포함 되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

* 터미널에서 실행:

  터미널을 열고 실행 하려는 Python 버전에 따라 다음 중 하나를 수행 합니다.

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* IDE에서 사용:

  Visual Studio Community edition에 설치 된 PyCharm를 사용 합니다. 

* Jupyter에서 사용:

  Jupyter를 열고 **새로** 만들기를 선택 하 여 새 노트북을 만듭니다. Python 3.5 환경에 대해 python 2.7 및 **python [Conda env: py35]** 에 대해 커널 유형을 **Python [Conda Root]** 로 설정할 수 있습니다. 

* Python 패키지 설치:

  DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성 하 고 설치할 수 있습니다. 전역 환경에 패키지를 설치 하려면 `source activate` 관리자 권한으로 명령을 사용 하거나 sudo 권한이 있는 사용자로 사용 하 여 root 또는 py35 환경을 활성화 합니다. 그런 다음 또는와 같은 패키지 관리자를 사용 `conda` `pip` 하 여 패키지를 설치 하거나 업데이트할 수 있습니다.


## <a name="r"></a>R

| 범주 | 값 |
| ------------- | ------------- |
| 지원 되는 언어 버전 | Microsoft R Open 3(sp3) (100%와 CRAN-R 호환)<br /> Microsoft R Server 8.x Developer edition (확장성 있는 엔터프라이즈급 R 플랫폼)|
| 지원 되는 DSVM 버전      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 샘플에 대한 링크      | R에 대 한 샘플 Jupyter 노트북이 포함 되어 있습니다.     |
| DSVM의 관련 도구      | SparkR, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

**Windows**:

* 명령 프롬프트에서를 실행 합니다.

  명령 프롬프트를 열고 `R`를 입력합니다.

* IDE에서 사용:

  Visual Studio Community Edition 또는 RStudio에 설치된 RTVS(Visual Studio용 RTools)를 사용합니다. 이러한 도구는 시작 메뉴 또는 바탕 화면 아이콘에서 사용할 수 있습니다. 

* Jupyter에서 사용

  Jupyter를 열고 **새로** 만들기를 선택 하 여 새 노트북을 만듭니다. 커널 유형을 **R** 로 설정 하 여 Jupyter r 커널 (irkernel)을 사용할 수 있습니다.

* R 패키지 설치:

  R은 모든 사용자가 읽을 수 있는 전역 환경에서 DSVM에 설치 됩니다. 하지만 관리자만 전역 패키지를 작성 하 고 설치할 수 있습니다. 전역 환경에 패키지를 설치 하려면 위의 방법 중 하나를 사용 하 여 R을 실행 합니다. 그런 다음 R 패키지 관리자를 실행 하 여 `install.packages()` 패키지를 설치 하거나 업데이트할 수 있습니다.

**Linux**:

* 터미널에서 실행:

  터미널을 열고를 실행 `R` 합니다.  

* IDE에서 사용:

  Linux DSVM에 설치 된 RStudio를 사용 합니다.  

* Jupyter에서 사용:

  Jupyter를 열고 **새로** 만들기를 선택 하 여 새 노트북을 만듭니다. 커널 유형을 **R** 로 설정 하 여 Jupyter r 커널 (irkernel)을 사용할 수 있습니다. 

* R 패키지 설치:

  R은 모든 사용자가 읽을 수 있는 전역 환경에서 DSVM에 설치 됩니다. 하지만 관리자만 전역 패키지를 작성 하 고 설치할 수 있습니다. 전역 환경에 패키지를 설치 하려면 위의 방법 중 하나를 사용 하 여 R을 실행 합니다. 그런 다음 R 패키지 관리자를 실행 하 여 `install.packages()` 패키지를 설치 하거나 업데이트할 수 있습니다.


## <a name="julia"></a>Julia

| 범주 | 값 |
| ------------- | ------------- |
| 지원 되는 언어 버전 | 0.6 |
| 지원 되는 DSVM 버전      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\JuliaPro-VERSION`에 설치됩니다.<br /> Linux: `/opt/JuliaPro-VERSION`에 설치됩니다.    |
| 샘플에 대한 링크      | 줄리아 용 샘플 Jupyter 노트북이 포함 되어 있습니다.     |
| DSVM의 관련 도구      | Python, R      |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

**Windows**:

* 명령 프롬프트에서 실행

  명령 프롬프트를 열고를 실행 `julia` 합니다.
* IDE에서 사용:

  `Juno`DSVM에 설치 되 고 바탕 화면 바로 가기로 사용할 수 있는 줄리아 IDE와 함께 사용 합니다.

* Jupyter에서 사용:

  Jupyter를 열고 **새로** 만들기를 선택 하 여 새 노트북을 만듭니다. 커널 유형을 **줄리아 버전**으로 설정할 수 있습니다.

* 줄리아 패키지 설치:

  기본 줄리아 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성 하 고 설치할 수 있습니다. 전역 환경에 패키지를 설치 하려면 위의 방법 중 하나를 사용 하 여 줄리아를 실행 합니다. 그런 다음,와 같은 줄리아 패키지 관리자 명령을 실행 `Pkg.add()` 하 여 패키지를 설치 하거나 업데이트할 수 있습니다.


**Linux**:
* 터미널에서 실행:

  터미널을 열고를 실행 `julia` 합니다.
* IDE에서 사용:

  `Juno`DSVM에 설치 되 고 **응용 프로그램** 메뉴 바로 가기로 사용할 수 있는 줄리아 IDE와 함께를 사용 합니다.

* Jupyter에서 사용:

  Jupyter를 열고 **새로** 만들기를 선택 하 여 새 노트북을 만듭니다. 커널 유형을 **줄리아 버전**으로 설정할 수 있습니다.

* 줄리아 패키지 설치:

  기본 줄리아 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성 하 고 설치할 수 있습니다. 전역 환경에 패키지를 설치 하려면 위의 방법 중 하나를 사용 하 여 줄리아를 실행 합니다. 그런 다음,와 같은 줄리아 패키지 관리자 명령을 실행 `Pkg.add()` 하 여 패키지를 설치 하거나 업데이트할 수 있습니다.

## <a name="other-languages"></a>기타 언어

**C #**: Windows에서 사용할 수 있고 Visual Studio Community edition 또는 `Developer Command Prompt for Visual Studio` 명령을 실행할 수 있는에서 액세스할 수 있습니다 `csc` .

**Java**: OPENJDK는 Dsvm의 Linux 및 Windows 버전 둘 다에서 사용할 수 있으며 경로에 설정 됩니다. Java를 사용 하려면 `javac` `java` Windows의 명령 프롬프트 또는 Linux의 bash 셸에 또는 명령을 입력 합니다.

**Node.js**: Node.js는 Dsvm의 Linux 및 Windows 버전 둘 다에서 사용할 수 있고 경로에 설정 됩니다. Node.js에 액세스 하려면 `node` `npm` Windows의 명령 프롬프트 또는 Linux의 bash 셸에 또는 명령을 입력 합니다. Windows에서는 Node.js 응용 프로그램을 개발 하기 위한 그래픽 IDE를 제공 하기 위해 Node.js tools 용 Visual Studio 확장이 설치 됩니다.

**F #**: Windows에서 사용할 수 있고 Visual Studio Community edition 또는 `Developer Command Prompt for Visual Studio` 명령을 실행할 수 있는에서 액세스할 수 있습니다 `fsc` .
