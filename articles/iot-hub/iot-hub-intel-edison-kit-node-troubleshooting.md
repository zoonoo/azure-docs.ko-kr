---
title: "Azure IoT에 Intel Edison(노드) 연결 - 단원 4: 문제 해결 | Microsoft Docs"
description: "Intel Edison Node.js 환경 문제 해결 페이지"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 문제 해결"
ms.assetid: f11c5521-8a36-44c0-baad-f5ec26ab4618
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: d77ee0be35a3f4b38c50cf9322124e4dfbba6ddc
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>문제 해결
## <a name="hardware-issues"></a>하드웨어 문제
Intel Edison의 일반적인 문제 해결에 대한 내용은 [공식 문제 해결 페이지](https://software.intel.com/en-us/node/637974)를 참조하세요.

## <a name="nodejs-package-issues"></a>Node.js 패키지 문제
### <a name="no-response-during-gulp-tasks"></a>gulp 작업 중 응답 없음
gulp 작업을 실행하다가 문제가 발생하면 디버깅을 위해 `--verbose` 옵션을 추가할 수 있습니다. `Ctrl + C`를 사용하여 현재 gulp 작업의 종료를 시도한 다음 콘솔 창에서 다음 명령을 실행하여 디버그 메시지를 확인합니다. 콘솔 출력에서 자세한 오류 메시지를 볼 수 있습니다. 

```bash
gulp --verbose
```

### <a name="npm-issues"></a>NPM 문제
다음 명령을 실행하여 NPM 패키지를 업데이트하세요.

```bash
npm install -g npm
```

문제가 지속되면 이 문서의 끝에 의견을 남기거나 [샘플 리포지토리][sample-repository]에서 GitHub 문제를 작성하세요.

## <a name="remote-debugging"></a>원격 디버깅

### <a name="run-the-sample-application-in-debug-mode"></a>디버그 모드에서 샘플 응용 프로그램 실행

```bash
gulp run --debug
```

디버그 엔진이 준비되면 콘솔 출력에서 ```Debugger listening on port 5858```이 표시될 수 있습니다.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>원격 장치에 연결하도록 VS Code 구성

왼쪽의 **디버그** 패널을 엽니다.

녹색 **디버깅 시작**(F5) 단추를 클릭합니다. VS Code에 업데이트할 **launch.json** 파일이 열립니다.

**launch.json** 파일을 다음 내용으로 업데이트하고 `[device hostname or IP address]`를 실제 장치 IP 주소 또는 호스트 이름으로 바꿉니다.  

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![원격 디버깅 구성](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>원격 응용 프로그램에 연결

녹색 **디버깅 시작**(F5) 단추를 클릭하여 디버깅을 시작합니다.

디버거에 대해 자세히 알아보려면 [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging)(VS Code의 JavaScript)를 참조하세요.

![대화형 원격 디버깅](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Azure-CLI 문제
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
[장치 탐색기](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)는 Windows 로컬 컴퓨터에서 실행되며 Azure의 IoT Hub에 연결합니다. 다음과 같은 [IoT Hub 끝점](iot-hub-devguide.md)과 통신합니다.

- _장치 ID 관리_: IoT Hub에 등록된 장치를 프로비전하고 관리합니다.
- _장치-클라우드 받기_: 장치에서 IoT Hub로 보내는 메시지를 모니터링할 수 있습니다.
- _클라우드-장치 보내기_: IoT Hub에서 장치로 메시지를 보낼 수 있습니다.

모든 기능을 사용하도록 이 도구에서 `IoT hub connection string`을 구성합니다.

### <a name="iot-hub-explorer"></a>IoT hub Explorer
[IoT hub Explorer](https://github.com/Azure/iothub-explorer)는 장치 클라이언트를 관리하기 위한 샘플 다중 플랫폼 CLI입니다. 이 도구를 사용하여 ID 레지스트리에서 장치를 관리하고, 장치-클라우드 메시지를 모니터링하고, 클라우드-장치 명령을 보낼 수 있습니다.

iothub-explorer 도구의 최신(시험판) 버전을 설치하려면 명령줄 환경에서 다음 명령을 실행합니다.

```bash
npm install -g iothub-explorer@latest
```

다음 명령을 사용하면 모든 iothub-explorer 명령 및 매개 변수에 대한 추가 도움말을 볼 수 있습니다.

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Azure 포털
전체 CLI 환경은 모든 Azure 리소스를 만들고 관리하는 데 도움이 됩니다. [Azure Portal](../azure-portal-overview.md)을 사용하여 Azure 리소스에 대한 프로비전, 관리, 디버깅을 지원할 수도 있습니다.

## <a name="azure-storage-issues"></a>Azure Storage 문제
[Microsoft Azure Storage 탐색기(미리 보기)](http://storageexplorer.com)는 Windows, macOS 및 Linux에서 [Azure Storage](https://azure.microsoft.com/en-us/services/storage/) 데이터 작업에 사용할 수 있는 Microsoft의 독립 실행형 앱입니다. 이 도구를 사용하면 테이블에 연결하여 그 안에 있는 데이터를 볼 수 있습니다. Azure Storage 문제를 해결하는 데 이 도구를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 페이지에는 Intel Edison 키트의 가장 일반적인 문제만 나와 있습니다. 또한 하단에 의견을 남겨 추가 문제 해결에 대한 문제를 보고할 수 있습니다.

[Intel Edison(Node.js) 시작](iot-hub-intel-edison-kit-node-get-started.md)으로 돌아가기

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-node-edison-getting-started
