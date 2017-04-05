---
title: "SensorTag 장치 및 Azure IoT 게이트웨이 - 단원 1: Intel NUC 설정 | Microsoft Docs"
description: "Intel NUC를 센서와 Azure IoT Hub 사이의 IoT 게이트웨이로 작동하도록 설정하여 센서 정보를 수집하고 IoT Hub에 보냅니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "iot 게이트웨이, intel nuc, nuc 컴퓨터, DE3815TYKE"
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: af2dde245fdef2984465f0c8447b558a2c770618
ms.lasthandoff: 03/30/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Intel NUC를 IoT 게이트웨이로 설정

## <a name="what-you-will-do"></a>수행할 사항

- Intel NUC를 IoT 게이트웨이로 설정합니다.
- Intel NUC에 Azure IoT 게이트웨이 SDK 패키지를 설치합니다.
- 게이트웨이 기능을 확인하려면 Intel NUC에서 "hello_world" 샘플 응용 프로그램을 실행합니다.
    
  > 문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

이 단원에서는 다음 내용을 배웁니다.

- Intel NUC와 주변 장치를 연결하는 방법
- 스마트 패키지 관리자를 사용하여 Intel NUC에 필요한 패키지를 설치 및 업데이트하는 방법
- 게이트웨이 기능을 확인하기 위해 "hello_world" 샘플 응용 프로그램을 실행하는 방법

## <a name="what-you-need"></a>필요한 항목

- 사전 설치된 Intel IoT 게이트웨이 소프트웨어 제품군을 포함하는 Intel NUC 키트 DE3815TYKE(Wind River Linux * 7.0.0.13) [Grove IoT Commercial Gateway Kit를 구매하려면 여기를 클릭하세요](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- 이더넷 케이블
- 키보드
- HDMI 또는 VGA 케이블
- HDMI 또는 VGA 포트가 있는 모니터
- 선택 사항: [Texas Instruments SensorTag(CC2650STK)](http://www.ti.com/tool/cc2650stk)

![게이트웨이 키트](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>주변 장치와 연결된 Intel NUC

아래 이미지는 다양한 주변 장치가 연결된 Intel NUC의 예제입니다.

1. 키보드에 연결됨
2. VGA 또는 HDMI 케이블로 모니터에 연결됨
3. 이더넷 케이블로 유선 네트워크에 연결됨
4. 전원 케이블로 전원 공급 장치에 연결됨

![주변 장치에 연결된 Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>SSH(보안 셸)를 통해 호스트 컴퓨터에서 Intel NUC 시스템에 연결

Intel NUC 장치의 IP 주소를 얻으려면 키보드와 모니터가 필요합니다. IP 주소를 이미 알고 있으면 이 섹션의 3단계로 건너뛸 수 있습니다.

1. 전원 단추를 눌러 Intel NUC를 켠 다음 로그인합니다.

   사용자 이름 및 암호는 모두 `root`입니다.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Intel NUC 장치에서 `ifconfig` 명령을 실행하여 Intel NUC의 IP 주소를 가져옵니다.

   다음은 명령 출력의 예제입니다.

   ![Intel NUC IP를 보여 주는 ifconfig 출력](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   이 예제에서 `inet addr:` 뒤에 나오는 값은 호스트 컴퓨터에서 Intel NUC에 연결할 때 필요한 IP 주소입니다.

3. 호스트 컴퓨터에서 다음 SSH 클라이언트 중 하나를 사용하여 Intel NUC에 연결합니다.

    - Windows용 [PuTTY](http://www.putty.org/)
    - Ubuntu 또는 macOS에 있는 기본 제공 SSH 클라이언트

   호스트 컴퓨터에서 Intel NUC를 작동하는 것이 더 효율적이고 생산적입니다. SSH 클라이언트를 통해 연결하려면 Intel NUC의 IP 주소, 사용자 이름 및 암호가 필요합니다. macOS에서 SSH 클라이언트를 사용하는 예제는 다음과 같습니다.
   ![MacOS에서 실행되는 SSH 클라이언트](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Azure IoT 게이트웨이 SDK 패키지 설치

Azure IoT 게이트웨이 SDK 패키지는 SDK 및 해당 종속성이 사전 컴파일된 이진 파일을 포함합니다. 이러한 이진 파일은 Azure IoT 게이트웨이 SDK, Azure IoT SDK 및 해당하는 도구입니다. 패키지에는 또한 게이트웨이 기능을 확인하는 "hello_world" 샘플 응용 프로그램이 포함되어 있습니다. SDK는 게이트웨이의 핵심 부분입니다. 

다음 단계에 따라 패키지를 설치합니다.

1. 터미널 창에서 다음 명령을 실행하여 IoT 클라우드 저장소를 추가합니다.

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   `rpm` 명령은 rpm 키를 가져옵니다. `smart channel` 명령은 rpm 채널을 스마트 패키지 관리자에 추가합니다. `smart update` 명령을 실행하기 전에 다음과 같은 출력이 표시됩니다.

   ![rpm 및 스마트 채널 명령 출력](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. 다음과 같이 smart update 명령을 실행합니다.

   ```bash
   smart update
   ```

3. 다음 명령을 실행하여 Azure IoT 게이트웨이 패키지를 설치합니다.

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure`는 패키지의 이름입니다. `smart install` 명령은 패키지 설치를 설치하는 데 사용됩니다.


    > '공개 키를 사용할 수 없음' 오류가 표시되면 다음 명령을 실행합니다.

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
   
   패키지가 설치된 후에 Intel NUC가 게이트웨이로 작동할 준비가 되었습니다.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Azure IoT 게이트웨이 SDK "hello_world" 샘플 응용 프로그램을 실행합니다.

다음 샘플 응용 프로그램은 `hello_world.json` 파일에서 게이트웨이를 만들고, Azure IoT 게이트웨이 SDK 아키텍처의 기본 구성 요소를 사용하여 5초마다 hellow world 메시지를 파일(log.txt)에 기록합니다.

다음 명령을 실행하여 Hello World 샘플을 실행할 수 있습니다.

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Hello World 응용 프로그램을 몇 분 동안 실행한 다음 Enter 키를 눌러 중지합니다.
![응용 프로그램 출력](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Enter 키를 누른 후에 표시되는 '잘못된 인수 핸들(NULL)' 오류는 무시할 수 있습니다.

이제 hello_world 폴더에 있는 log.txt 파일을 열어 게이트웨이가 성공적으로 실행되었는지 확인할 수 있습니다. ![log.txt 디렉터리 보기](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

다음 명령을 사용하여 log.txt를 엽니다.

```bash
vim log.txt
```

그런 다음 게이트웨이 Hello World 모듈을 통해 5초마다 작성된 로깅 메시지의 JSON 형식 출력이 될 log.txt의 내용이 표시됩니다.
![log.txt 디렉터리 보기](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="summary"></a>요약

축하합니다. 게이트웨이로 Intel NUC 설정을 완료했습니다. 이제 다음 단원으로 이동하여 호스트 컴퓨터를 설정하고, Azure IoT Hub를 만들고, Azure IoT Hub 논리적 장치를 등록할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[호스트 컴퓨터 및 Azure IoT Hub 준비](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)

