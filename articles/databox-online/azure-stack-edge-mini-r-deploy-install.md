---
title: Azure Stack Edge Mini R 물리적 디바이스를 설치하는 방법에 대한 자습서 | Microsoft Docs
description: Azure Stack Edge Mini R 디바이스 설치에 대한 두 번째 자습서에서는 전원 및 네트워크에 대한 물리적 디바이스의 케이블을 연결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96464975"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>자습서: Azure Stack Edge Mini R 설치

이 자습서에서는 Azure Stack Edge Mini R 물리적 디바이스를 설치하는 방법에 대해 설명합니다. 설치 절차에는 디바이스에 케이블 연결이 포함됩니다.

설치를 완료하는 데 30분 정도가 걸릴 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 디바이스 검사
> * 디바이스에 케이블 연결

## <a name="prerequisites"></a>사전 요구 사항

물리적 디바이스를 설치하기 위한 필수 조건은 다음과 같습니다.

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge 리소스

시작하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Mini R 배포 준비](azure-stack-edge-mini-r-deploy-prep.md)의 모든 단계를 완료했습니다.
    * 디바이스를 배포할 Azure Stack Edge 리소스를 만들었습니다.
    * Azure Stack Edge 리소스를 통해 디바이스를 활성화하기 위한 활성화 키를 생성했습니다.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Azure Stack Edge Mini R 물리적 디바이스의 경우

디바이스를 배포하기 전에 다음 사항을 확인합니다.

- 디바이스를 평평하고 안정적이고 흔들림 없는 작업대에 안전하게 놓습니다.
- 장치를 설치하려는 사이트에 다음 장치가 있는지 확인합니다.
    - 독립적인 전원의 표준 AC 전원 장치 또는
    - 랙 PDU(전력 배포 장치) 
    

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우

시작하기 전에

- Azure Stack Edge Mini R을 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성합니다. 자세한 내용은 [Azure Stack Edge 네트워킹 요구 사항](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements)을 참조하세요.

- 디바이스가 최적으로 작동할 수 있도록 최소 인터넷 대역폭이 20Mbps인지 확인합니다. <!-- engg TBC -->


## <a name="inspect-the-device"></a>디바이스 검사

이 디바이스는 단일 장치로 제공됩니다. 디바이스의 포장을 풀려면 다음 단계를 수행합니다.

1. 평평한 지면에 상자를 놓습니다.
2. 디바이스 케이스에 손상이 있는지 검사합니다. 케이스를 열고 디바이스를 검사합니다. 케이스 또는 디바이스가 손상된 것으로 보이는 경우 디바이스가 올바르게 작동하는지 여부를 평가하는 데 도움을 얻으려면 Microsoft 지원에 문의하세요.
3. 케이스를 연 후 다음이 있는지 확인합니다.
    - 사이드 범퍼가 연결된 휴대용 Azure Stack Edge Mini R 디바이스 1개
    - 디바이스에 연결된 배터리 1개 및 후방 덮개 
    - 전원에 배터리를 연결하는 전원 코드 1개 

여기에 나열된 항목 중에 받지 못한 것이 있으면 Azure Stack Edge 지원에 문의하세요. 다음 단계는 디바이스에 케이블을 연결하는 것입니다.


## <a name="cable-the-device"></a>디바이스에 케이블 연결

다음 절차에서는 전원 및 네트워크를 위해 Azure Stack Edge 디바이스에 케이블을 연결하는 방법에 대해 설명합니다.

디바이스 케이블 연결을 시작하기 전에 다음이 필요합니다.

- 설치 사이트의 Azure Stack Edge Mini R 물리적 디바이스
- 전원 케이블 1개
- 관리 인터페이스에 연결하는 하나 이상의 1-GbE RJ-45 네트워크 케이블 디바이스에는 두 개의 1-GbE 네트워크 인터페이스가 있습니다. 하나는 관리용이고 하나는 데이터용입니다.
- 구성할 각 데이터 네트워크 인터페이스에 대한 10-GbE SFP+ 동 케이블 1개 하나 이상의 데이터 네트워크 인터페이스 - PORT 3 또는 PORT 4를 인터넷에 연결해야 합니다(Azure에 연결).  
- 전력 분배 장치 1개에 대한 액세스(권장)

