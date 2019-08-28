---
title: 데이터 과학 Virtual Machine 개발 도구 - Azure | Microsoft Docs
description: Data Science Virtual Machine에 사전 설치 된 도구 및 통합 개발 환경에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074295"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine의 개발 도구

DSVM (Data Science Virtual Machine)은 매우 생산적인 IDE (통합 개발 환경)에서 널리 사용 되는 여러 도구를 번들로 묶습니다. DSVM에서 제공되는 몇 가지 도구는 다음과 같습니다.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 범용 IDE      |
| 지원 되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | 소프트웨어 개발    |
| DSVM에 구성 및 설치 하는 방법      | 데이터 과학 워크로드(Python 및 R 도구), Azure 워크로드(Hadoop, Data Lake), Node.js, SQL Server 도구, [Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| 사용 및 실행 방법      | 바탕 화면 바로`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`가기 ()    |
| DSVM의 관련 도구      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio 코드 

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 범용 IDE      |
| 지원 되는 DSVM 버전      | Windows, Linux     |
| 일반적인 사용 용도      | 코드 편집기 및 Git 통합   |
| 사용 및 실행 방법      | Windows의 바탕`C:\Program Files (x86)\Microsoft VS Code\Code.exe`화면 바로 가기 (), Linux의 바탕`code`화면 바로 가기 또는 터미널 ()    |
| DSVM의 관련 도구      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | R 언어용 클라이언트 IDE   |
| 지원 되는 DSVM 버전      | Windows, Linux      |
| 일반적인 사용 용도      |  R 개발     |
| 사용 및 실행 방법      | Windows의 바탕`C:\Program Files\RStudio\bin\rstudio.exe`화면 바로 가기 (), Linux`/usr/bin/rstudio`의 바탕 화면 바로 가기 ()      |
| DSVM의 관련 도구      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | R 언어용 클라이언트 IDE   |
| 무엇인가요?   | R용 웹 기반 IDE    |
| 지원 되는 DSVM 버전      | Linux      |
| 일반적인 사용 용도      |  R 개발     |
| 사용 및 실행 방법      | _Systemctl 사용 rstudio-server_를 사용 하 여 서비스를 사용 하도록 설정 하 고 _systemctl 시작 rstudio-server_를 사용 하 여 서비스를 시작 합니다. 그런 다음 http:\//avvv\8787과 같습니다에서 rstudio 서버에 로그인 합니다.       |
| DSVM의 관련 도구      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | Julia 언어용 클라이언트 IDE   |
| 지원 되는 DSVM 버전      | Windows, Linux      |
| 일반적인 사용 용도      |  Julia 개발     |
| 사용 및 실행 방법      | Windows의 바탕`C:\JuliaPro-0.5.1.1\Juno.bat`화면 바로 가기 (), Linux`/opt/JuliaPro-VERSION/Juno`의 바탕 화면 바로 가기 ()      |
| DSVM의 관련 도구      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | Python 언어용 클라이언트 IDE    |
| 지원 되는 DSVM 버전      | Linux      |
| 일반적인 사용 용도      |  Python 개발     |
| 사용 및 실행 방법      | Linux의 바탕`/usr/bin/pycharm`화면 바로 가기 ()      |
| DSVM의 관련 도구      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 대화형 데이터 시각화 및 BI 도구    |
| 지원 되는 DSVM 버전      | Windows  |
| 일반적인 사용 용도      |  데이터 시각화 및 대시보드 작성   |
| 사용 및 실행 방법      | 바탕 화면 바로`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`가기 ()      |
| DSVM의 관련 도구      |   Visual Studio 2019, Visual Studio Code, Juno      |

