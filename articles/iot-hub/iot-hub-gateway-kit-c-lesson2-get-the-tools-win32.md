---
title: "SensorTag 장치 및 Azure IoT 게이트웨이 - 단원 2: 도구 다운로드(Windows) | Microsoft Docs"
description: "Windows를 실행하는 호스트 컴퓨터에 도구 및 소프트웨어를 설치하고, IoT Hub를 만들어 장치를 IoT Hub에 등록합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 개발, IoT 소프트웨어, IoT 클라우드 서비스, 사물 인터넷 소프트웨어, Azure CLI, Windows에 Git 설치, gulp 실행, Node Js Windows 설치, Windows에 NPM 설치, Windows에 Python 설치"
ms.assetid: 18ae6ee4-574a-4d5f-9838-ca2a78165628
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 4a854222be9aaf6733f1c256dd75195927299099
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-windows-7-and-later"></a>도구 얻기(Windows 7 이상)
> [!div class="op_single_selector"]
> * [Windows 7 이상](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>수행할 사항

- Git, Node.js, Gulp, Python을 설치합니다.
- Azure 명령줄 인터페이스(Azure CLI) 설치. 

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

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
- Windows 컴퓨터

## <a name="install-git-and-nodejs"></a>Git 및 Node.js 설치

다음 링크를 클릭하여 Windows용 Git 및 Node.js LTS를 다운로드하여 설치합니다.

- [Windows용 Git 얻기](https://git-scm.com/download/win/)
- [Windows용 Node.js LTS 얻기](https://nodejs.org/en/)

## <a name="install-nodejs-development-tools"></a>Node.js 개발 도구 설치

[gulp.js](http://gulpjs.com/)를 사용하여 스크립트 배포 및 실행을 자동화합니다.

`Windows + R`을 누르고 `cmd`를 입력한 후 `Enter`을 눌러 명령 프롬프트 창을 열고 다음 명령을 실행합니다.

```cmd
npm install -g gulp
```

설치에 문제가 발생하면 [문제 해결 안내서](iot-hub-gateway-kit-c-troubleshooting.md)에서 일반적인 문제에 대한 솔루션을 참조하세요.

> [!Note]
> Node.js에서 개발된 Automation 스크립트를 실행하려면 Node, NPM 및 Gulp가 필요합니다.

## <a name="install-python"></a>Python 설치

Python 2.7, 3.4 또는 3.5에서 선택할 수 있습니다. 이 자습서에서 Python 2.7을 사용합니다. Python을 이미 설치한 경우 다음 섹션으로 이동하세요.

[Windows용 Python 얻기](https://www.python.org/downloads/)

python.exe 및 pip.exe가 설치되어 있는 폴더의 경로를 시스템 `PATH` 환경 변수에 추가해야 합니다. 기본적으로 python.exe는 `C:\Python27`에, pip.exe는 `C:\Python27\Scripts`에 설치되어 있습니다.

## <a name="install-the-azure-cli"></a>Azure CLI 설치

Azure CLI를 설치하려면 다음 단계를 따르세요.

1. 관리자로 명령 프롬프트 창을 엽니다.

2. 다음 명령을 실행하여 CLI 에이전트를 설치합니다.

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   설치에는 5분 정도 걸릴 수 있습니다.

3. 다음 명령을 실행하여 설치를 확인합니다.

   ```cmd
   az iot -h
   ```

   성공적으로 설치되면 다음과 같은 출력이 표시됩니다.

   ![Azure CLI 설치 확인](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

## <a name="install-visual-studio-code"></a>Visual Studio Code 설치

이 자습서의 뒷부분에 나오는 Visual Studio 코드를 사용하여 구성 파일을 편집합니다.

Visual Studio Code를 [다운로드](https://code.visualstudio.com/docs/setup/windows)하여 설치합니다.

## <a name="summary"></a>요약

호스트 컴퓨터에 필요한 모든 도구 및 소프트웨어를 설치했습니다. 다음 작업은 Azure CLI를 사용하여 IoT Hub를 만들고 장치를 IoT Hub에 등록하는 것입니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub 만들기 및 장치 등록](iot-hub-gateway-kit-c-lesson2-register-device.md)

