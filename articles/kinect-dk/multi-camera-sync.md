---
title: 여러 Azure Kinect 진한 장치 동기화
description: 이 문서에서는 다중 장치 동기화의 이점 및 동기화 할 장치를 설정 하는 방법을 살펴봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure, kinect, 사양, 하드웨어, 진한, 기능, 깊이, 색, RGB, IMU, 배열, 깊이, 다중 동기화
ms.openlocfilehash: 7c79101de5e5455ae2ff9fd8b5d8369a3832631c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361163"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>여러 Azure Kinect 진한 장치 동기화

각 Azure Kinect 진한 장치에는 여러 장치를 함께 연결 하는 데 사용할 수 있는 3.5-mm 동기화 포트 (**동기화** 및 **동기화**)가 포함 되어 있습니다. 장치를 연결한 후에는 소프트웨어에서 해당 장치 간의 트리거 시간을 조정할 수 있습니다. 

이 문서에서는 장치를 연결 하 고 동기화 하는 방법을 설명 합니다.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>여러 Azure Kinect 진한 장치 사용의 이점

다음을 포함 하 여 여러 Azure Kinect 진한 장치를 사용 하는 여러 가지 이유가 있습니다.

- Occlusions를 입력 합니다. Azure Kinect 진한 데이터 변환은 단일 이미지를 생성 하지만 두 카메라 (깊이 및 RGB)는 실제로는 작은 거리입니다. 이 오프셋을 사용 하면 occlusions 가능 합니다. 폐색 전경 개체가 장치의 두 카메라 중 하나에 대 한 배경 개체의 일부 보기를 차단할 때 발생 합니다. 결과 색 이미지에서 전경 개체는 배경 개체의 그림자를 캐스트 하는 것 처럼 보입니다.  
   예를 들어 다음 다이어그램에서 왼쪽 카메라는 회색 픽셀 "P2"를 표시 합니다. 그러나 흰색 전경 개체는 오른쪽 카메라 IR 보를 차단 합니다. 오른쪽 카메라에는 "P2"에 대 한 데이터가 없습니다.  
   ![다이어그램에는 두 카메라 중 하나가 차단 된 동일한 지점에서 전송 되는 두 카메라를 표시 합니다.](./media/occlusion.png)  
   추가 동기화 된 장치는 폐색 데이터를 제공할 수 있습니다.
- 3 차원에서 개체를 검색 합니다.
- 유효 프레임 속도를 FPS (초당 프레임 수) 보다 큰 값으로 늘립니다.
- 동일한 장면의 여러 4K 색 이미지를 캡처합니다. 모든 4K 색 이미지는 노출 중심의 100 마이크로초 내에 정렬 &mu; 됩니다.
- 공간 내에서 카메라 커버리지를 늘립니다.

## <a name="plan-your-multi-device-configuration"></a>다중 장치 구성 계획

시작 하기 전에 [Azure KINECT 진한 하드웨어 사양](hardware-specification.md) 및 [azure Kinect 진한 깊이 카메라](depth-camera.md)를 확인 해야 합니다.

### <a name="select-a-device-configuration"></a>장치 구성 선택

장치 구성에 대해 다음 방법 중 하나를 사용할 수 있습니다.

- **데이지 체인 구성**. 하나의 마스터 장치와 최대 8 개의 하위 장치를 동기화 합니다.  
   ![데이지 체인 구성으로 Azure Kinect 진한 장치를 연결 하는 방법을 보여 주는 다이어그램입니다.](./media/multicam-sync-daisychain.png)
