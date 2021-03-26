---
title: Azure IoT Hub에 대 한 장치 업데이트에서 장치 업데이트 계정 만들기 | Microsoft Docs
description: Azure IoT Hub에 대 한 장치 업데이트에서 장치 업데이트 계정을 만듭니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558485"
---
# <a name="device-update-for-iot-hub-resource-management"></a>IoT Hub 리소스 관리용 디바이스 업데이트

장치 업데이트를 시작 하려면 장치 업데이트 계정 및 인스턴스를 만들고 액세스 제어 역할을 설정 해야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* IoT Hub에 대한 액세스 권한. S1(표준) 계층 이상을 사용하는 것이 좋습니다. 
* 지원되는 브라우저:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>디바이스 업데이트 계정 만들기

1. [Azure Portal](https://portal.azure.com) 로 이동

2. 리소스 만들기를 클릭 하 고 "IoT Hub에 대 한 장치 업데이트"를 검색 합니다.

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="IoT Hub 리소스에 대 한 장치 업데이트 스크린샷" lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. 만들기-장치 업데이트 >를 클릭 IoT Hub

4. 장치 업데이트 계정 및 리소스 그룹에 연결할 Azure 구독을 지정 합니다.

5. 장치 업데이트 계정의 이름 및 위치 지정

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="계정 세부 정보의 스크린샷" lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > [Azure 제품-지역별 페이지로](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) 이동 하 여 IoT Hub에 대 한 장치 업데이트를 사용할 수 있는 지역을 검색할 수 있습니다. IoT Hub에 대 한 장치 업데이트를 사용자의 지역에서 사용할 수 없는 경우 가장 가까운 사용 가능한 지역에서 계정을 만들도록 선택할 수 있습니다. 

6. "다음: 검토 +> 만들기"를 클릭 합니다.

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="계정 세부 정보 검토의 스크린샷" lightbox="media/create-device-update-account/account-review.png":::

7. 세부 정보를 검토 한 다음 "만들기"를 선택 합니다. 배포가 진행 되 고 있는 것을 볼 수 있습니다. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="진행 중인 계정 배포 스크린샷" lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. 몇 분 안에 배포 상태가 "완료"로 변경 되는 것을 볼 수 있습니다. "리소스로 이동"을 클릭 합니다.

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="계정 배포의 스크린샷 완료" lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>장치 업데이트 인스턴스 만들기 

장치 업데이트 인스턴스는 단일 IoT hub에 연결 됩니다. 장치 업데이트에 사용할 IoT hub를 선택 합니다. 이 단계를 수행 하는 동안 새 공유 액세스 정책을 만들어 장치 업데이트에서 IoT Hub 사용 하는 데 필요한 권한만 사용 하는지 확인 합니다 (레지스트리 쓰기 및 서비스 연결). 이 정책은 액세스 권한이 장치 업데이트로만 제한 되도록 합니다.

계정을 만든 후에 장치 업데이트 인스턴스를 만듭니다.

1. 새로 만든 계정 리소스에 있으면 인스턴스 관리 "인스턴스" 블레이드로 이동 합니다.

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="계정 내의 인스턴스 관리 스크린샷" lightbox="media/create-device-update-account/instance-blade.png":::

2. "만들기 및 인스턴스 이름 지정을 클릭 하 고 IoT Hub를 선택 합니다.

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="인스턴스 정보의 스크린샷" lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > 장치 업데이트 리소스에 연결 하는 IoT Hub 장치 업데이트 계정과 동일한 지역에 있을 필요는 없습니다. 그러나 성능을 향상 시키려면 장치 업데이트 계정의 지역에 가까운 지역에 IoT Hub 하는 것이 좋습니다. 

3. “만들기”를 클릭합니다. 인스턴스가 "생성 중" 상태로 표시 됩니다. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="인스턴스를 만드는 스크린샷" lightbox="media/create-device-update-account/instance-creating.png":::

4. 인스턴스 배포가 완료 될 때까지 5-10 분을 허용 합니다. "프로 비전 상태"가 "성공"으로 표시 될 때까지 상태를 새로 고칩니다.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="인스턴스 생성의 스크린샷 성공" lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>IoT Hub 구성 

장치 업데이트는 IoT Hub에서 변경 알림을 받도록 하기 위해 "기본 제공" 이벤트 허브와 통합 됩니다. "IoT Hub 구성" 단추를 클릭 하면 IoT 장치와 통신 하는 데 필요한 메시지 경로 및 액세스 정책이 구성 됩니다. 

IoT Hub 구성 하려면

1. "프로 비전 상태" 인스턴스가 "성공"으로 전환 되 면 인스턴스 관리 블레이드에서 인스턴스를 선택 합니다. "IoT Hub 구성"을 클릭 합니다.

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="인스턴스에 대 한 IoT Hub를 구성 하는 스크린샷" lightbox="media/create-device-update-account/instance-configure.png":::

2. "이 변경을 수행 하는 데 동의 함"을 선택 합니다.

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="인스턴스에 대 한 IoT Hub를 구성 하는 것에 동의 하는 스크린샷" lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. "업데이트"를 클릭 합니다.

[구성 된 메시지 경로에 대해 알아봅니다.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>액세스 제어 역할 구성

다른 사용자가 장치 업데이트에 액세스할 수 있도록 하려면이 리소스에 대 한 액세스 권한을 사용자에 게 부여 해야 합니다. 

1. 장치 업데이트 계정 내에서 액세스 제어 (IAM)로 이동 합니다.

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="장치 업데이트 계정 내의 액세스 제어 스크린샷" lightbox="media/create-device-update-account/account-access-control.png":::

2. "역할 할당 추가"를 클릭 합니다.

3. "역할 선택" 아래의 지정 된 옵션에서 장치 업데이트 역할을 선택 합니다.
     - 장치 업데이트 관리자
     - 장치 업데이트 판독기
     - 장치 업데이트 콘텐츠 관리자
     - 장치 업데이트 콘텐츠 판독기
     - 장치 업데이트 배포 관리자
     - 장치 업데이트 배포 판독기
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="장치 업데이트 계정 내에서 액세스 제어 역할 할당의 스크린샷" lightbox="media/create-device-update-account/role-assignment.png":::
    
    [IoT Hub에 대 한 장치 업데이트의 역할 기반 액세스 제어에 대해 알아봅니다.](device-update-control-access.md) 
    
4. 사용자 또는 Azure AD 그룹에 대 한 액세스 권한 할당
5. 저장을 클릭합니다.
6. 이제 IoT Hub 내에서 장치 업데이트 환경을 사용할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

다음 빠른 자습서 중 하나를 사용 하 여 장치를 업데이트 해 보세요.

 - [시뮬레이터에서 디바이스 업데이트](device-update-simulator.md)
 - [Raspberry Pi에서 디바이스 업데이트](device-update-raspberry-pi.md)
 - [Ubuntu Server 18.04 x64 패키지 에이전트에서 디바이스 업데이트](device-update-ubuntu-agent.md)

[장치 업데이트 계정 및 인스턴스에 대해 알아봅니다.](device-update-resources.md) 

[장치 업데이트 액세스 제어 역할에 대해 알아봅니다. ](device-update-control-access.md) 

