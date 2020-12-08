---
title: Azure Stack Edge Pro R 물리적 디바이스를 설치하는 방법에 대한 자습서 | Microsoft Docs
description: Azure Stack Edge Pro R 설치에 대한 두 번째 자습서에서는 전원 및 네트워크에 대한 물리적 디바이스의 케이블을 연결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464595"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>자습서: Azure Stack Edge Pro R 설치

이 자습서에서는 Azure Stack Edge Pro R 물리적 디바이스를 설치하는 방법에 대해 설명합니다. 설치 절차에는 디바이스에 케이블 연결이 포함됩니다.

설치를 완료하는 데 30분 정도가 걸릴 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 디바이스 검사
> * 디바이스에 케이블 연결

## <a name="prerequisites"></a>사전 요구 사항

물리적 디바이스를 설치하기 위한 필수 조건은 다음과 같습니다.

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge 리소스

시작하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Pro R 배포 준비](azure-stack-edge-pro-r-deploy-prep.md)의 모든 단계를 완료했습니다.
    * 디바이스를 배포할 Azure Stack Edge 리소스를 만들었습니다.
    * Azure Stack Edge 리소스를 통해 디바이스를 활성화하기 위한 활성화 키를 생성했습니다.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Azure Stack Edge Pro R 물리적 디바이스의 경우

디바이스를 배포하기 전에 다음 사항을 확인합니다.

- 디바이스를 평평하고 안정적이고 흔들림 없는 작업대에 안전하게 놓습니다.
- 장치를 설치하려는 사이트에 다음 장치가 있는지 확인합니다.
    - 독립적인 전원의 표준 AC 전원 장치

        또는
    - 랙 PDU(전력 배포 장치) 디바이스는 UPS(무정전 전원 공급 장치)와 함께 제공됩니다.
    

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우

시작하기 전에

- Azure Stack Edge Pro R을 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성합니다. 자세한 내용은 [Azure Stack Edge Pro R 네트워킹 요구 사항](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements)을 참조하세요.

- 디바이스가 최적으로 작동할 수 있도록 최소 인터넷 대역폭이 20Mbps인지 확인합니다.


## <a name="inspect-the-device"></a>디바이스 검사

이 디바이스는 단일 장치로 제공됩니다. 디바이스의 포장을 풀려면 다음 단계를 수행합니다.

1. 평평한 지면에 상자를 놓습니다.
2. 디바이스 케이스에 손상이 있는지 검사합니다. 케이스를 열고 디바이스를 검사합니다. 케이스 또는 디바이스가 손상된 것으로 보이는 경우 디바이스가 올바르게 작동하는지 여부를 평가하는 데 도움을 얻으려면 Microsoft 지원에 문의하세요.
3. 케이스를 연 후 다음이 있는지 확인합니다.
    - 단일 인클로저 Azure Stack Edge Pro R 디바이스 1개
    - UPS(무정전 전원 공급 장치) 1개
    - 디바이스를 UPS에 연결하는 짧은 전원 케이블 2개
    - 전원에 UPS를 연결하는 전원 케이블 1개

여기에 나열된 항목 중에 받지 못한 것이 있으면 Azure Stack Edge Pro R 지원에 문의하세요. 다음 단계는 디바이스에 케이블을 연결하는 것입니다.


## <a name="cable-the-device"></a>디바이스에 케이블 연결

다음 절차에서는 전원 및 네트워크를 위해 Azure Stack Edge Pro R 디바이스에 케이블을 연결하는 방법에 대해 설명합니다.

디바이스 케이블 연결을 시작하기 전에 다음이 필요합니다.

- 설치 사이트의 Azure Stack Edge Pro R 물리적 디바이스
- 전원 케이블 1개
- 관리 인터페이스에 연결하는 하나 이상의 1-GbE RJ-45 네트워크 케이블 디바이스에는 두 개의 1-GbE 네트워크 인터페이스가 있습니다. 하나는 관리용이고 하나는 데이터용입니다.
- 구성할 각 데이터 네트워크 인터페이스에 대한 10/25-GbE SFP+ 동 케이블 1개 하나 이상의 데이터 네트워크 인터페이스 - PORT 3 또는 PORT 4를 인터넷에 연결해야 합니다(Azure에 연결).  
- 전력 분배 장치 1개에 대한 액세스(권장)

> [!NOTE]
> - 단 하나의 데이터 네트워크 인터페이스를 연결하는 경우 PORT 3 또는 PORT 4와 같은 25/10-GbE 네트워크 인터페이스를 사용하여 데이터를 Azure에 보내는 것이 좋습니다. 
> - 최상의 성능을 얻고 많은 양의 데이터를 처리하려면 모든 데이터 포트를 연결하는 것이 좋습니다.
> - Azure Stack Edge Pro R 디바이스는 데이터 원본 서버의 데이터를 수집할 수 있도록 데이터 센터 네트워크에 연결해야 합니다.

Azure Stack Edge Pro R 디바이스에서 다음을 수행합니다.

- 전면 패널에는 디스크 드라이브와 전원 단추가 있습니다.

    - 디바이스의 전면에 8개의 디스크 슬롯이 있습니다.
    - 또한 디바이스에는 운영 체제 디스크로 사용되는 2 X M.2 SATA 디스크가 있습니다. 

- 백플레인에는 중복 PSU(전원 공급 장치)가 있습니다.
- 백플레인에는 4개의 네트워크 인터페이스가 있습니다.

    - 1-Gbps 인터페이스 2개
    - 10-Gbps 인터페이스로도 사용할 수 있는 25-Gbps 인터페이스 2개
    - BMC(베이스보드 관리 컨트롤러)

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
디바이스의 전원 및 네트워크에 케이블을 연결하려면 다음 단계를 수행합니다.

1. 디바이스의 백플레인에서 다양한 포트를 식별합니다.

    ![케이블로 연결된 디바이스의 백플레인](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. 디바이스의 전면에서 디스크 슬롯과 전원 단추를 찾습니다.

    ![디바이스의 프런트플레인](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. 전원 코드의 한쪽 끝을 UPS에 연결합니다. 전원 코드의 다른 끝을 랙 PDU(전원 분배 장치)에 연결합니다. 
5. 전원 단추를 눌러 디바이스를 켭니다.
6. 1-GbE 네트워크 인터페이스 PORT 1을 물리적 디바이스 구성에 사용되는 컴퓨터에 연결합니다. PORT 1은 전용 관리 인터페이스입니다.
7. 하나 이상의 PORT 2, PORT 3 또는 PORT 4를 데이터 센터 네트워크/인터넷에 연결합니다.

    - PORT 2를 연결하는 경우 RJ-45 네트워크 케이블을 사용합니다.
    - 10/25-GbE 네트워크 인터페이스의 경우 SFP+ 동선 케이블을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법과 같은 Azure Stack Edge Pro R 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 디바이스 포장 풀기
> * 디바이스에 케이블 연결

다음 자습서로 이동하여 디바이스에 연결하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R에 연결](./azure-stack-edge-pro-r-deploy-connect.md)
