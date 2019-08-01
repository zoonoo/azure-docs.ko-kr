---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640436"
---
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* **Windows**

    * [Python 2.x 또는](https://www.python.org/downloads/)3.x. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [*pip* Python 패키지 관리 시스템을 설치 또는 업그레이드](https://pip.pypa.io/en/stable/installing/)해야 할 수 있습니다.

    * Windows OS를 사용 하는 경우 Python에서 네이티브 Dll을 사용할 수 있도록 올바른 버전의 [비주얼 C++ 재배포 가능 패키지가](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) 있는지를 알고 있어야 합니다. 최신 버전을 사용 하는 것이 좋습니다.

    * 필요한 경우 명령을 사용 하 여 [iothub-client](https://pypi.org/project/azure-iothub-device-client/) 패키지를 설치 합니다.`pip install azure-iothub-device-client`

    * 필요한 경우 명령을 사용 하 여 [iothub-client](https://pypi.org/project/azure-iothub-service-client/) 패키지를 설치 합니다.`pip install azure-iothub-service-client`

* **Mac OS**

    Mac OS의 경우 Python 3.7.0 (또는 2.7) + 보강 부스트-1.67 + 말아 7.61.1 (모두 homebrew를 통해 설치 됨)가 필요 합니다. 다른 모든 배포/OS는 작동 하지 않는 다른 버전의 향상 된 & 종속성을 포함 하 여 런타임에 ImportError를 발생 시킬 수 있습니다.

    `azure-iothub-service-client` 및 `azure-iothub-device-client`에 대한 *pip* 패키지는 현재 Windows OS에만 사용할 수 있습니다. Linux/Mac OS의 경우 [Python 용 개발 환경 준비](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) 게시물의 linux 및 Mac OS 관련 섹션을 참조 하세요.

> [!NOTE]
> 샘플에서 iothub_client를 가져올 때 몇 가지 오류 보고서가 있습니다. **ImportError** 문제를 처리 하는 방법에 대 한 자세한 내용은 [ImportError 문제 처리](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)를 참조 하세요.
