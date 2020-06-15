---
title: Azure Stack Edge 디바이스 반환 또는 바꾸기 | Microsoft Docs
description: Azure Stack Edge 디바이스를 반환하거나 바꾸는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743631"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스 반환 또는 바꾸기

이 문서에서는 데이터를 지우고 Azure Stack Edge 디바이스를 반환하는 방법을 설명합니다. 디바이스를 반환한 후에는 디바이스와 연결된 리소스를 삭제하거나 교체 디바이스를 주문할 수도 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스의 데이터 디스크에서 데이터 지우기
> * 지원 티켓을 열어 디바이스 반환
> * 디바이스 포장 및 픽업 예약
> * Microsoft Azure Portal에서 리소스 삭제
> * 교체 디바이스 받기

## <a name="erase-data-from-the-device"></a>디바이스에서 데이터 지우기

디바이스의 데이터 디스크에서 데이터를 지우려면 디바이스를 초기화해야 합니다. 로컬 웹 UI 또는 PowerShell 인터페이스를 사용하여 디바이스를 초기화할 수 있습니다.

필요한 경우 초기화하기 전에 디바이스의 로컬 데이터를 복사합니다. 디바이스에서 Azure Storage 컨테이너로 데이터를 복사할 수 있습니다.

로컬 웹 UI를 사용하여 디바이스를 초기화하려면 다음 단계를 수행하세요.

1. 로컬 웹 UI에서 **유지 관리 > 디바이스 초기화**로 이동합니다.
2. **디바이스 초기화**를 선택합니다.

    ![디바이스 다시 설정](media/azure-stack-edge-return-device/device-reset-1.png)

3. 확인 메시지가 나타나면 경고를 검토하고 **예**를 선택하여 계속합니다.

    ![초기화 확인](media/azure-stack-edge-return-device/device-reset-2.png)  

초기화하면 디바이스 데이터 디스크에서 데이터가 지워집니다. 이 프로세스에는 디바이스의 데이터 양에 따라 30 ~ 40분 정도 소요됩니다.

