---
title: Azure Percept의 알려진 문제
description: Azure Percept의 알려진 문제 및 해결 방법에 대한 자세한 정보
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605062"
---
# <a name="known-issues"></a>알려진 문제

이 문제가 발생하는 경우 버그를 열 필요가 없습니다. 해결 방법 중 하나를 사용하는 데 문제가 있는 경우 문제를 개설하세요.

|Area|문제 설명|해결 방법|
|-------|---------|---------|
| 온보딩 환경 | 디바이스의 Wi-Fi가 구성되어 있지 않으면 온보딩 환경을 완료할 수 없습니다(Azure 로그인이 실패함). | 1. [Azure Percept DK에 SSH를 실행합니다](./how-to-ssh-into-percept-dk.md). <br> 2. 디바이스의 이더넷 IP 주소를 식별하고 복사합니다. <br> 3. 이더넷 IP 기반 URL을 사용하여 온보딩 환경에 연결합니다. |
| 온보딩 환경 | EULA(사용권 계약)에서 링크를 클릭하면 새 웹 페이지가 열리지 않을 수도 있습니다. | 링크를 복사하여 별도의 브라우저 창에서 엽니다. |
| 온보딩 환경 | 모바일 Wi-Fi 핫스팟에 연결된 경우 온보딩 환경을 사용할 수 없습니다. | SoftAP, Wi-Fi 네트워크 또는 이더넷을 통해 네트워크에 직접 디바이스를 연결합니다. |
| Wi-Fi | SoftAP는 경우에 따라 연결이 끊기거나 사라질 수 있습니다. | 조사하고 있습니다.  디바이스를 다시 부팅하면 일반적으로 다시 돌아옵니다. |
| Wi-Fi | Wi-Fi SoftAP를 설정 및 해제하는 하드웨어 단추가 작동하지 않는 경우가 있습니다. | 계속해서 단추를 누르거나 디바이스를 다시 부팅 합니다. |
| Wi-Fi | Wi-Fi에 연결하면 사용자에게 메시지가 표시될 수 있습니다. <br> "이 Wi-Fi 네트워크는 이전 보안 표준을 사용합니다." | Devkit의 SoftAP는 WEP 암호화 알고리즘을 사용합니다. |
| Wi-Fi | 다음 오류 메시지를 사용하여 Windows 10 PC에서 SoftAP에 연결할 수 없습니다. <br> "이 네트워크에 연결할 수 없습니다." | Devkit와 컴퓨터를 모두 다시 부팅합니다. |
| 장치 업데이트 | 컨테이너가 OTA 업데이트 후에 실행되지 않습니다. | 디바이스에 SSH를 수행하고 다음 명령을 사용하여 IoT Edge 컨테이너를 다시 시작합니다. `systemctl restart iotedge` 그러면 모든 컨테이너가 다시 시작됩니다. |
| 장치 업데이트 | 성공한 경우에도 사용자에게 업데이트가 실패했다는 메시지가 표시될 수 있습니다. | IoT Hub에서 devkit의 디바이스 쌍으로 이동하고 `swVersion`의 값을 확인하여 업데이트를 확인합니다. 이는 첫 번째 업데이트 후에 수정됩니다. |
| 장치 업데이트 | 첫 번째 업데이트 후 Wi-Fi 연결 설정이 손실될 수 있습니다. | Wi-Fi 연결을 설정하기 위해 업데이트 후 온보딩 환경을 실행합니다. 이는 첫 번째 업데이트 후에 수정됩니다. |
| 장치 업데이트 | OTA 업데이트를 수행한 후에는 이전에 만든 사용자 계정을 사용하여 SSH를 통해 로그온할 수 없으며 새 SSH 사용자를 온보딩 환경에서 만들 수 없습니다. 이 문제는 미리 설치된 다음 이미지 버전에서 OTA 업데이트를 수행하는 시스템에 영향을 줍니다. 2020.110.114.105 및 2020.109.101.105. | 사용자 프로필을 복구하려면 OTA 업데이트 후 다음 단계를 수행하세요. <br> 사용자 이름으로 "root"를 사용하여 [devkit에 SSH](./how-to-ssh-into-percept-dk.md)를 수행합니다. 온보딩 환경을 통해 SSH "루트" 사용자 로그인을 사용하지 않도록 설정한 경우, 다시 사용하도록 설정해야 합니다. 성공적으로 연결한 후 다음 명령을 실행합니다. <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> 이전 사용자 홈 데이터를 복구하려면 다음 명령을 실행합니다. <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| 장치 업데이트 | OTA 업데이트를 수행하면 업데이트 그룹이 손실됩니다. | 다음 [지침](./how-to-update-over-the-air.md#create-a-device-update-group)에 따라 디바이스의 태그를 업데이트합니다. |
| Dev Tools Pack 설치 관리자 | Docker가 시스템에서 제대로 실행되지 않는 경우 선택적 Caffe 설치가 실패할 수 있습니다. | Docker가 설치되어 실행 중인지 확인한 다음 Caffe 설치를 다시 시도합니다. |
| Dev Tools Pack 설치 관리자 | 호환되지 않는 시스템에서는 선택적 CUDA 설치에 실패합니다. | 설치 프로그램을 실행하기 전에 이 시스템 호환성을 확인합니다. |
| Docker, 네트워크, IoT Edge | 내부 네트워크에서 172.x.x.x를 사용하는 경우, Docker 컨테이너는 IoT Edge 연결에 실패합니다. | 다음과 같이/etc/docker/daemon.json 파일에 특수 bip 섹션을 추가합니다. `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Azure Percept Studio 내의 "스트림 보기" 링크는 디바이스의 웹 스트림을 표시하는 새 창을 열지 않습니다. | 1. [Azure Portal](https://portal.azure.com)을 열고 **IoT Hub** 를 선택합니다. <br> 2. 디바이스가 연결된 IoT Hub를 클릭합니다. <br> 3. IoT Hub 페이지에서 **자동 디바이스 관리** 의 **IoT Edge** 를 선택합니다. <br> 4. 목록에서 디바이스를 선택합니다. <br> 5. 디바이스 페이지의 맨 위에 있는 **모듈 설정** 을 선택합니다. <br> 6. **HostIpModule** 옆의 휴지통 아이콘을 클릭하여 모듈을 삭제합니다. <br> 7. 작업을 확인하려면 **검토 + 만들기** 를 클릭한 다음 **만들기** 를 클릭합니다. <br> 8. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)를 열고 왼쪽 메뉴 패널에서 **디바이스** 를 클릭합니다. <br> 9. 목록에서 디바이스를 선택합니다. <br> 10. **비전** 탭에서 **디바이스 스트림 보기** 를 클릭합니다. 디바이스에서 새 버전의 HostIpModule을 다운로드하고 디바이스의 웹 스트림을 사용하여 브라우저 탭을 엽니다. |