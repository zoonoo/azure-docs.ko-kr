---
title: "시뮬레이션된 장치 및 Azure IoT 게이트웨이 - 단원 1: NUC 설정 | Microsoft Docs"
description: "Intel NUC를 센서와 Azure IoT Hub 사이의 IoT 게이트웨이로 작동하도록 설정하여 센서 정보를 수집하고 IoT Hub에 보냅니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "iot 게이트웨이, intel nuc, nuc 컴퓨터, DE3815TYKE"
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 399ac2d571b65503da7d9cc47d2dec9aa5e4c3d7


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Intel NUC를 IoT 게이트웨이로 설정

## <a name="what-you-will-do"></a>수행할 사항

- Intel NUC를 IoT 게이트웨이로 설정합니다.
- Intel NUC에 Azure IoT 게이트웨이 SDK 패키지를 설치합니다.
- 게이트웨이 기능을 확인하려면 Intel NUC에서 "hello_world" 샘플 응용 프로그램을 실행합니다.
문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-sim-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

이 단원에서는 다음 내용을 배웁니다.

- Intel NUC와 주변 장치를 연결하는 방법
- 스마트 패키지 관리자를 사용하여 Intel NUC에 필요한 패키지를 설치 및 업데이트하는 방법
- 게이트웨이 기능을 확인하기 위해 "hello_world" 샘플 응용 프로그램을 실행하는 방법

## <a name="what-you-need"></a>필요한 항목

- 사전 설치된 Intel IoT 게이트웨이 소프트웨어 제품군을 포함하는 Intel NUC 키트 DE3815TYKE(Wind River Linux *&7;.0.0.13)
- 이더넷 케이블
- 키보드
- HDMI 또는 VGA 케이블
- HDMI 또는 VGA 포트가 있는 모니터

![게이트웨이 키트](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>주변 장치와 연결된 Intel NUC

아래 이미지는 다양한 주변 장치가 연결된 Intel NUC의 예제입니다.

1. 키보드에 연결됨
2. VGA 또는 HDMI 케이블 모니터에 연결됨
3. 이더넷 케이블을 통해 유선된 네트워크에 연결됨
4. 전원 케이블로 전원 공급 장치에 연결됨

![주변 장치에 연결된 Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>SSH(보안 셸)를 통해 호스트 컴퓨터에서 Intel NUC 시스템에 연결

NUC 장치의 IP 주소를 얻기 위해 키보드 및 모니터가 필요합니다. IP 주소를 이미 알고 있는 경우 이 섹션의 3단계로 건너뛸 수 있습니다.

1. 전원 단추를 눌러 Intel NUC를 켜고 시스템에 로그인합니다.

   사용자 이름 및 암호는 모두 `root`입니다.

2. `ifconfig` 명령을 실행하여 NUC의 IP 주소를 가져옵니다. 이 단계는 NUC 장치에서 수행됩니다.

   다음은 명령 출력의 예제입니다.

   ![NUC IP를 보여주는 ifconfig 출력](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   이 예제에서는 `inet addr:` 뒤에 나오는 값은 호스트 컴퓨터에서 Intel NUC로 원격 연결을 하려는 경우에 필요한 IP 주소입니다.

3. 호스트 컴퓨터에서 다음 중 하나의 SSH 클라이언트를 사용하여 Intel NUC에 연결합니다.

   - Windows용 [PuTTY](http://www.putty.org/)입니다.
   - Ubuntu 또는 macOS에 있는 기본 제공 SSH 클라이언트입니다.

   호스트 컴퓨터의 Intel NUC에서 작동하기에 보다 효율적이고 생산적입니다. IP 주소, 사용자 이름 및 SSH 클라이언트를 통해 NUC 연결할 때 사용할 암호가 필요합니다. MacOS에서 SSH 클라이언트를 사용하는 예제입니다.
   ![MacOS에서 실행되는 SSH 클라이언트](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Azure IoT 게이트웨이 SDK 패키지 설치

Azure IoT 게이트웨이 SDK 패키지는 SDK 및 해당 종속성이 사전 컴파일된 이진 파일을 포함합니다. 이러한 이진 파일은 Azure IoT 게이트웨이 SDK, Azure IoT SDK 및 해당하는 도구입니다. 또한 이 패키지에는 게이트웨이 기능을 확인하는 데 사용되는 "hello_world" 샘플 응용 프로그램이 포함되어 있습니다. SDK는 게이트웨이의 핵심 부분입니다. 패키지를 설치하려면 다음 단계를 따르세요.

1. 터미널 창에서 다음 명령을 실행하여 IoT 클라우드 저장소를 추가합니다.

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   `rpm` 명령은 rpm 키를 가져옵니다. `smart channel` 명령은 rpm 채널을 스마트 패키지 관리자에 추가합니다. `smart update` 명령을 실행하기 전에 아래와 같은 출력을 볼 수 있습니다.

   ![rpm 및 스마트 채널 명령 출력](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. 다음 명령을 실행하여 패키지를 설치합니다.

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure`는 패키지의 이름입니다. `smart install` 명령은 패키지 설치를 설치하는 데 사용됩니다.

   패키지를 설치한 후 Intel NUC는 게이트웨이로 작동해야 합니다.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Azure IoT 게이트웨이 SDK "hello_world" 샘플 응용 프로그램을 실행합니다.

`azureiotgatewaysdk/samples`로 이동하여 샘플 "hello_world" 샘플 응용 프로그램을 실행합니다. 이 샘플 응용 프로그램은 `hello_world.json` 파일에서 게이트웨이를 만들고 Azure IoT 게이트웨이 SDK 아키텍처의 기본 구성 요소를 사용하여 5초마다 파일에 hellow world 메시지를 기록합니다.

다음 명령을 실행하여 샘플 "hello_world" 샘플 응용 프로그램을 실행합니다.

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

게이트웨이 기능이 올바르게 작동하는 경우 샘플 응용 프로그램은 다음과 같은 출력을 생성합니다.

![응용 프로그램 출력](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="summary"></a>요약

축하합니다. 게이트웨이로 Intel NUC 설정을 완료했습니다. 이제 호스트 컴퓨터를 설정하고 Azure IoT hub를 만들고 Azure IoT hub 논리적 장치를 등록하는 다음 단원으로 이동할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[호스트 컴퓨터 및 Azure IoT Hub 준비](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)



<!--HONumber=Jan17_HO4-->


