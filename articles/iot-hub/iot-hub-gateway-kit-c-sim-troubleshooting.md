---
title: "시뮬레이션된 장치 및 Azure IoT 게이트웨이 - 문제 해결 | Microsoft Docs"
description: "Intel NUC 게이트웨이 문제 해결 페이지"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 문제, 사물 인터넷 문제"
ms.assetid: 3ee8f4b0-5799-40a3-8cf0-8d5aa44dbc2b
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a70de978d4636a31644702c6f3a8ca0d0b80f5c2


---
# <a name="troubleshooting"></a>문제 해결

## <a name="hardware-issues"></a>하드웨어 문제

### <a name="ti-sensortag-cannot-be-connected"></a>TI SensorTag는 연결할 수 없음

SensorTag 연결 문제를 해결하려면 [SensorTag 앱](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide)을 사용합니다.

### <a name="have-an-issue-with-intel-nuc"></a>Intel NUC에 문제가 있음

부팅 문제를 해결하려면 [문제 해결 Intel® NUC 제품에서 부트되지 않는 문제](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html)를 참조하세요.

운영 체제 문제를 해결하려면 [Intel NUC의 운영 체제 문제 해결](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html)을 참조하세요.

다른 문제를 해결하려면 [Intel NUC에 대한 깜박임 코드 및 경고음 코드](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html)를 참조하세요.

## <a name="nodejs-package-issues"></a>Node.js 패키지 문제

### <a name="no-response-during-gulp-tasks"></a>gulp 작업 중 응답 없음

gulp 작업을 실행하다가 문제가 발생하면 디버깅을 위해 `--verbose` 옵션을 추가할 수 있습니다. `Ctrl + C`를 사용하여 현재 gulp 작업의 종료를 시도한 다음 콘솔 창에서 다음 명령을 실행하여 디버그 메시지를 확인합니다. 콘솔 출력에서 자세한 오류 메시지를 볼 수 있습니다.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>장치 검색 문제

`discover-sensortag` 명령에 대한 일반적인 문제 해결에 도움이 필요하면 [wiki 페이지](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl)를 확인하세요.

### <a name="npm-issues"></a>NPM 문제

다음 명령을 실행하여 NPM 패키지를 업데이트하세요.

```bash
npm install -g npm
```

문제가 지속되면 이 문서의 끝에 의견을 남기거나 [샘플 리포지토리](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started)에서 GitHub 문제를 작성하세요.

## <a name="remote-debugging"></a>원격 디버깅
> 아래 지침은 이 자습서에서 사용된 node.js 스크립트를 디버깅하기 위한 것입니다.
### <a name="run-the-sample-application-in-debug-mode"></a>디버그 모드에서 샘플 응용 프로그램 실행

다음 명령을 실행하여 디버그 모드에서 샘플 응용 프로그램을 실행합니다.

```bash
gulp run --debug
```

디버그 엔진이 준비되면 콘솔 출력에 `Debugger listening on port 5858`이 표시됩니다.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>원격 장치에 연결하도록 Visual Studio Code 구성

