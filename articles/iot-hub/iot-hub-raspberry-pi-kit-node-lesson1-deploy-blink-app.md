---
title: "깜박임 응용 프로그램 만들기 및 배포 | Microsoft Docs"
description: "GitHub에서 샘플 Node.js 응용 프로그램과, Raspberry Pi 3 보드에 이 응용 프로그램을 배포하기 위한 gulp를 복제합니다. 이 샘플 응용 프로그램은 보드에 연결된 LED를 2초마다 깜박이게 합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Raspberry Pi LED 깜박임, Raspberry Pi를 통한 LED 깜박임"
ms.assetid: a5a03a57-fe86-416f-90ff-6eca17775842
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: aee1ef8c4275f64865ff830739fc818d6bf02bd2


---
# <a name="create-and-deploy-the-blink-application"></a>깜박임 응용 프로그램 만들기 및 배포
## <a name="what-you-will-do"></a>수행할 사항
GitHub에서 샘플 Node.js 응용 프로그램을 복제하고 gulp 도구를 사용하여 Raspberry Pi 3 보드에 이 응용 프로그램을 배포합니다. 샘플 응용 프로그램은 보드에 연결된 LED를 2초마다 깜박이게 합니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.

* Pi에 관한 네트워킹 정보를 검색하기 위해 `device-discover-cli` 도구 사용 방법.
* Pi에서 샘플 응용 프로그램을 배포하고 실행하는 방법.
* Pi에서 원격으로 실행되는 응용 프로그램을 배포하고 디버깅하는 방법.

## <a name="what-you-need"></a>필요한 항목
다음 작업을 성공적으로 완료해야 합니다.

* [장치 구성](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
* [도구 얻기](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Pi의 IP 주소 및 호스트 이름 가져오기
macOS 또는 Ubuntu의 터미널이나 Windows에서 명령 프롬프트를 열고 다음 명령을 실행합니다.

```bash
devdisco list --eth
```

출력은 다음과 유사해야 합니다.

![장치 검색](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Pi의 `IP address` 및 `hostname`을 기록해 둡니다. 이 정보는 이 문서의 뒤 부분에서 필요합니다.

> [!NOTE]
> Pi가 컴퓨터와 같은 네트워크에 연결되어 있어야 합니다. 예를 들어 컴퓨터는 무선 네트워크, Pi는 유선 네트워크에 연결되었다면 devdisco 출력에 IP 주소가 표시되지 않을 수 있습니다.

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제
샘플 코드를 열려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 GitHub에서 샘플 리포지토리를 복제합니다.
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
   ```
2. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.
   
   ```bash
   cd iot-hub-node-raspberrypi-getting-started
   cd Lesson1
   code .
   ```

![Repo 구조](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

`app` 하위 폴더의 `app.js` 파일은 LED 제어 코드가 담긴 핵심 원본 파일입니다.

### <a name="install-application-dependencies"></a>응용 프로그램 종속성 설치
다음 명령을 실행하여 샘플 응용 프로그램에 필요한 라이브러리 및 기타 모듈을 설치합니다.

```bash
npm install
```

## <a name="configure-the-device-connection"></a>장치 연결 구성
장치 연결을 구성하려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 장치 구성 파일을 생성합니다.
   
   ```bash
   gulp init
   ```
   
   구성 파일 `config-raspberrypi.json`에는 Pi에 로그인하는 데 사용하는 사용자 자격 증명이 포함됩니다. 사용자 자격 증명의 유출을 방지하기 위해 구성 파일은 컴퓨터 홈 폴더의 `.iot-hub-getting-started` 하위 폴더에 생성됩니다.

2. 다음 명령을 실행하여 Visual Studio Code에서 장치 구성 파일을 엽니다.
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
3. 자리 표시자 `[device hostname or IP address]`를 앞서 "Pi의 IP 주소 및 호스트 이름 가져오기"에서 확보한 IP 주소 또는 호스트 이름과 바꿉니다.
   
   ![Config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> Raspberry Pi에 연결할 때 사용자 이름과 암호 대신 SSH 키를 사용할 수 있습니다. 이렇게 하려면 **ssh-keygen** 및 **ssh-copy-id pi@\<장치 주소\>**를 사용하여 키를 생성해야 합니다.
>
> Windows의 경우 이러한 명령은 **Git Bash**에서 사용할 수 있습니다.
>
> MacOS의 경우 **brew install ssh-copy-id**를 실행해야 합니다.
>
> 키를 Raspberry Pi에 업로드한 후, **config-raspberrypi.json**에서 **device_password**를 **device_key_path** 속성으로 바꿉니다.
>
> 업데이트된 줄은 다음과 같이 표시되어야 합니다.
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

축하합니다. Pi의 첫 번째 샘플 응용 프로그램을 만들었습니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
### <a name="install-nodejs-and--on-pi"></a>Pi에 Node.js 설치
다음 명령을 실행하여 Node.js 및 NPM을 Pi에 설치합니다.

```bash
gulp install-tools
```

이 작업의 최초 실행을 완료하려면 10분 정도 걸릴 수 있습니다.

### <a name="deploy-and-run-the-sample-app"></a>샘플 앱 배포 및 실행
다음 명령을 실행하여 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>앱 작동 확인
Pi에 연결된 LED가 2초마다 깜빡이는 것을 볼 수 있습니다.  Led가 깜박이지 않으면 [문제 해결 가이드](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 일반적인 문제에 대한솔루션을 참조하세요.
![LED 깜박임](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>요약
Pi 작동에 필요한 도구를 설치했으며 LED를 깜박이게 하는 샘플 응용 프로그램을 Pi에 배포했습니다. 이제 메시지 송수신을 위해 Azure IoT Hub에 Pi를 연결하는 다른 샘플 응용 프로그램을 만들고, 배포하고, 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 도구 얻기](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)




<!--HONumber=Nov16_HO5-->


