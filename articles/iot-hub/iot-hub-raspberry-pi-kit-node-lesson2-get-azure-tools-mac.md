---
title: "Azure IoT에 Raspberry Pi(노드) 연결 - 단원 2: 도구 다운로드(Ubuntu) | Microsoft Docs"
description: "macOS에 Python 및 Azure 명령줄 인터페이스(Azure CLI)를 설치합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT 클라우드 서비스, Azure CLI"
ms.assetid: 1814b703-2d81-45db-aff0-eb338c97f120
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 455e5a28f54fb0af4e7d4601ecef666b74950169
ms.lasthandoff: 03/10/2017


---
# <a name="get-azure-tools-macos-1010"></a>Azure 도구 얻기(macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 이상](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>수행할 사항
Azure 명령줄 인터페이스(Azure CLI) 설치. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.
* Azure CLI를 설치하는 방법.
* Azure CLI의 IoT 하위 그룹을 추가하는 방법.

## <a name="what-you-need"></a>필요한 항목
* 인터넷에 연결된 Mac.
* 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [무료 Azure 평가판 계정](http://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="install-python"></a>Python 설치
macOS는 기본적으로 Python 2.7이 탑재되어 있지만 Homebrew를 통해 Python을 설치하는 것이 좋습니다. [macOS에서 Python 설치](http://docs.python-guide.org/en/latest/starting/install/osx/)를 참조하세요.

다음 명령을 실행하여 Python 및 pip를 설치합니다.

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Azure CLI 설치
Azure CLI는 Azure에 대한 다중 플랫폼 명령줄 환경을 제공합니다. 명령줄에서 리소스를 프로비전하고 관리하는 작업을 바로 수행할 수 있습니다. 

최신 Azure CLI를 설치하려면 다음 단계를 따르세요.

1. 터미널 창에서 다음 명령을 실행합니다. Azure CLI를 설치하는 데&5;분 정도 걸릴 수 있습니다.

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. 다음 명령을 실행하여 설치를 확인합니다.

   ```bash
   az iot -h
   ```

성공적으로 설치되면 다음과 같은 출력이 표시됩니다.

![성공을 나타내는 출력](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="summary"></a>요약
Azure CLI를 설치했습니다. 다음 작업은 Azure CLI를 사용하여 Azure IoT Hub 및 장치 ID를 만드는 것입니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub 만들기 및 Raspberry Pi 3 등록](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