1. 왼쪽의 **디버그** 창을 엽니다.
2. 녹색 **디버깅 시작**(F5) 단추를 클릭합니다. Visual Studio Code에 `launch.json` 파일이 열립니다.
3. 다음 내용이 포함된 `launch.json` 파일을 업데이트합니다. `[device hostname or IP address]`를 실제 장치 IP 주소 또는 호스트 이름으로 바꿉니다.

   ``` json
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
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![원격 디버깅 구성](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>원격 응용 프로그램에 연결

녹색 **디버깅 시작**(F5) 단추를 클릭하여 디버깅을 시작합니다.

디버거에 대해 자세히 알아보려면 [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging)(VS Code의 JavaScript)를 참조하세요.

![디버깅 BLE 샘플](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Azure CLI 문제

Azure 명령줄 인터페이스(Azure CLI)는 미리 보기 빌드입니다. 해결책을 검색하려면 [Preview Install Guide](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)(미리 보기 설치 가이드)를 사용하세요.

도구에서 버그가 발견되면 GitHub 리포지토리의 **문제** 섹션에 [문제](https://github.com/Azure/azure-cli/issues)를 기록해 주세요.

일반적인 문제 해결에 도움이 필요하면 [readme](https://github.com/Azure/azure-cli/blob/master/README.rst)(추가 정보)를 확인하세요.

"요구 사항을 만족하는 버전을 찾을 수 없습니다"라는 메시지가 나타나면 다음 명령을 실행하여 pip를 최신 버전으로 업그레이드합니다.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python 설치 문제

### <a name="legacy-installation-issues-macos"></a>레거시 설치 문제(macOS)

pip를 설치하는 경우 이전 패키지가 **su** 권한으로 설치되어 있으면 권한 오류가 throw됩니다. 이러한 문제는 brew(macOS)를 사용하는 이전 Python 설치가 완전히 제거되지 않으면 발생합니다. 이전 설치의 일부 pip 패키지가 root에 의해 생성되었고 이로 인해 권한 오류가 발생합니다. 해결책은 root에 의해 설치된 해당 패키지를 제거하는 것입니다. 다음 단계를 수행하여 이 작업을 완료하세요.

1. `/usr/local/lib/python2.7/site-packages`으로 이동합니다.
2. root에 의해 생성된 패키지 나열: `ls -l | grep root`
3. 2단계의 패키지 제거: `sudo rm -rf {package name}`
4. Python을 다시 설치합니다.

## <a name="azure-iot-hub-issues"></a>Azure IoT Hub 문제

Azure CLI를 사용하여 Azure IoT Hub 프로비저닝을 완료했고 IoT Hub에 연결하는 장치를 관리할 도구가 필요하다면 다음 도구를 사용해 보세요.

### <a name="device-explorer"></a>장치 탐색기

[장치 탐색기](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)는 Windows 로컬 컴퓨터에서 실행되며 Azure의 IoT Hub에 연결합니다. 다음과 같은 [IoT Hub 끝점](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/)과 통신합니다.

- 장치 ID 관리: IoT Hub에 등록된 장치를 프로비전하고 관리합니다.
- 장치-클라우드 받기: 장치에서 IoT Hub로 보내는 메시지를 모니터링할 수 있습니다.
- 클라우드-장치 보내기: IoT Hub에서 장치로 메시지를 보낼 수 있습니다.

모든 기능을 사용하도록 이 도구에서 IoT Hub 연결 문자열을 구성합니다.

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer)는 장치 클라이언트를 관리하기 위한 샘플 다중 플랫폼 CLI 도구입니다. 이 도구를 사용하여 ID 레지스트리에서 장치를 관리하고, 장치-클라우드 메시지를 모니터링하고, 클라우드-장치 명령을 보낼 수 있습니다.

iothub-explorer 도구의 최신(시험판) 버전을 설치하려면 다음 명령을 실행합니다.

```bash
npm install -g iothub-explorer@latest
```

모든 iothub-explorer 명령 및 매개 변수에 대한 추가 도움말을 보려면 다음 명령을 실행합니다.

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Azure 포털

전체 CLI 환경은 모든 Azure 리소스를 만들고 관리하는 데 도움이 됩니다. [Azure Portal](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/)을 사용하여 Azure 리소스에 대한 프로비전, 관리, 디버깅을 지원할 수도 있습니다.

## <a name="azure-storage-issues"></a>Azure Storage

[Microsoft Azure Storage 탐색기(미기 보기)](http://storageexplorer.com/)는 Windows, macOS 및 Linux에서 Azure Storage 데이터 작업에 사용할 수 있는 Microsoft의 독립 실행형 앱입니다. 이 도구를 사용하면 테이블에 연결하여 그 안에 있는 데이터를 볼 수 있습니다. Azure Storage 문제를 해결하는 데 이 도구를 사용할 수 있습니다.



<!--HONumber=Jan17_HO4-->