- **별모양 구성**. 하나의 마스터 장치와 최대 2 개의 하위 장치를 동기화 합니다.  
   ![별모양 구성에서 여러 Azure 진한 장치를 설정 하는 방법을 보여 주는 다이어그램입니다.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>외부 동기화 트리거 사용

두 구성에서 마스터 장치는 하위 장치에 대 한 트리거 신호를 제공 합니다. 그러나 동기화 트리거에 대해 사용자 지정 외부 원본을 사용할 수 있습니다. 예를 들어이 옵션을 사용 하 여 이미지 캡처를 다른 장비와 동기화 할 수 있습니다. 데이지 체인 구성 또는 별모양 구성에서 외부 트리거 원본은 마스터 장치에 연결 됩니다.

외부 트리거 원본은 마스터 장치와 동일한 방식으로 작동 해야 합니다. 다음과 같은 특징이 있는 동기화 신호를 제공 해야 합니다.

- 활성 높음
- 펄스 너비: 8 초 초과 &mu;
- 5V TTL/CMOS
- 최대 구동 용량: milliamps (mA) 미만
- 빈도 지원: 정확히 30 FPS, 15FPS 및 5FPS (color camera master VSYNC 신호의 주파수)

트리거 원본은 3.5-mm 오디오 케이블을 사용 하 여 포트에서 마스터 장치 **동기화** 로 신호를 배달 해야 합니다. 스테레오 또는 mono 케이블을 사용할 수 있습니다. Azure Kinect는 오디오 케이블 커넥터의 모든 되셨나요?와 링을 함께 연결 하 고 grounds 합니다. 다음 다이어그램에 표시 된 것 처럼 장치는 커넥터 팁 에서만 동기화 신호를 받습니다.

![외부 트리거 신호에 대 한 케이블 구성](./media/resources/camera-trigger-signal.jpg)

외부 장비를 사용 하는 방법에 대 한 자세한 내용은 [외부 동기화 장치에 Azure Kinect 레코더 사용](record-external-synchronized-units.md) 을 참조 하세요.

### <a name="plan-your-camera-settings-and-software-configuration"></a>카메라 설정 및 소프트웨어 구성 계획

카메라를 제어 하 고 이미지 데이터를 사용 하도록 소프트웨어를 설정 하는 방법에 대 한 자세한 내용은 [Azure Kinect 센서 SDK](about-sensor-sdk.md)를 참조 하세요.

이 섹션에서는 동기화 된 장치에 영향을 주는 몇 가지 요소에 대해 설명 합니다 (단일 장치는 아님). 소프트웨어에서 이러한 요인을 고려해 야 합니다.

#### <a name="exposure-considerations"></a>노출 고려 사항
각 장치의 정확한 타이밍을 제어 하려면 수동 노출 설정을 사용 하는 것이 좋습니다. 자동 노출 설정에서 각 컬러 카메라는 실제 노출을 동적으로 변경할 수 있습니다. 노출이 타이밍에 영향을 주므로 이러한 변경 내용은 카메라를 동기화 되지 않은 것으로 빠르게 푸시합니다.

이미지 캡처 루프에서 동일한 노출 설정을 반복적으로 설정 하지 않도록 합니다. 필요한 경우 API를 한 번만 호출 합니다.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>여러 깊이 카메라 간의 간섭 방지

여러 깊이 카메라의 이미지가 이미지와 겹치는 경우 각 카메라는 자체 연결 된 레이저 이미지를 표시 해야 합니다. 레이저 좋아하세요가 서로 방해 하지 않도록 하려면 카메라 캡처는 160 μs 이상으로 서로의 오프셋 이어야 합니다.

각 깊이 카메라 캡처에 대해 레이저는 9 번 켜고 매번 125 s에 대해 활성 상태입니다 &mu; . 그런 다음 &mu; &mu; 작업 모드에 따라 레이저가 14505 s 또는 23905 s에서 유휴 상태가 됩니다. 이 동작은 오프셋 계산의 시작 지점이 125 s 임을 의미 합니다 &mu; .

또한 카메라 클록과 장치 펌웨어 클록의 차이가 160 s로 증가 합니다 &mu; . 구성에 대 한 보다 정확한 오프셋을 계산 하려면 사용 하는 깊이 모드를 확인 하 고 [깊이 센서 원시 타이밍 표](hardware-specification.md#depth-sensor-raw-timing)를 참조 하세요. 이 테이블의 데이터를 사용 하 여 다음 수식을 사용 하 여 최소 오프셋 (각 카메라의 노출 시간)을 계산할 수 있습니다.

> *노출 시간* = (*IR 펄스* &times; *너비*) + (*유휴 기간* &times; *유휴 시간*)

160의 오프셋을 사용 하는 경우 &mu; 다른 레이저 좋아하세요 유휴 상태에 있는 동안 각 레이저가 켜지 되도록 최대 9 개의 추가 깊이 카메라를 구성할 수 있습니다.

소프트웨어에서 또는를 사용 ```depth_delay_off_color_usec``` ```subordinate_delay_off_master_usec``` 하 여 각 IR 레이저가 고유한 160 &mu; s 창에서 발생 하거나 다른 보기 필드를 포함 하는지 확인 합니다.

## <a name="prepare-your-devices-and-other-hardware"></a>장치 및 기타 하드웨어 준비

여러 Azure Kinect 진한 장치 외에도 빌드 하려는 구성을 지원 하기 위해 추가 호스트 컴퓨터 및 기타 하드웨어를 가져와야 할 수 있습니다. 설치를 시작 하기 전에이 섹션의 정보를 사용 하 여 모든 장치와 하드웨어가 준비 되었는지 확인 합니다.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect 진한 장치

동기화 하려는 각 Azure Kinect 진한 장치에 대해 다음을 수행 합니다.

- 장치에 최신 펌웨어가 설치 되어 있는지 확인 합니다. 장치를 업데이트 하는 방법에 대 한 자세한 내용은 [Azure KINECT 진한 펌웨어 업데이트](update-device-firmware.md)를 참조 하세요. 
- 장치 커버를 제거 하 여 동기화 포트를 표시 합니다.
- 각 장치의 일련 번호를 확인 합니다. 이 번호는 나중에 설치 프로세스에서 사용 합니다.

### <a name="host-computers"></a>호스트 컴퓨터

일반적으로 각 Azure Kinect 진한 호스트 컴퓨터를 사용 합니다. 장치를 사용 하는 방법 및 USB 연결을 통해 전송 되는 데이터의 양에 따라 전용 호스트 컨트롤러를 사용할 수 있습니다. 

각 호스트 컴퓨터에 Azure Kinect 센서 SDK가 설치 되어 있는지 확인 합니다. 센서 SDK를 설치 하는 방법에 대 한 자세한 내용은 [빠른 시작: Azure Kinect에 설정을 참조](set-up-azure-kinect-dk.md)하세요. 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux 컴퓨터: Ubuntu의 USB 메모리

기본적으로 Linux 기반 호스트 컴퓨터는 usb 전송을 처리 하기 위해 USB 컨트롤러를 16mb의 커널 메모리로만 할당 합니다. 이 금액은 일반적으로 단일 Azure Kinect을 지원 하기에 충분 합니다. 그러나 여러 장치를 지원 하려면 USB 컨트롤러에 더 많은 메모리가 있어야 합니다. 메모리를 늘리려면 다음 단계를 수행 합니다.

1. 편집/**기타/기본/grub**.
1. 다음 줄을 찾습니다.
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   다음 줄을 사용 하 여 바꿉니다.
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > 이 명령은 USB 메모리를 32 MB로 설정 합니다. 기본값은 두 번 설정 된 예제입니다. 솔루션에 맞게 훨씬 더 큰 값을 설정할 수 있습니다.
1. **Sudo 업데이트-grub**를 실행 합니다.
1. 컴퓨터를 다시 시작합니다.

### <a name="cables"></a>케이블

장치를 서로 연결 하 고 호스트 컴퓨터에 연결 하려면 3.5-mm의 숫 케이블 (3.5-mm 오디오 케이블이 라고도 함)을 사용 해야 합니다. 케이블의 길이는 10 미터 미만 이어야 하며 스테레오 또는 mono 일 수 있습니다.

필요한 케이블 수는 사용 하는 장치 수와 특정 장치 구성에 따라 달라 집니다. Azure Kinect 진한 상자는 케이블을 포함 하지 않습니다. 별도로 구입 해야 합니다.

스타 구성에서 장치를 연결 하는 경우에는 하나의 헤드폰 분할자도 있어야 합니다.

## <a name="connect-your-devices"></a>사용자 디바이스 연결

**데이지 체인 구성에서 Azure Kinect 진한 장치를 연결 하려면**

1. 각 Azure Kinect 진한을 power에 연결 합니다.
1. 각 장치를 해당 호스트 PC에 연결 합니다. 
1. 마스터 장치로 사용할 장치를 하나 선택 하 고 3.5-mm 오디오 케이블을 **동기화 아웃** 포트에 연결 합니다.
1. 첫 번째 종속 장치의 포트에서 다른 케이블 끝을 **동기화** 합니다.
1. 다른 장치를 연결 하려면 다른 케이블을 첫 번째 하위 장치의 **동기화 아웃** 포트에 연결 하 고 다음 장치의 **동기화에서 동기화** 합니다.
1. 모든 장치가 연결 될 때까지 이전 단계를 반복 합니다. 마지막 장치에는 케이블 연결이 하나만 있어야 합니다. 해당 **동기화** 포트는 비어 있어야 합니다.

**별모양 구성에서 Azure Kinect 진한 장치를 연결 하려면**

1. 각 Azure Kinect 진한을 power에 연결 합니다.
1. 각 장치를 해당 호스트 PC에 연결 합니다. 
1. 하나의 장치를 마스터 장치로 선택 하 고, 헤드폰 분할자의 한쪽 끝을 **동기화 아웃** 포트에 꽂습니다.
1. 3.5-mm 오디오 케이블을 헤드폰 분할자의 "분할" 끝에 연결 합니다.
1. 각 케이블의 다른 끝을 종속 장치 중 하나의 포트 **에서 동기화** 로 연결 합니다.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>장치가 연결 되어 있고 통신 하는지 확인

장치가 올바르게 연결 되었는지 확인 하려면 [Azure Kinect Viewer](azure-kinect-viewer.md)를 사용 합니다. 필요에 따라이 절차를 반복 하 여 마스터 장치와 함께 각 하위 장치를 테스트 합니다.

> [!IMPORTANT]  
> 이 절차에서는 각 Azure Kinect의 일련 번호를 알고 있어야 합니다.

1. Azure Kinect Viewer의 두 인스턴스를 엽니다.
1. **장치 열기**에서 테스트 하려는 하위 장치의 일련 번호를 선택 합니다.  
   ![장치 열기](./media/open-devices.png)
   > [!IMPORTANT]  
   > 모든 장치 간에 정확한 이미지 캡처 맞춤을 얻으려면 마스터 장치를 마지막으로 시작 해야 합니다.  
1. **외부 동기화**아래에서 **Sub**를 선택 합니다.  
   ![하위 카메라 시작](./media/sub-device-start.png)
1.  **시작**을 선택합니다.  
    > [!NOTE]  
    > 이는 하위 장치 이므로 Azure Kinect Viewer는 장치가 시작 된 후 이미지를 표시 하지 않습니다. 하위 장치가 마스터 장치에서 동기화 신호를 받을 때까지 이미지가 표시 되지 않습니다.
1. 하위 장치가 시작 된 후 Azure Kinect Viewer의 다른 인스턴스를 사용 하 여 마스터 장치를 엽니다.
1. **외부 동기화**아래에서 **마스터**를 선택 합니다.
1. **시작**을 선택합니다.

마스터 Azure Kinect 장치가 시작 되 면 Azure Kinect Viewer의 두 인스턴스가 모두 이미지를 표시 해야 합니다.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>장치를 동기화 된 집합으로 보정

장치가 올바르게 통신 하 고 있는지 확인 한 후에는 단일 도메인에서 이미지를 생성 하도록 보정할 준비가 된 것입니다.

단일 장치에서 깊이와 RGB 카메라는 함께 작동 하도록 공장에서 보정 됩니다. 그러나 여러 장치가 함께 작동 해야 하는 경우 이미지를 처리 하는 데 사용 하려는 카메라의 도메인으로 이미지를 캡처한 카메라의 도메인에서 이미지를 변환 하는 방법을 결정 하기 위해 이러한 장치를 보정 해야 합니다.

장치를 교차 보정 하기 위한 여러 옵션이 있습니다. Microsoft에서는 OpenCV 메서드를 사용 하는 [GitHub 녹색 화면 코드 샘플](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)을 제공 합니다. 이 코드의 추가 정보 파일 샘플에서는 장치를 보정 하는 방법에 대 한 자세한 내용 및 지침을 제공 합니다.

보정에 대 한 자세한 내용은 [Azure Kinect 보정 함수 사용](use-calibration-functions.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

동기화 된 장치를 설정한 후에는 다음을 사용 하는 방법을 배울 수도 있습니다.
> [!div class="nextstepaction"]
> [Azure Kinect 센서 SDK 레코드 및 재생 API](record-playback-api.md)

## <a name="related-topics"></a>관련 항목

- [Azure Kinect 센서 SDK 정보](about-sensor-sdk.md)
- [Azure Kinect 진한 하드웨어 사양](hardware-specification.md) 
- [빠른 시작: Azure Kinect 진한 사용자 설정](set-up-azure-kinect-dk.md) 
- [Azure Kinect 진한 펌웨어 업데이트](update-device-firmware.md) 
- [Azure Kinect DK 다시 설정](reset-azure-kinect-dk.md) 
- [Azure Kinect 뷰어](azure-kinect-viewer.md) 
