---
title: 무선 업데이트를 통해 배포할 Azure IoT Hub 설정
description: 무선을 통해 Azure Percept에 업데이트를 배포 하도록 Azure IoT Hub를 구성 하는 방법에 대해 알아봅니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662915"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>무선 업데이트를 통해 Azure Percept에 배포 하도록 Azure IoT Hub를 설정 하는 방법
대공 업데이트를 사용 하 여 Azure Percept를 안전 하 게 유지 하 고 최신 상태로 유지 하세요. 몇 가지 간단한 단계를 통해 IoT Hub에 대 한 장치 업데이트를 사용 하 여 Azure 환경을 설정 하 고 최신 업데이트를 Azure Percept에 배포할 수 있습니다.

## <a name="create-a-device-update-account"></a>장치 업데이트 계정 만들기

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 azure Percept에서 사용 하는 azure 계정으로 로그인 합니다. 

1. 페이지 맨 위에 있는 검색 창에서 "IoT Hub에 대 한 장치 업데이트" 입력을 시작 합니다.

1. 검색 창에 표시 되는 **IoT hub에 대 한 장치 업데이트** 를 선택 합니다.

1. 페이지의 왼쪽 위 부분에 있는 **+ 추가** 단추를 클릭 합니다.

1. Azure Percept 장치에 연결 된 Azure 구독 및 리소스 그룹을 선택 합니다 (온 보 딩에서 IoT Hub 위치).

1. 장치 업데이트 계정의 이름 및 위치 지정

1. 세부 정보를 검토 한 다음 **검토 + 만들기** 를 선택 합니다.
 
1. 배포가 완료 되 면 **리소스로 이동** 을 클릭 합니다.
 
## <a name="create-a-device-update-instance"></a>장치 업데이트 인스턴스 만들기
이제 IoT Hub 계정의 장치 업데이트 내에서 인스턴스를 만듭니다.

1. IoT Hub 리소스에 대 한 장치 업데이트에서 **인스턴스 관리** 아래에 있는 **인스턴스** 를 클릭 합니다.
 
1. **+ 만들기** 를 클릭 하 고, 인스턴스 이름을 지정 하 고, Azure Percept 장치와 연결 된 IoT Hub (즉, 온 보 딩 환경에서 만들어짐)를 선택 합니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.
 
1. **만들기** 를 클릭합니다.

## <a name="configure-iot-hub"></a>IoT Hub 구성

1. 인스턴스 **관리 인스턴스** 페이지에서 장치 업데이트 인스턴스가 **성공** 상태로 전환 될 때까지 기다립니다. **삭제** 옆의 **새로 고침** 아이콘을 클릭 하 여 상태를 업데이트 합니다.
 
1. 만든 인스턴스를 선택 하 고 **IoT Hub 구성** 을 클릭 합니다. 왼쪽 창에서 **동의 함을 선택 하 여 이러한 변경을 수행 하** 고 **업데이트** 를 클릭 합니다.
 
1. 프로세스가 성공적으로 완료 될 때까지 기다립니다.
 
## <a name="configure-access-control-roles"></a>액세스 제어 역할 구성
최종 단계를 사용 하면 사용자에 게 업데이트를 게시 하 고 배포할 수 있는 권한을 부여할 수 있습니다.

1. IoT Hub 리소스에 대 한 장치 업데이트에서 **Access control (IAM)** 을 클릭 합니다.
 
2. **+ 추가** 를 클릭 하 고 **역할 할당 추가** 를 선택 합니다.
 
3. **역할** 에 대해 **장치 업데이트 관리자** 를 선택 합니다. **액세스 권한을 할당 하 여** **사용자, 그룹 또는 서비스 주체** 를 선택 합니다. **선택** 에 대해 업데이트를 배포할 사용자의 계정이 나 계정을 선택 합니다. 그런 다음 **저장** 을 클릭합니다. 

    > [!TIP]
    > 조직에서 더 많은 사람에 게 액세스 권한을 부여 하려는 경우이 단계를 반복 하 고 각 사용자에 게 **장치 업데이트 관리자** 를 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제를 설정 하 고 IoT Hub에 대 한 장치 업데이트를 사용 하 여 [Azure Percept dev kit를 무선으로 업데이트할](./how-to-update-over-the-air.md) 수 있습니다. Azure Percept 장치에 사용 하는 Azure IoT Hub으로 이동 합니다.