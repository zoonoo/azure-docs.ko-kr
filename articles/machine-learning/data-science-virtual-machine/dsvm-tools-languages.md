---
title: 지원되는 언어
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 미리 설치된 프로그램 언어 및 관련 도구
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100516433"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine에서 지원되는 언어 

DSVM(Data Science Virtual Machine)은 AI(인공 지능) 애플리케이션을 빌드하기 위한 몇 가지 미리 빌드된 언어 및 개발 도구와 함께 제공됩니다. 몇 가지 주목할 만한 항목은 다음과 같습니다.

## <a name="python-windows-server-2016-edition"></a>Python(Windows Server 2016 버전)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 언어 버전 | Python 2.7 및 3.7 |
| 지원되는 DSVM 버전      | Windows Server 2016     |
| DSVM에 구성/설치 방법  | 다음 두 가지 전역 `conda` 환경이 만들어집니다. <br /> * `/anaconda/`에 있는 `root` 환경은 Python 3.7입니다. <br/> * `/anaconda/envs/python2`에 있는 `python2` 환경은 Python 2.7입니다.       |
| 샘플에 대한 링크      | Python에 대한 샘플 Jupyter Notebooks가 포함되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, Julia.      |

> [!NOTE]
> 2018년 3월 이전에 만들어진 Windows Server 2016 빌드에는 Python 3.5 및 Python 2.7이 포함되어 있습니다. 또한 Python 2.7은 Conda **루트** 환경이고, **py37** 은 Python 3.7 환경입니다.

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

* 명령 프롬프트에서 실행:

  명령 프롬프트를 열고 실행하려는 Python 버전에 따라 다음 방법 중 하나를 사용합니다.

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* IDE에서 사용:

  Visual Studio Community 버전에 설치된 PTVS(Visual Studio용 Python 도구)를 사용합니다. 기본적으로 PTVS에서 자동으로 설정되는 유일한 환경은 Python 3.6입니다. 

    > [!NOTE]
    > Python 2.7의 PTVS를 가리키려면 PTVS에서 사용자 지정 환경을 만들어야 합니다. Visual Studio Community 버전에서 이러한 환경 경로를 설정하려면 **도구** -> **Python 도구** -> **Python 환경** 으로 이동한 다음 **+ 사용자 지정** 을 선택합니다. 그런 다음 위치를 **c:\anaconda\envs\python2** 로 설정하고 **자동 검색** 을 선택합니다.

* Jupyter에서 사용:

  Jupyter를 열고 **신규** 를 클릭하여 새 Notebook을 만듭니다. 커널 형식은 Python 3.7의 경우 _Python [Conda Root]_ 로 설정하고 Python 2.7의 경우 _Python [Conda env:python2]_ 로 설정할 수 있습니다.

* Python 패키지를 설치합니다.

  DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 관리자 권한으로 `activate` 명령을 사용하여 root 또는 python2 환경을 활성화합니다. 그런 다음 `conda` 또는 `pip`과 같은 패키지 관리자를 사용하여 패키지를 설치하거나 업데이트할 수 있습니다.

## <a name="python-linux-edition"></a>Python(Linux 버전)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 언어 버전 | Python 2.7 및 3.5 |
| 지원되는 DSVM 버전      | Linux   |
| DSVM에 구성/설치 방법  | 다음 두 가지 전역 `conda` 환경이 만들어집니다. <br /> * `/anaconda/`에 있는 `root` 환경은 Python 2.7입니다. <br/> * `/anaconda/envs/py35`에 있는 `py35` 환경은 Python 3.5입니다.       |
| 샘플에 대한 링크      | Python에 대한 샘플 Jupyter Notebooks가 포함되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

* 터미널에서 실행:

  터미널을 열고 실행하려는 Python 버전에 따라 다음을 수행합니다.

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* IDE에서 사용:

  Visual Studio Community 버전에 설치된 PyCharm을 사용합니다. 

* Jupyter에서 사용:

  Jupyter를 열고 **신규** 를 클릭하여 새 Notebook을 만듭니다. 커널 유형을 Python 2.7의 경우 **Python [Conda Root]** 로 설정하고 Python 3.5 환경의 경우 **Python [Conda env:py35]** 로 설정할 수 있습니다. 

* Python 패키지를 설치합니다.

  DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 관리자 권한으로 또는 sudo 권한이 있는 사용자로 `source activate` 명령을 사용하여 루트 또는 py35 환경을 활성화합니다. 그런 다음 `conda` 또는 `pip`과 같은 패키지 관리자를 사용하여 패키지를 설치하거나 업데이트할 수 있습니다.


## <a name="r"></a>R

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 언어 버전 | Microsoft R Open 3.x(CRAN-R과 100% 호환 가능)<br /> Microsoft R Server 9.x 개발자 버전(스케일링 가능한 엔터프라이즈 지원 R 플랫폼)|
| 지원되는 DSVM 버전      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 샘플에 대한 링크      | R에 대한 샘플 Jupyter Notebooks가 포함되어 있습니다.     |
| DSVM의 관련 도구      | SparkR, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

**Windows**:

* 명령 프롬프트에서 실행:

  명령 프롬프트를 열고 `R`를 입력합니다.

* IDE에서 사용:

  Visual Studio Community Edition 또는 RStudio에 설치된 RTVS(Visual Studio용 RTools)를 사용합니다. 시작 메뉴 또는 바탕 화면 아이콘으로 사용할 수 있습니다. 

* Jupyter에서 사용

  Jupyter를 열고 **신규** 를 클릭하여 새 Notebook을 만듭니다. Jupyter R 커널(IRKernel)을 사용하려면 커널 유형을 **R** 로 설정할 수 있습니다.

* R 패키지 설치:

  R은 모든 사용자가 읽을 수 있는 전역 환경에서 DSVM에 설치됩니다. 하지만 관리자만 전역 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 R을 실행합니다. 그런 후 R 패키지 관리자 `install.packages()`를 실행하여 패키지를 설치하거나 업데이트할 수 있습니다.

**Linux**:

* 터미널에서 실행:

  터미널을 열고 `R`를 실행합니다.  

* IDE에서 사용:

  Linux DSVM에 설치된 RStudio를 사용합니다.  

* Jupyter에서 사용:

  Jupyter를 열고 **신규** 를 클릭하여 새 Notebook을 만듭니다. Jupyter R 커널(IRKernel)을 사용하려면 커널 유형을 **R** 로 설정할 수 있습니다. 

* R 패키지 설치:

  R은 모든 사용자가 읽을 수 있는 전역 환경에서 DSVM에 설치됩니다. 하지만 관리자만 전역 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 R을 실행합니다. 그런 후 R 패키지 관리자 `install.packages()`를 실행하여 패키지를 설치하거나 업데이트할 수 있습니다.


## <a name="julia"></a>Julia

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 언어 버전 | 0.6 |
| 지원되는 DSVM 버전      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\JuliaPro-VERSION`에 설치됩니다.<br /> Linux: `/opt/JuliaPro-VERSION`에 설치됩니다.    |
| 샘플에 대한 링크      | Julia에 대한 샘플 Jupyter Notebooks가 포함되어 있습니다.     |
| DSVM의 관련 도구      | Python, R      |

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법    

**Windows**:

* 명령 프롬프트에서 실행

  명령 프롬프트를 열고 `julia`를 실행합니다.
* IDE에서 사용:

  DSVM에 설치되고 바탕 화면 바로 가기로 사용할 수 있는 Julia IDE가 있는 `Juno`를 사용합니다.

* Jupyter에서 사용:

  Jupyter를 열고 **신규** 를 클릭하여 새 Notebook을 만듭니다. 커널 유형을 **Julia VERSION** 으로 설정할 수 있습니다.

* 다음과 같이 Julia 패키지를 설치합니다.

  기본 Julia 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 Julia를 실행합니다. 그런 후 `Pkg.add()`와 같은 Julia 패키지 관리자 명령을 실행하여 패키지를 설치하거나 업데이트할 수 있습니다.


**Linux**:
* 터미널에서 실행:

  터미널을 열고 `julia`를 실행합니다.
* IDE에서 사용:

  DSVM에 설치되고 **애플리케이션** 메뉴 바로 가기로 사용할 수 있는 Julia IDE가 있는 `Juno`를 사용합니다.

* Jupyter에서 사용:

  Jupyter를 열고 **신규** 를 클릭하여 새 Notebook을 만듭니다. 커널 유형을 **Julia VERSION** 으로 설정할 수 있습니다.

* 다음과 같이 Julia 패키지를 설치합니다.

  기본 Julia 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 Julia를 실행합니다. 그런 후 `Pkg.add()`와 같은 Julia 패키지 관리자 명령을 실행하여 패키지를 설치하거나 업데이트할 수 있습니다.

## <a name="other-languages"></a>기타 언어

**C#** : Windows에서 사용할 수 있으며, Visual Studio Community 버전을 통해서 또는 `csc` 명령을 실행할 수 있는 `Developer Command Prompt for Visual Studio`에서 액세스할 수 있습니다.

**Java**: OpenJDK는 DSVM의 Linux 및 Windows 버전 둘 다에서 사용할 수 있고 경로에 설정됩니다. Java를 사용하려면 Windows의 명령 프롬프트 또는 Linux의 bash 셸에 `javac` 또는 `java` 명령을 입력합니다.

**Node.js**: Node.js는 DSVM의 Linux 및 Windows 버전 둘 다에서 사용할 수 있으며 경로에 설정됩니다. Node.js에 액세스하려면 Windows의 명령 프롬프트 또는 Linux의 bash 셸에 `node` 또는 `npm` 명령을 입력합니다. Windows에서는 Node.js 애플리케이션을 개발하기 위한 그래픽 IDE를 제공하기 위해 Node.js 도구용 Visual Studio 확장이 설치됩니다.

**F#** : Windows에서 사용할 수 있으며, Visual Studio Community 버전을 통해 또는 `fsc` 명령을 실행할 수 있는 `Developer Command Prompt for Visual Studio`에서 액세스할 수 있습니다.