> [!NOTE]
> - 단 하나의 데이터 네트워크 인터페이스를 연결하는 경우 PORT 3 또는 PORT 4와 같은 10-GbE 네트워크 인터페이스를 사용하여 데이터를 Azure에 보내는 것이 좋습니다. 
> - 최상의 성능을 얻고 많은 양의 데이터를 처리하려면 모든 데이터 포트를 연결하는 것이 좋습니다.
> - Azure Stack Edge 디바이스는 데이터 원본 서버의 데이터를 수집할 수 있도록 데이터 센터 네트워크에 연결해야 합니다. <!-- engg TBC -->

Azure Stack Edge 디바이스에서:

- 전면 패널에는 SSD 캐리어가 있습니다. 

    - 디바이스에는 슬롯에 SSD 디스크가 1개 있습니다. 
    - 또한 이 디바이스에는 운영 체제 디스크의 스토리지 역할을 하는 CFx 카드가 있습니다.
    
- 전면 패널에는 네트워크 인터페이스와 Wi-Fi에 대한 액세스 권한이 있습니다.

    - 1GbE RJ 45 네트워크 인터페이스 2개 이는 디바이스의 로컬 UI에 있는 PORT 1과 PORT 2입니다.
    - 10GbE SFP+ 네트워크 인터페이스 2개 이는 디바이스의 로컬 UI에 있는 PORT 3과 PORT 4입니다. 
    - Wi-Fi 트랜시버가 연결된 Wi-Fi 포트 1개

- 전면 패널에는 전원 단추도 있습니다. 

- 후면 패널에는 디바이스에 설치된 배터리 및 덮개가 포함되어 있습니다. 


디바이스의 전원 및 네트워크에 케이블을 연결하려면 다음 단계를 수행합니다.

1. 디바이스의 전면 평면에서 다양한 네트워크 및 스토리지 구성 요소를 식별합니다.

    ![디바이스의 네트워크 및 스토리지 인터페이스](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. 디바이스 전면의 왼쪽 아래에서 전원 단추를 찾습니다. 

    ![디바이스의 전원 단추가 있는 디바이스의 전면 평면](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. 배터리는 디바이스의 후면 평면에 연결됩니다. 배터리에 있는 두 번째 전원 단추를 식별합니다. 

    ![배터리에 전원 단추가 있는 디바이스의 전면 평면](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    전원 코드의 한쪽 끝을 배터리에, 다른 끝을 전원 콘센트에 연결합니다. 

    ![전원 코드 연결](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    배터리로만 실행하는 경우(배터리가 전원에 연결되지 않은 경우) 전면의 전원 스위치와 배터리의 스위치를 모두 ON 위치로 전환해야 합니다. 배터리가 전원에 연결된 경우에는 디바이스 전면의 전원 단추만 ON 위치로 전환해야 합니다. 

4. 전면 평면의 전원 단추를 눌러 디바이스를 켭니다. 
    
    > [!NOTE]
    > 디바이스의 전원을 켜거나 끄려면 전원 단추 위에 있는 검은색 단추를 누른 다음, 전원 단추를 ON 또는 OFF 위치로 전환해야 합니다. 

5. 이 디바이스에서 Wi-Fi를 구성하는 경우 다음 케이블 연결 다이어그램을 사용합니다.

    ![Wi-Fi용 케이블 연결](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - 1-GbE 네트워크 인터페이스 PORT 1을 물리적 디바이스 구성에 사용되는 컴퓨터에 연결합니다. PORT 1은 전용 관리 인터페이스입니다.


    이 디바이스에 대해 유선 구성을 사용하는 경우 다음 다이어그램을 사용합니다.
     
    ![유선용 케이블 연결](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - 1-GbE 네트워크 인터페이스 PORT 1을 물리적 디바이스 구성에 사용되는 컴퓨터에 연결합니다. PORT 1은 전용 관리 인터페이스입니다.
    - 하나 이상의 PORT 2, PORT 3 또는 PORT 4를 데이터 센터 네트워크/인터넷에 연결합니다.
    
        - PORT 2를 연결하는 경우 RJ-45 네트워크 케이블을 사용합니다.
        - 10-GbE 네트워크 인터페이스의 경우 SFP+ 동선 케이블을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법과 같은 Azure Stack Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 디바이스 포장 풀기
> * 디바이스에 케이블 연결

디바이스를 연결하고, 설정하고, 활성화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge 연결 및 설정](./azure-stack-edge-mini-r-deploy-connect.md)
