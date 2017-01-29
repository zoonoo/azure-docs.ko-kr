---
title: "Mac 컴퓨터 및 Azure IoT Hub 준비 | Microsoft Docs"
description: "Mac 컴퓨터에 도구를 설치하고, IoT Hub를 만들고 IoT Hub에 장치를 등록합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 개발, IoT 소프트웨어, IoT 클라우드 서비스, 사물 인터넷 소프트웨어, Azure CLI, Python Mac 설치, Mac에 Git 설치, gulp 실행, Node Js Mac 설치"
ms.assetid: 42f9d186-e20c-4ef9-98cc-71d39e058b06
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: f3bed8620c13372c3af50657bb54aa8dce115529


---
# <a name="get-the-tools-macos"></a>도구 얻기(macOS)
> [!div class="op_single_selector"]
> * [Windows 7 이상](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>수행할 사항

- Git, Node.js, Gulp, Python을 설치합니다.
- Azure 명령줄 인터페이스(Azure CLI) 설치. 

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-sim-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

이 단원에서는 다음 내용을 배웁니다.

- [Git](https://git-scm.com/) 및 [Node.js](https://nodejs.org/en/)를 설치하는 방법
  - Git는 오픈 소스 분산 버전 제어 시스템입니다. 이 단원에 대한 샘플 응용 프로그램은 Git에 저장됩니다.
  - Node.js는 풍부한 패키지 에코 시스템을 사용하는 JavaScript 런타임입니다.
- [NPM](https://www.npmjs.com/)을 사용하여 Node.js 개발 도구를 설치하는 방법
  - Node.js의 최소 필수 버전은 4.5 LTS입니다.
  - NPM은 Node.js에 대한 패키지 관리자 중 하나입니다.
- Visual Studio Code 설치 방법
  - Visual Studio Code는 Windows, Linux 및 macOS를 위한 간단하지만 플랫폼 간 강력한 소스 코드 편집기입니다. 또한 디버깅, 포함된 Git 컨트롤, 구문 강조, 지능형 코드 완성, 스니펫 및 코드 리팩터링에 대해 탁월한 지원을 제공합니다.
- Python을 설치하는 방법.
  - Python은 널리 사용되는 높은 수준의 범용 해석형 동적 프로그래밍 언어입니다.
- Azure CLI를 설치하는 방법.
  - Azure CLI는 Azure에 대한 다중 플랫폼 명령줄 환경을 제공합니다. 명령줄에서 리소스를 프로비전하고 관리하는 작업을 바로 수행할 수 있습니다.
- Azure CLI를 사용하여 IoT Hub를 만드는 방법

## <a name="what-you-need"></a>필요한 항목

- 도구 및 소프트웨어를 다운로드하기 위한 인터넷 연결
- OS X Yosemite(10.10) 이상을 실행하는 Mac 컴퓨터

## <a name="install-git-and-nodejs"></a>Git 및 Node.js 설치

Git 및 Node.js를 설치하려면 다음 단계를 따라 Homebrew 패키지 관리 유틸리티를 사용하세요.

1. Homebrew를 [다운로드](http://brew.sh/) 및 설치합니다. Homebrew를 이미 설치한 경우 2단계로 이동하세요.
   1. `Cmd + Space`를 누르고 `Terminal`을 입력하여 터미널을 엽니다.
   2. 다음 명령을 실행합니다.

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. 다음 명령을 실행하여 Git 및 Node.js를 설치합니다.

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Node.js 개발 도구 설치

[gulp.js](http://gulpjs.com/)를 사용하여 스크립트 배포 및 실행을 자동화합니다.

gulp를 설치하려면 터미널에서 다음 명령을 실행합니다.

```bash
npm install -g gulp
```

설치에 문제가 발생하면 [문제 해결 안내서](iot-hub-gateway-kit-c-sim-troubleshooting.md)에서 일반적인 문제에 대한 솔루션을 참조하세요.

> [!Note]
> Node.js에서 개발된 Automation 스크립트를 실행하려면 Node, NPM 및 Gulp가 필요합니다.

## <a name="install-python"></a>Python 설치

Mac OS X는 Python 2.7이 함께 제공되지만 Homebrew를 통해 Python을 설치하는 것이 좋습니다. [Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/)(Mac OS X에 Python 설치)를 참조하세요.

다음 명령을 실행하여 Python 및 pip를 설치합니다.

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Azure CLI 설치

Azure CLI를 설치하려면 다음 단계를 따르세요.

1. 터미널에서 다음 명령을 실행합니다.
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   설치에는 5분 정도 걸릴 수 있습니다.

2. 다음 명령을 실행하여 설치를 확인합니다.
   ```bash
   az iot -h
   ```
   성공적으로 설치되면 다음과 같은 출력이 표시됩니다.

   ![Azure CLI 설치 확인](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Visual Studio Code 설치

이 자습서의 뒷부분에 나오는 Visual Studio 코드를 사용하여 구성 파일을 편집합니다.

Visual Studio Code를 [다운로드](https://code.visualstudio.com/docs/setup/osx)하여 설치합니다.

## <a name="summary"></a>요약

Mac 컴퓨터에 필요한 도구 및 소프트웨어를 모두 설치했습니다. 다음 작업은 Azure CLI를 사용하여 IoT Hub를 만들고 장치를 IoT Hub에 등록하는 것입니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub 만들기 및 장치 등록](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


