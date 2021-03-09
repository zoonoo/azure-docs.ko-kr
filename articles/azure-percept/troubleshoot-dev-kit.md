---
title: Azure Percept 진한 및 IoT Edge의 일반적인 문제 해결
description: 온 보 딩 환경에서 발견 된 몇 가지 일반적인 문제에 대 한 문제 해결 팁을 확인 하세요.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a6d099e8d267c9fe03e0bb676276e7a4ab8157ab
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521529"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Azure Percept 진한 (dev kit) 문제 해결

Azure Percept에 대 한 일반적인 문제 해결 팁은 아래 지침을 참조 하세요.

## <a name="general-troubleshooting-commands"></a>일반 문제 해결 명령

이러한 명령을 실행 하려면 
1. [Dev kit의 Wi-Fi AP](./quickstart-percept-dk-set-up.md) 에 연결
1. [Dev kit로 SSH](./how-to-ssh-into-percept-dk.md)
1. SSH 터미널에 명령 입력

추가 분석을 위해 모든 출력을 .txt 파일로 리디렉션하려면 다음 구문을 사용 합니다.

```console
[command] > [file name].txt
```

.Txt 파일에 출력을 리디렉션하는 경우 SCP를 통해 호스트 PC에 파일을 복사 합니다.

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` 는 .txt 파일을 복사할 호스트 PC의 위치를 나타냅니다. ```[remote username]```[설치 환경](./quickstart-percept-dk-set-up.md)에서 선택 된 SSH 사용자 이름입니다. OOBE를 실행 하는 동안 SSH 로그인을 설정 하지 않은 경우 원격 사용자 이름은 ```root``` 입니다.

Azure IoT Edge 명령에 대 한 자세한 내용은 [Azure IoT Edge 장치 문제 해결 설명서](https://docs.microsoft.com/azure/iot-edge/troubleshoot)를 참조 하세요.

|범주:         |명령:                    |함수:                  |
|------------------|----------------------------|---------------------------|
|OS                |```cat /etc/os-release```         |Mariner 이미지 버전 확인 |
|OS                |```cat /etc/os-subrelease```      |파생 이미지 버전 확인 |
|OS                |```cat /etc/adu-version```        |ADU 버전 확인 |
|온도       |```cat /sys/class/thermal/thermal_zone0/temp``` |devkit의 온도 확인 |
|Wi-Fi             |```journalctl -u hostapd.service``` |SoftAP 로그 확인|
|Wi-Fi             |```journalctl -u wpa_supplicant.service``` |Wi-Fi services 로그 확인 |
|Wi-Fi             |```journalctl -u ztpd.service```  |Wi-Fi 제로 터치식 프로 비전 서비스 로그를 확인 합니다. |
|Wi-Fi             |```journalctl -u systemd-networkd``` |Mariner 네트워크 스택 로그를 확인 합니다. |
|Wi-Fi             |```/data/misc/wifi/hostapd_virtual.conf``` |wifi 액세스 지점 구성 세부 정보 확인 |
|OOBE              |```journalctl -u oobe -b```       |OOBE 로그 확인 |
|원격 분석         |```azure-device-health-id```      |고유한 원격 분석 HW_ID 찾기 |
|Azure IoT Edge          |```sudo iotedge check```          |일반적인 문제에 대 한 구성 및 연결 검사 실행 |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |음성 및 비전 모듈과 같은 컨테이너 로그를 확인 합니다. |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |모듈 로그, Azure IoT Edge security manager 로그, 컨테이너 엔진 로그, ```iotedge check``` JSON 출력 및 지난 시간의 기타 유용한 디버그 정보 수집 |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Azure IoT Edge 보안 관리자의 로그 보기 |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Azure IoT Edge 보안 데몬을 다시 시작 합니다. |
|Azure IoT Edge          |```sudo iotedge list```           |배포 된 Azure IoT Edge 모듈 나열 |
|기타             |```df [option] [file]```          |지정 된 파일 시스템에서 사용 가능한/총 공간에 대 한 정보를 표시 합니다. |
|기타             |```ip route get 1.1.1.1```        |장치 IP 및 인터페이스 정보를 표시 합니다. |
|기타             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |장치 IP 주소만 표시 |


```journalctl```Wi-Fi 명령은 다음 단일 명령으로 결합 될 수 있습니다.

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker 문제 해결 명령

|명령:                        |함수:                  |
|--------------------------------|---------------------------|
|```docker ps``` |[실행 중인 컨테이너를 표시 합니다.](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[장치에 있는 이미지를 표시 합니다.](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[장치에서 이미지를 삭제 합니다.](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[지정 된 모듈의 컨테이너 로그를 가져옵니다.](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[모든 현 현 이미지를 제거 합니다.](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |docker 컨테이너 다운로드 상태 확인 |

## <a name="usb-updating"></a>USB 업데이트

|오류:                                    |해결 방법:                                               |
|------------------------------------------|--------------------------------------------------------|
|UUU를 통해 USB 플래시 중 LIBUSB_ERROR_XXX |이 오류는 UUU 업데이트 중에 USB 연결 오류가 발생 한 경우에 발생 합니다. USB 케이블이 PC 또는 10 배의 USB 포트에 제대로 연결 되어 있지 않으면이 양식의 오류가 발생 합니다. USB 케이블의 양쪽 끝을 분리 하 고 replugging 하 고 케이블을 jiggling 하 여 보안 연결을 확인 하세요. 이는 거의 항상 문제를 해결 합니다. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept 진한 캐리어 보드 LED 상태

캐리어 보드 하우징 위에는 세 개의 작은 Led가 있습니다. LED 1 옆에 클라우드 아이콘이 인쇄 되 고 LED 2 옆에 Wi-Fi 아이콘이 인쇄 되며 LED 3 옆에 느낌표 아이콘이 인쇄 됩니다. 각 LED 상태에 대 한 자세한 내용은 아래 표를 참조 하십시오.

|LED             |시스템 상태      |Description                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |켜기 (solid) |장치가 IoT Hub에 연결 되어 있습니다. |
|LED 2 (Wi-fi)   |저속 깜박임 |장치는 Wi-Fi 쉬운 연결로 구성할 준비가 되었으며 구성 기에 대 한 존재를 발표 하 고 있습니다. |
|LED 2 (Wi-fi)   |고속 깜박임 |인증에 성공 하 고 장치를 연결 하는 중입니다. |
|LED 2 (Wi-fi)   |켜기 (solid) |인증 및 연결에 성공 했습니다. 장치가 Wi-Fi 네트워크에 연결 되어 있습니다. |
|LED 3           |해당 없음         |LED를 사용 하지 않습니다. |


