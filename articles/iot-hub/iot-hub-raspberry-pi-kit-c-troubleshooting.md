---
title: "Azure IoT에 Raspberry Pi(C) 연결 - 문제 해결 | Microsoft Docs"
description: "Raspberry Pi Node.js 환경 문제 해결 페이지"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 문제, 사물 인터넷 문제"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: 3653c79b-8a0c-41d4-b0bf-f6b4edb4d233
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 52d76bb967471ae49b55b0fcb930c223984b39e3
ms.lasthandoff: 01/24/2017


---
# <a name="troubleshooting"></a>문제 해결
## <a name="hardware-issues"></a>하드웨어 문제
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>응용 프로그램은 잘 실행되는데 LED가 깜빡이지 않음
이 문제는 항상 하드웨어 회로 연결과 관련이 있습니다. 다음 단계를 사용하여 문제를 확인하세요.

1. 보드에서 올바른 **GPIO**를 선택했는지 확인합니다. 두 포트는 **GPIO GND(핀 6)** 및 **GPIO 04(핀 7)**이어야 합니다.
2. LED의 극성이 올바른지 확인하세요. 다리가 긴 쪽이 **양극** 즉, 양극 핀을 나타냅니다.
3. Raspberry Pi 3에서 **3.3V 핀**과 **GND 핀**을 사용합니다. Pi를 DC 전원으로 처리합니다. LED가 제대로 작동하는지 확인합니다.

![LED 사양](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>다른 하드웨어 문제
Raspberry Pi 3의 일반적인 문제 해결에 대한 내용은 [공식 문제 해결 페이지](http://elinux.org/R-Pi_Troubleshooting)를 참조하세요.

## <a name="nodejs-package-issues"></a>Node.js 패키지 문제
### <a name="no-response-during-gulp-tasks"></a>gulp 작업 중 응답 없음
gulp 작업을 실행하다가 문제가 발생하면 디버깅을 위해 `--verbose` 옵션을 추가할 수 있습니다. `Ctrl + C`를 사용하여 현재 gulp 작업의 종료를 시도한 다음 콘솔 창에서 다음 명령을 실행하여 디버그 메시지를 확인합니다. 콘솔 출력에서 자세한 오류 메시지를 볼 수 있습니다. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>장치 검색 문제
`devdisco` 명령에 대한 일반적인 문제 해결에 도움이 필요하면 [readme](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md)(추가 정보)를 확인하세요.

### <a name="npm-issues"></a>NPM 문제
다음 명령을 실행하여 NPM 패키지를 업데이트하세요.

```bash
npm install -g npm
```

문제가 지속되면 이 문서의 끝에 의견을 남기거나 [샘플 리포지토리](https://github.com/Azure-Samples/iot-hub-c-raspberrypi-getting-started)에서 GitHub 문제를 작성하세요.

## <a name="remote-debugging"></a>원격 디버깅

원격 디버깅 지원은 Visual Studio Code C/C++ 확장에서 곧 사용할 수 있습니다.

한편 좋아하는 SSH 터미널을 통해 GDB를 사용할 수 있습니다.

```bash
cd c-pi-lesson-x
sudo gdb app
```

## <a name="azure-cli-issues"></a>Azure CLI 문제
Azure 명령줄 인터페이스(Azure CLI)는 미리 보기 빌드입니다. 솔루션을 찾으려면 [미리 보기 설치 가이드](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)에서 솔루션을 참조하세요. 예상대로 명령이 작동하지 않으면 Azure-cli를 최신 버전으로 업그레이드하세요.

도구에서 버그가 발견되면 GitHub 리포지토리의 **문제** 섹션에 [문제](https://github.com/Azure/azure-cli/issues)를 기록해 주세요.

일반적인 문제 해결에 도움이 필요하면 [readme](https://github.com/Azure/azure-cli/blob/master/README.rst)(추가 정보)를 확인하세요.

"요구 사항을 만족하는 버전을 찾을 수 없습니다"라는 메시지가 나타나면 다음 명령을 실행하여 pip를 최신 버전으로 업그레이드합니다.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python 설치 문제
### <a name="legacy-installation-issues-macos"></a>레거시 설치 문제(macOS)
**pip**를 설치하는 경우 이전 패키지가 **su** 권한으로 설치되어 있으면 권한 오류가 발생합니다. 이러한 문제는 brew(macOS)를 사용하는 이전 Python 설치가 완전히 제거되지 않으면 발생합니다. 이전 설치의 일부 **pip** 패키지가 root에 의해 생성되었고 이로 인해 사용 권한 오류가 발생합니다. 해결책은 root에 의해 설치된 해당 패키지를 제거하는 것입니다. 다음 단계를 수행하여 이 작업을 완료하세요.

1. /usr/local/lib/python2.7/site-packages로 이동
2. root에 의해 생성된 패키지를 나열입니다. `ls -l | grep root`
3. 2단계의 패키지 제거: `sudo rm -rf {package name}`
4. Python을 다시 설치합니다.

## <a name="azure-iot-hub-issues"></a>Azure IoT Hub 문제
`azure-cli`를 사용하여 Azure IoT Hub 프로비저닝을 완료했고 IoT Hub에 연결하는 장치를 관리할 도구가 필요하다면 다음 도구를 사용해 보세요.

### <a name="device-explorer"></a>장치 탐색기
[장치 탐색기](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)는 Windows 로컬 컴퓨터에서 실행되며 Azure의 IoT Hub에 연결합니다. 다음과 같은 [IoT Hub 끝점](iot-hub-devguide.md)과 통신합니다.

* *장치 ID 관리*: IoT Hub에 등록된 장치를 프로비전하고 관리합니다.
* *장치-클라우드 받기*: 장치에서 IoT Hub로 보내는 메시지를 모니터링할 수 있습니다.
* *클라우드-장치 보내기*: IoT Hub에서 장치로 메시지를 보낼 수 있습니다.

모든 기능을 사용하도록 이 도구에서 `IoT hub connection string`을 구성합니다.

### <a name="iot-hub-explorer"></a>IoT hub Explorer
[IoT hub Explorer](https://github.com/Azure/iothub-explorer)는 장치 클라이언트를 관리하기 위한 샘플 다중 플랫폼 CLI입니다. 이 도구를 사용하여 ID 레지스트리에서 장치를 관리하고, 장치-클라우드 메시지를 모니터링하고, 클라우드-장치 명령을 보낼 수 있습니다.

iothub-explorer 도구의 최신(시험판) 버전을 설치하려면 명령줄 환경에서 다음 명령을 실행합니다.

```
npm install -g iothub-explorer@latest
```

다음 명령을 사용하면 모든 iothub-explorer 명령 및 매개 변수에 대한 추가 도움말을 볼 수 있습니다.

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Azure 포털
전체 CLI 환경은 모든 Azure 리소스를 만들고 관리하는 데 도움이 됩니다. [Azure Portal](../azure-portal-overview.md)을 사용하여 Azure 리소스에 대한 프로비전, 관리, 디버깅을 지원할 수도 있습니다.

## <a name="azure-storage-issues"></a>Azure Storage 문제
[Microsoft Azure Storage 탐색기(미기 보기)](http://storageexplorer.com)는 Windows, macOS 및 Linux에서 Azure Storage 데이터 작업에 사용할 수 있는 Microsoft의 독립 실행형 앱입니다. 이 도구를 사용하면 테이블에 연결하여 그 안에 있는 데이터를 볼 수 있습니다. Azure Storage 문제를 해결하는 데 이 도구를 사용할 수 있습니다.

