---
title: Azure Percept의 알려진 문제
description: Azure Percept의 알려진 문제 및 해결 방법에 대 한 자세한 정보
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a3f44f3d0cdf024bca12b0023891f21175f52b47
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026941"
---
# <a name="known-issues"></a>알려진 문제

이러한 문제가 발생 하는 경우 버그를 열 필요가 없습니다. 해결 방법 중 하나를 사용 하는 데 문제가 있는 경우 문제를 여세요.

|Area|문제 설명|해결 방법|
|-------|---------|---------|
| 온 보 딩 환경 | 장치의 Wi-Fi 구성 되어 있지 않으면 (Azure 로그인이 실패 함) 온 보 딩 환경을 완료할 수 없습니다. | 1. 장치 액세스 포인트 (10.1.1.1)로 SSH <br> 2. 장치 이더넷 IP 주소를 식별 하 고 복사 합니다. <br> 3. 복사 된 이더넷 IP 기반 URL을 사용 하 여 온 보 딩 환경에 연결 |
| 온 보 딩 환경 | 온 보 딩 환경에서 EULA의 링크를 클릭 하면 때때로 새 웹 페이지가 열리지 않습니다. | 링크를 복사 하 여 별도의 브라우저 창에서 엽니다. |
| 온 보 딩 환경 | 모바일 Wi-Fi 핫스팟에 연결 된 경우에는 온 보 딩 환경을 사용할 수 없습니다. | SoftAP, Wi-Fi 네트워크 또는 이더넷을 통해 네트워크에 직접 장치를 연결 합니다. |
| Wi-fi/SoftAP | SoftAP는 경우에 따라 연결이 끊거나 사라질 수 있습니다. | 조사 하 고 있습니다.  장치를 다시 부팅 하면 일반적으로 다시 부팅 됩니다. |
| Wi-Fi | Wi-Fi SoftAP를 설정 및 해제 하는 하드웨어 단추가 작동 하지 않는 경우가 있습니다. | 계속 해 서 단추를 누르거나 장치를 다시 부팅 합니다. |
| Wi-Fi | 사용자는 "이 Wi-Fi 네트워크에서 이전 보안 표준을 사용 합니다." 라고 Wi-Fi에 연결한 후에 메시지가 표시 될 수 있습니다. | Devkit의 핫스팟/SoftAP는 WEP 암호화 알고리즘을 사용 합니다. |
| Wi-Fi | 다음 오류 메시지를 사용 하 여 Windows 10 PC에서 SoftAP에 연결할 수 없습니다. <br> "이 네트워크에 연결할 수 없습니다." | Devkit와 컴퓨터를 모두 다시 부팅 합니다. |
| 장치 업데이트 | 컨테이너는 OTA 업데이트 후에 실행 되지 않습니다. | 장치에 SSH를 사용 하 고이 명령을 사용 하 여 IoT Edge 컨테이너를 다시 시작 합니다 `systemctl restart iotedge` . 그러면 모든 컨테이너가 다시 시작 됩니다. |
| 장치 업데이트 | 성공한 경우에도 사용자에 게 업데이트가 실패 했다는 메시지가 표시 될 수 있습니다. | IoT Hub에서 장치에 대 한 장치 쌍으로 이동 하 여 장치를 업데이트 했는지 확인 합니다. 이는 첫 번째 업데이트 후에 수정 됩니다. |
| 장치 업데이트 | 사용자는 첫 번째 업데이트 후 Wi-Fi 연결 설정이 손실 될 수 있습니다. | 업데이트 후 Wi-Fi 연결을 설정 하 여 온 보 딩 환경을 실행 합니다. 이는 첫 번째 업데이트 후에 수정 됩니다. |
| 장치 업데이트 | OTA 업데이트를 수행한 후에는 사용자가 이전에 만든 사용자 계정을 사용 하 여 SSH를 통해 로그온 할 수 없으며 새 SSH 사용자를 온 보 딩 환경에서 만들 수 없습니다. 이 문제는 미리 설치 된 다음 이미지 버전에서 OTA 업데이트를 수행 하는 시스템에 영향을 줍니다. 2020.110.114.105 및 2020.109.101.105. | 사용자 프로필을 복구 하려면 OTA 업데이트 후 다음 단계를 수행 합니다. <br> 사용자 이름으로 "root"를 사용 하 여 [devkit에 SSH를](./how-to-ssh-into-percept-dk.md) 사용 합니다. 온 보 딩 환경을 통해 SSH "루트" 사용자 로그인을 사용 하지 않도록 설정한 경우 다시 사용 하도록 설정 해야 합니다. 성공적으로 연결한 후 다음 명령을 실행 합니다. <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> 이전 사용자 홈 데이터를 복구 하려면 다음 명령을 실행 합니다. <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| 장치 업데이트 | OTA 업데이트를 수행 하면 업데이트 그룹이 손실 됩니다. | 다음 [지침](./how-to-update-over-the-air.md#create-a-device-update-group)에 따라 장치의 태그를 업데이트 합니다. |
| Dev Tools Pack 설치 관리자 | Docker가 시스템에서 제대로 실행 되지 않는 경우 선택적 Caffe 설치가 실패할 수 있습니다. | Docker가 설치 되어 실행 중인지 확인 한 다음 Caffe 설치를 다시 시도 합니다. |
| Dev Tools Pack 설치 관리자 | 호환 되지 않는 시스템에서 선택적으로 설치 되지 않습니다. | 설치 관리자를 실행 하기 전에이 시스템 호환성을 확인 합니다. |
| Docker, 네트워크, IoT Edge | 내부 네트워크에서 172.x.x.x를 사용 하는 경우 docker 컨테이너가 edge에 연결 되지 않습니다. | 다음과 같이/etc/docker/daemon.js파일에 특수 bip 섹션을 추가 합니다. `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Azure Percept Studio 내의 "스트림 보기" 링크는 장치의 웹 스트림을 표시 하는 새 창을 열지 않습니다. | 1. [Azure Portal](https://portal.azure.com) 를 열고 **IoT Hub** 를 선택 합니다. <br> 2. 장치가 연결 된 IoT Hub를 클릭 합니다. <br> 3. IoT Hub 페이지에서 **장치 자동 관리** 의 **IoT Edge** 을 선택 합니다. <br> 4. 목록에서 장치를 선택 합니다. <br> 5. 장치 페이지의 맨 위에 있는 **모듈 설정** 을 선택 합니다. <br> 6. **hostipmodule** 옆의 휴지통 아이콘을 클릭 하 여 모듈을 삭제 합니다. <br> 7. 작업을 확인 하려면 **검토 + 만들기** 를 클릭 한 다음 **만들기** 를 클릭 합니다. <br> 8. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 를 열고 왼쪽 메뉴 패널에서 **장치** 를 클릭 합니다. <br> 9. 목록에서 장치를 선택 합니다. <br> 10. **비전** 탭에서 **장치 스트림 보기** 를 클릭 합니다. 장치에서 새 버전의 HostIpModule을 다운로드 하 고 장치의 웹 스트림을 사용 하 여 브라우저 탭을 엽니다. |