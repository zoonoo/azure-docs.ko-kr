---
title: 지원되는 언어
titleSuffix: Azure Data Science Virtual Machine
description: 지원되는 프로그램 언어 및 관련 도구는 데이터 과학 가상 머신에 사전 설치되어 있습니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283655"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine에서 지원되는 언어 

DSVM(데이터 과학 가상 머신)에는 인공 지능(AI) 응용 프로그램을 빌드하기 위한 여러 사전 빌드된 언어 및 개발 도구가 함께 제공됩니다. 여기에 주목할만한 것들 중 일부입니다.

## <a name="python-windows-server-2016-edition"></a>파이썬 (윈도우 서버 2016 에디션)

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 파이썬 2.7 및 3.7 |
| 지원되는 DSVM 에디션      | Windows Server 2016     |
| DSVM에 구성/설치 방법  | 두 `conda` 개의 전역 환경이 만들어집니다. <br /> * `root` 에 `/anaconda/` 있는 환경은 파이썬 3.7입니다. <br/> * `python2` 에 `/anaconda/envs/python2` 있는 환경은 파이썬 2.7입니다.       |
| 샘플에 대한 링크      | 파이썬샘플 주피터 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | 파이스파크, R, 줄리아.      |

> [!NOTE]
> 2018년 3월 이전에 만들어진 Windows Server 2016에는 파이썬 3.5와 파이썬 2.7이 포함되어 있습니다. 파이썬 2.7은 콘다 **루트** 환경이며 **py37은** 파이썬 3.7 환경입니다.

### <a name="how-to-use-and-run-it"></a>그것을 사용하고 실행하는 방법    

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

  비주얼 스튜디오 커뮤니티 버전에 설치된 PTVS(비주얼 스튜디오용 파이썬 도구)를 사용합니다. 기본적으로 PTVS에서 자동으로 설정되는 유일한 환경은 파이썬 3.6입니다. 

    > [!NOTE]
    > 파이썬 2.7에서 PTVS를 가리키려면 PTVS에서 사용자 지정 환경을 만들어야 합니다. Visual Studio 커뮤니티 에디션에서 이 환경 경로를 설정하려면 **도구 파이썬** -> **도구** -> **파이썬 환경으로** 이동하여 **+ 사용자 지정**을 선택합니다. 그런 다음 위치를 **c:\anaconda\envs\python2로** 설정하고 **자동 감지를 선택합니다.**

* 주피터에서 사용:

  Jupyter를 열고 새 전자 필기를 만들려면 **새로** 만들기를 선택합니다. 파이썬 3.7의 경우 파이썬 _[콘다 루트]와_ 파이썬 2.7의 _파이썬 [Conda env:python2]로_ 커널 유형을 설정할 수 있습니다.

* 파이썬 패키지 설치:

  DSVM의 기본 파이썬 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 그러나 관리자만 글로벌 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 `activate` 명령을 관리자로 사용하여 루트 또는 python2 환경으로 활성화합니다. 그런 다음 패키지 `conda` 관리자를 사용하거나 `pip` 패키지를 설치하거나 업데이트할 수 있습니다.

## <a name="python-linux-edition"></a>파이썬 (리눅스 에디션)

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 파이썬 2.7 및 3.5 |
| 지원되는 DSVM 에디션      | Linux   |
| DSVM에 구성/설치 방법  | 두 `conda` 개의 전역 환경이 만들어집니다. <br /> * `root``/anaconda/` 환경은 파이썬 2.7입니다. <br/> * `py35``/anaconda/envs/py35`환경은 파이썬 3.5입니다.       |
| 샘플에 대한 링크      | 파이썬샘플 주피터 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>그것을 사용하고 실행하는 방법    

* 터미널에서 실행:

  터미널을 열고 실행하려는 Python 버전에 따라 다음 중 하나를 수행합니다.

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* IDE에서 사용:

  비주얼 스튜디오 커뮤니티 에디션에 설치된 PyCharm을 사용합니다. 

* 주피터에서 사용:

  Jupyter를 열고 새 전자 필기를 만들려면 **새로** 만들기를 선택합니다. 파이썬 2.7의 경우 파이썬 **[콘다 루트]와** 파이썬 3.5 환경의 **파이썬 [Conda env:py35]로** 커널 유형을 설정할 수 있습니다. 

* 파이썬 패키지 설치:

  DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 그러나 관리자만 글로벌 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 `source activate` 명령을 관리자또는 sudo 권한이 있는 사용자로 사용하여 루트 또는 py35 환경으로 활성화합니다. 그런 다음 패키지 `conda` 관리자를 사용하거나 `pip` 패키지를 설치하거나 업데이트할 수 있습니다.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 마이크로소프트 R 오픈 3.x (100% CRAN-R와 호환)<br /> 마이크로소프트 R 서버 9.x 개발자 버전 (확장 가능한 엔터프라이즈 지원 R 플랫폼)|
| 지원되는 DSVM 에디션      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 샘플에 대한 링크      | R용 샘플 Jupyter 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>그것을 사용하고 실행하는 방법    

**윈도우**:

* 명령 프롬프트에서 실행:

  명령 프롬프트를 열고 `R`를 입력합니다.

