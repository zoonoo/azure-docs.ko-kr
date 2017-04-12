---
title: "Azure IoT에 Intel Edison(노드) 연결 - 단원 2: Azure 도구(Ubuntu) | Microsoft Docs"
description: "Ubuntu에 Python 및 Azure CLI(Azure 명령줄 인터페이스)를 설치합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure cli, IoT 클라우드 서비스, Arduino 클라우드"
ms.assetid: 6dcb34bf-54a3-4af0-ba89-95d5cfafceff
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 1b3ee6667c8ba376bbbd7a2bb61e85ec17b61b4d
ms.lasthandoff: 01/25/2017


---
# <a name="get-azure-tools-ubuntu-1604"></a>Azure 도구 얻기(Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 이상][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>수행할 사항
Azure 명령줄 인터페이스(Azure CLI) 설치. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.
* Azure CLI를 설치하는 방법.
* Azure CLI의 IoT 하위 그룹을 추가하는 방법.

## <a name="what-you-need"></a>필요한 항목
* 인터넷에 연결된 Ubuntu 컴퓨터.
* 활성 Azure 구독. 계정이 없는 경우 몇 분 만에 [무료 평가판 계정](http://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="install-the-azure-cli"></a>Azure CLI 설치
Azure CLI는 명령줄에서 직접 작업하여 리소스를 프로비전하고 관리할 수 있도록 하는 Azure를 위한 다중 플랫폼 명령줄 환경을 제공합니다.

최신 Azure CLI를 설치하려면 다음 단계를 따르세요.

1. 터미널 창에서 다음 명령을 실행합니다. Azure CLI를 설치하는 데&5;분 정도 걸릴 수 있습니다.

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. 다음 명령을 실행하여 설치를 확인합니다.

   ```bash
   az iot -h
   ```

성공적으로 설치되면 다음과 같은 출력이 표시됩니다.

![성공을 나타내는 출력](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="summary"></a>요약
Azure CLI를 설치했습니다. 다음 작업은 Azure CLI를 사용하여 Azure IoT Hub 및 장치 ID를 만드는 것입니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub 만들기 및 Intel Edison 등록][create-your-iot-hub-and-register-intel-edison]


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-mac.md

