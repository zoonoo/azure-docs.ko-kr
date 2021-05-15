---
title: Azure Percept DK 및 IoT Edge의 일반 문제 해결
description: Azure Percept DK의 몇 가지 일반적인 문제에 대한 문제 해결 팁을 확인하세요.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c9c62ec07873272b956877ec51d8765ae0bbd100
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605640"
---
# <a name="azure-percept-dk-troubleshooting"></a>Azure Percept DK 문제 해결

Azure Percept DK에 대한 일반적인 문제 해결 팁은 아래 지침을 참조하세요.

## <a name="general-troubleshooting-commands"></a>일반 문제 해결 명령

해당 명령을 실행하려면 [개발자 키트에 SSH](./how-to-ssh-into-percept-dk.md)를 실행하고 SSH 클라이언트 프롬프트에 명령을 입력합니다.

추가 분석을 위해 임의의 출력을 .txt 파일로 리디렉션하려면 다음 구문을 사용합니다.

```console
sudo [command] > [file name].txt
```

복사할 수 있도록 .txt 파일의 사용 권한을 변경합니다.

```console
sudo chmod 666 [file name].txt
```

.txt 파일에 출력을 리디렉션하는 경우 SCP를 통해 호스트 PC에 파일을 복사합니다.

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]```는 .txt 파일을 복사할 호스트 PC의 위치를 나타냅니다. ```[remote username]```는 [설치 경험](./quickstart-percept-dk-set-up.md) 중 선택한 SSH 사용자 이름입니다.

Azure IoT Edge 명령에 대한 자세한 내용은 [Azure IoT Edge 디바이스 문제 해결 설명서](../iot-edge/troubleshoot.md)를 참조하세요.

|범주:         |명령:                    |함수:                  |
|------------------|----------------------------|---------------------------|
|OS                |```cat /etc/os-release```         |Mariner 이미지 버전 확인 |
|OS                |```cat /etc/os-subrelease```      |파생 이미지 버전 확인 |
|OS                |```cat /etc/adu-version```        |ADU 버전 확인 |
|온도       |```cat /sys/class/thermal/thermal_zone0/temp``` |devkit의 온도 확인 |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |SoftAP 로그 확인|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |Wi-Fi 서비스 로그 확인 |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |Wi-Fi 제로 터치식 프로비전 서비스 로그 확인 |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |Mariner 네트워크 스택 로그 확인 |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |Wifi 액세스 지점 구성 세부 정보 확인 |
|OOBE              |```sudo journalctl -u oobe -b```       |OOBE 로그 확인 |
|원격 분석         |```sudo azure-device-health-id```      |고유한 원격 분석 HW_ID 찾기 |
|Azure IoT Edge          |```sudo iotedge check```          |일반적인 문제에 대한 구성 및 연결 검사 실행 |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |음성 및 Vision 모듈과 같은 컨테이너 로그를 확인합니다. |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |모듈 로그, Azure IoT Edge 보안 관리자 로그, 컨테이너 엔진 로그, ```iotedge check``` JSON 출력 및 지난 시간의 기타 유용한 디버그 정보 수집 |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Azure IoT Edge 보안 관리자의 로그 보기 |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Azure IoT Edge 보안 디먼 재시작 |
|Azure IoT Edge          |```sudo iotedge list```           |배포된 Azure IoT Edge 모듈 나열 |
|기타             |```df [option] [file]```          |지정된 파일 시스템에서 사용 가능한/총 공간에 대한 정보를 표시합니다. |
|기타             |`ip route get 1.1.1.1`        |디바이스 IP 및 인터페이스 정보 표시 |
|기타             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |디바이스 IP 주소만 표시 |


```journalctl``` Wi-Fi 명령은 다음 단일 명령으로 결합될 수 있습니다.

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker 문제 해결 명령

|명령:                        |함수:                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[실행 중인 컨테이너 표시](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[디바이스에 있는 이미지 표시](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[디바이스에서 이미지 삭제](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[지정된 모듈의 컨테이너 로그 가져오기](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[모든 현수 이미지 제거](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |docker 컨테이너 다운로드 상태 확인 |

## <a name="usb-updates"></a>USB 업데이트

|오류:                                    |해결책:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX during USB flash via UUU |이 오류는 UUU 업데이트 중에 USB 연결 오류가 발생한 경우에 발생합니다. USB 케이블이 PC 또는 Percept DK 캐리어 보드의 USB 포트에 제대로 연결되어 있지 않으면, 이 양식의 오류가 발생합니다. USB 케이블의 양쪽 끝을 분리하고 다시 연결한 다음 케이블을 흔들어 안전하게 연결되었는지 확인합니다. 이 방법을 사용하면 거의 항상 문제를 해결합니다. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK 캐리어 보드 LED 상태

캐리어 보드 하우징 위에는 세 개의 작은 LED가 있습니다. LED 1 옆에 클라우드 아이콘이 인쇄되고 LED 2 옆에 Wi-Fi 아이콘이 인쇄되며 LED 3 옆에 느낌표 아이콘이 인쇄됩니다. 각 LED 상태에 대한 자세한 내용은 아래 표를 참조하세요.

|LED             |시스템 상태      |Description                      |
|----------------|-----------|---------------------------------|
|LED 1(IoT Hub) |켜기(단색) |디바이스가 IoT Hub에 연결되어 있습니다. |
|LED 2(Wi-fi)   |저속 깜박임 |디바이스를 Wi-Fi Easy Connect로 구성할 준비가 되었으며 구성기에 존재를 알리고 있습니다. |
|LED 2(Wi-fi)   |고속 깜박임 |인증에 성공하고 디바이스를 연결하는 중입니다. |
|LED 2(Wi-fi)   |켜기(단색) |인증 및 연결에 성공했습니다. 디바이스가 Wi-Fi 네트워크에 연결되어 있습니다. |
|LED 3           |해당 없음         |LED를 사용하지 않습니다. |