* IDE에서 사용:

  Visual Studio Community Edition 또는 RStudio에 설치된 RTVS(Visual Studio용 RTools)를 사용합니다. 이러한 메뉴는 시작 메뉴 또는 바탕 화면 아이콘으로 사용할 수 있습니다. 

* 주피터에 사용

  Jupyter를 열고 새 전자 필기를 만들려면 **새로** 만들기를 선택합니다. 커널 유형을 **R로** 설정하여 Jupyter R 커널(IRKernel)을 사용할 수 있습니다.

* R 패키지 설치:

  R은 모든 사용자가 읽을 수 있는 글로벌 환경에서 DSVM에 설치됩니다. 그러나 관리자만 글로벌 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 앞의 방법 중 하나를 사용하여 R을 실행합니다. 그런 다음 R 패키지 관리자를 `install.packages()` 실행하여 패키지를 설치하거나 업데이트할 수 있습니다.

**리눅스**:

* 터미널에서 실행:

  터미널을 열고 `R`실행합니다.  

* IDE에서 사용:

  리눅스 DSVM에 설치된 RStudio를 사용합니다.  

* 주피터에서 사용:

  Jupyter를 열고 새 전자 필기를 만들려면 **새로** 만들기를 선택합니다. 커널 유형을 **R로** 설정하여 Jupyter R 커널(IRKernel)을 사용할 수 있습니다. 

* R 패키지 설치:

  R은 모든 사용자가 읽을 수 있는 글로벌 환경에서 DSVM에 설치됩니다. 그러나 관리자만 글로벌 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 앞의 방법 중 하나를 사용하여 R을 실행합니다. 그런 다음 R 패키지 관리자를 `install.packages()` 실행하여 패키지를 설치하거나 업데이트할 수 있습니다.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 0.6 |
| 지원되는 DSVM 에디션      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\JuliaPro-VERSION`에 설치됩니다.<br /> Linux: `/opt/JuliaPro-VERSION`에 설치됩니다.    |
| 샘플에 대한 링크      | 줄리아에 대한 샘플 주피터 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | Python, R      |
### <a name="how-to-use-and-run-it"></a>그것을 사용하고 실행하는 방법    

**윈도우**:

* 명령 프롬프트에서 실행

  명령 프롬프트를 `julia`열고 실행합니다.
* IDE에서 사용:

  DSVM에 설치된 줄리아 IDE와 함께 사용하며 `Juno` 데스크톱 바로 가기로 사용할 수 있습니다.

* 주피터에서 사용:

  Jupyter를 열고 새 전자 필기를 만들려면 **새로** 만들기를 선택합니다. 커널 유형을 **줄리아 버전으로**설정할 수 있습니다.

* 줄리아 패키지 설치:

  기본 Julia 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 그러나 관리자만 글로벌 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 Julia를 실행합니다. 그런 다음 패키지를 설치하거나 업데이트하는 `Pkg.add()` 것과 같은 Julia 패키지 관리자 명령을 실행할 수 있습니다.


**리눅스**:
* 터미널에서 실행:

  터미널을 열고 `julia`실행합니다.
* IDE에서 사용:

  을 `Juno`사용하여 줄리아 IDE가 DSVM에 설치되고 **응용 프로그램** 메뉴 바로 가기로 사용할 수 있습니다.

* 주피터에서 사용:

  Jupyter를 열고 새 전자 필기를 만들려면 **새로** 만들기를 선택합니다. 커널 유형을 **줄리아 버전으로**설정할 수 있습니다.

* 줄리아 패키지 설치:

  기본 Julia 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 그러나 관리자만 글로벌 패키지를 작성하고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 Julia를 실행합니다. 그런 다음 패키지를 설치하거나 업데이트하는 `Pkg.add()` 것과 같은 Julia 패키지 관리자 명령을 실행할 수 있습니다.

## <a name="other-languages"></a>기타 언어

**C#**: Windows에서 사용할 수 있으며 Visual Studio `Developer Command Prompt for Visual Studio`커뮤니티 버전 또는 `csc` 명령을 실행할 수 있는 에서 액세스할 수 있습니다.

**자바**: OpenJDK는 DSVM의 리눅스와 윈도우 버전 모두에서 사용할 수 있으며 경로에 설정되어 있습니다. Java를 사용하려면 `javac` Windows또는 Linux의 bash 셸에서 명령 프롬프트에 또는 `java` 명령을 입력합니다.

**Node.js**: Node.js는 DSVM의 리눅스와 윈도우 버전 모두에서 사용할 수 있으며 경로에 설정되어 있습니다. Node.js에 액세스하려면 `node` Windows또는 Linux의 bash 셸에서 명령 프롬프트에 또는 `npm` 명령을 입력합니다. Windows에서 Node.js 도구에 대한 Visual Studio 확장이 설치되어 Node.js 응용 프로그램을 개발하는 그래픽 IDE를 제공합니다.

**F#**: Windows에서 사용할 수 있으며 Visual Studio `Developer Command Prompt for Visual Studio`커뮤니티 버전 또는 `fsc` 명령을 실행할 수 있는 에서 액세스할 수 있습니다.