또는 디바이스의 PowerShell 인터페이스에 연결하고 `Reset-HcsAppliance` cmdlet을 사용하여 데이터 디스크에서 데이터를 지웁니다. 자세한 내용은 [디바이스 초기화](azure-stack-edge-connect-powershell-interface.md#reset-your-device)를 참조하세요.

> [!NOTE]
> - 디바이스를 교환하거나 새 디바이스로 업그레이드하는 경우 새 디바이스를 받은 후에만 디바이스를 초기화하는 것이 좋습니다.
> - 디바이스를 재설정하면 디바이스에 있는 로컬 데이터만 모두 삭제됩니다. 클라우드에 있는 데이터는 삭제되지 않으며 [요금](https://azure.microsoft.com/pricing/details/storage/)이 징수됩니다. 이 데이터는 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 클라우드 스토리지 관리 도구를 사용하여 별도로 삭제해야 합니다.

## <a name="open-a-support-ticket"></a>지원 티켓 열기

반환 프로세스를 시작하려면 다음 단계를 수행하세요.

1. Microsoft 지원에서 지원 티켓을 열어 디바이스 반환 의사를 밝힙니다. 문제 유형을 **Azure Stack Edge 하드웨어**로 선택합니다.

    ![지원 티켓 열기](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어가 연락드릴 것입니다. 배송 정보를 제공합니다.
3. 반송 상자가 필요한 경우 요청할 수 있습니다. **반환용 빈 상자 필요** 질문에 **예**로 답합니다.


## <a name="schedule-a-pickup"></a>픽업 예약

1. 디바이스를 종료합니다. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. **종료**를 선택합니다. 확인 메시지가 표시되면 **예**를 클릭하여 계속합니다. 자세한 내용은 [전원 관리](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)를 참조하세요.
3. 전원 케이블을 분리하고 디바이스에서 모든 네트워크 케이블을 제거합니다.
4. 가지고 있는 상자나 Azure에서 받은 빈 상자를 사용하여 배송 꾸러미를 준비합니다. 디바이스와 함께 제공된 전원 코드를 상자에 넣습니다.
5. Azure에서 받은 배송 레이블을 상자에 붙입니다.
6. 해당 지역 운송업체에서 픽업하도록 예약합니다. 미국 내에서 디바이스를 반환하는 경우 UPS 또는 FedEx를 이용할 수 있습니다. UPS로 픽업을 예약하려면 다음을 수행합니다.

    1. 로컬 UPS(국가/지역별 무료 전화 번호)에 전화합니다.
    2. 통화 시 인쇄된 레이블에 표시된 역방향 배송 추적 번호를 알려줍니다.
    3. 추적 번호를 알려주지 않으면 픽업 시 UPS에서 추가 요금을 지불하도록 요구합니다.

    픽업을 예약하는 대신 가장 가까운 반납 위치에 Azure Stack Edge를 반납할 수도 있습니다.

## <a name="delete-the-resource"></a>리소스 삭제

Azure 데이터 센터에서 디바이스를 받은 후 디바이스에 손상 또는 변조 징후가 있는지 검사합니다.

- 디바이스가 온전하고 정상적인 상태로 도착하면 해당 리소스에 대한 청구 미터가 중지됩니다. Microsoft 지원에서 디바이스 반환 확인을 위해 연락드립니다. 그러면 Microsoft Azure Portal에서 디바이스와 연결된 리소스를 삭제할 수 있습니다.
- 디바이스가 많이 손상된 상태로 도착하면 벌금이 부과될 수 있습니다. 자세한 내용은 [분실 또는 손상된 디바이스에 대한 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/)와 [제품 서비스 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요.  


Microsoft Azure Portal에서 디바이스를 삭제할 수 있습니다.
-    주문한 후 Microsoft에서 디바이스를 준비하기 전
-    디바이스가 Microsoft에 반환된 후 Azure 데이터 센터에서 실물 검사를 통과하면 Microsoft 지원 센터에서 디바이스 반환 확인을 위해 전화를 드립니다.

다른 구독 또는 위치에 대해 디바이스를 활성화한 경우 Microsoft는 1일(업무일 기준) 이내에 주문을 새 구독 또는 위치로 이동합니다. 주문이 이동된 후 이 리소스를 삭제할 수 있습니다.


Microsoft Azure Portal에서 디바이스와 리소스를 삭제하려면 다음 단계를 수행하세요.

1. Microsoft Azure Portal에서 리소스로 이동한 다음, **개요**로 이동합니다. 명령 모음에서 **삭제**를 선택합니다.

    ![삭제 선택](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **디바이스 삭제** 블레이드에서 삭제하려는 디바이스의 이름을 입력하고 **삭제**를 선택합니다.

    ![삭제 확인](media/azure-stack-edge-return-device/delete-resource-2.png)

디바이스와 연결된 리소스가 성공적으로 삭제되면 알림을 받습니다.

## <a name="get-a-replacement-device"></a>교체 디바이스 받기

기존 디바이스에 하드웨어 오류가 있거나 업그레이드가 필요한 경우 교체 디바이스가 필요합니다. 디바이스에 하드웨어 오류가 있으면 다음 단계를 수행하세요.

1. [하드웨어 문제에 대한 지원 티켓을 엽니다](#open-a-support-ticket). Microsoft 지원에서 이 인스턴스에 FRU(Field Replacement Unit)를 사용할 수 없거나 디바이스에 하드웨어 업그레이드가 필요하다고 판단합니다. 두 경우 모두 Microsoft 지원에서 대체 디바이스를 주문합니다.
2. 교체 디바이스에 대한 [새 리소스를 만듭니다](azure-stack-edge-deploy-prep.md#create-a-new-resource). **Azure Stack Edge 디바이스 있음** 확인란을 선택합니다. 
3. 교체 디바이스를 받은 후 새 리소스에 대해 교체 디바이스를 [설치](azure-stack-edge-deploy-install.md)하고 [활성화](azure-stack-edge-deploy-connect-setup-activate.md)합니다.
4. 모든 단계에 따라 원래 디바이스를 반환합니다.
    1. 다른 티켓을 열어 원래 디바이스를 반환합니다.
    2. [디바이스에서 데이터를 지웁니다](#erase-data-from-the-device).
    3. [픽업을 예약합니다](#schedule-a-pickup).
    5. 반환된 디바이스와 연결된 [리소스를 삭제합니다](#delete-the-resource).



## <a name="next-steps"></a>다음 단계

- [대역폭을 관리](azure-stack-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
