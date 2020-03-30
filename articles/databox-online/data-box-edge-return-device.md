---
title: Azure 데이터 박스 에지 장치 반환 또는 교체 | 마이크로 소프트 문서
description: Azure 데이터 상자 가장자리 장치를 반환하거나 교체하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651103"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Azure 데이터 상자 가장자리 장치 반환 또는 교체

이 문서에서는 데이터를 초기화한 다음 Azure 데이터 상자 가장자리 장치를 반환하는 방법에 대해 설명합니다. 장치를 반환한 후 장치와 연결된 리소스를 삭제하거나 대체 장치를 주문할 수도 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 장치의 데이터 디스크에서 데이터 지우기
> * 지원 티켓을 열어 기기를 반품합니다.
> * 장치를 포장하고 픽업을 예약합니다.
> * Azure 포털에서 리소스 삭제
> * 교체 장치 가져오기

## <a name="erase-data-from-the-device"></a>장치에서 데이터 지우기

장치의 데이터 디스크에서 데이터를 초기화하려면 장치를 재설정해야합니다. 로컬 웹 UI 또는 PowerShell 인터페이스를 사용하여 장치를 재설정할 수 있습니다.

재설정하기 전에 필요한 경우 장치에서 로컬 데이터의 복사본을 만듭니다. 장치에서 Azure 저장소 컨테이너로 데이터를 복사할 수 있습니다.

로컬 웹 UI를 사용하여 장치를 재설정하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **유지 > 장치 재설정으로**이동합니다.
2. **장치 재설정을**선택합니다.

    ![디바이스 다시 설정](media/data-box-edge-return-device/device-reset-1.png)

3. 확인 메시지가 표시되면 경고를 검토하고 **예를** 선택하여 계속합니다.

    ![재설정 확인](media/data-box-edge-return-device/device-reset-2.png)  

리셋은 장치 데이터 디스크에서 데이터를 지웁습니다. 기기의 데이터 양에 따라 이 프로세스는 약 30-40분 정도 걸립니다.

또는 장치의 PowerShell 인터페이스에 연결하고 cmdlet을 `Reset-HcsAppliance` 사용하여 데이터 디스크에서 데이터를 지웁니까? 자세한 내용은 [장치 재설정](data-box-edge-connect-powershell-interface.md#reset-your-device)을 참조하십시오.

> [!NOTE]
> - 새 기기를 교환하거나 업그레이드하는 경우 새 기기를 받은 후에만 기기를 재설정하는 것이 좋습니다.
> - 장치 재설정은 장치에서 모든 로컬 데이터만 삭제합니다. 클라우드에 있는 데이터는 삭제되지 않으며 [요금을](https://azure.microsoft.com/pricing/details/storage/)수집합니다. 이 데이터는 [Azure 저장소 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 클라우드 저장소 관리 도구를 사용하여 별도로 삭제해야 합니다.

## <a name="open-a-support-ticket"></a>지원 티켓 열기

반환 프로세스를 시작하려면 다음 단계를 수행합니다.

1. 장치를 반환할 예정임을 나타내는 Microsoft 지원 지원 으로 지원 티켓을 엽니다. 문제 유형을 **데이터 상자 가장자리 하드웨어로**선택합니다.

    ![지원 티켓 열기](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어가 연락을 드릴 것입니다. 배송 세부 정보를 제공합니다.
3. 반품 배송 상자가 필요한 경우 요청할 수 있습니다. 질문에 **예** 답변 **반환하려면 빈 상자가 필요합니다.**


## <a name="schedule-a-pickup"></a>픽업 예약

1. 디바이스를 종료합니다. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. **종료를 선택합니다.** 확인 메시지가 표시되면 **예를** 클릭하여 계속합니다. 자세한 내용은 [전원 관리를](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)참조하십시오.
3. 전원 케이블의 플러그를 뽑고 장치에서 모든 네트워크 케이블을 제거합니다.
4. 사용자 고유의 상자 또는 Azure에서 받은 빈 상자를 사용하여 발송물 패키지를 준비합니다. 장치와 장치와 함께 제공된 전원 코드를 상자에 놓습니다.
5. 패키지에 Azure에서 받은 배송 레이블을 부착합니다.
6. 해당 지역 운송업체에서 픽업하도록 예약합니다. 미국에서 기기를 반품하는 경우 이동통신사는 UPS 또는 FedEx일 수 있습니다. UPS로 픽업을 예약하려면 다음

    1. 로컬 UPS(국가별 무료 전화 번호)에 전화합니다.
    2. 통화에서 인쇄된 라벨에 표시된 역배송 선적 조회 번호를 인용합니다.
    3. 조회 번호가 인용되지 않은 경우 UPS는 픽업 시 추가 요금을 지불해야 합니다.

    픽업을 예약하는 대신 가장 가까운 드롭오프 위치에서 데이터 상자 가장자리를 드롭오프할 수도 있습니다.

## <a name="delete-the-resource"></a>리소스 삭제

Azure 데이터 센터에서 장치를 받은 후 장치가 손상또는 변조의 징후가 있는지 검사합니다.

- 장치가 손상되지 않고 상태가 양호하면 해당 리소스에 대한 청구 미터가 중지됩니다. Microsoft 지원담당자가 연락하여 장치가 반환됐는지를 확인합니다. 그런 다음 Azure 포털에서 장치와 연결된 리소스를 삭제할 수 있습니다.
- 장치가 크게 손상된 도착하면 벌금이 부과될 수 있습니다. 자세한 내용은 [분실 또는 손상된 장치에 대한 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) 및 제품 서비스 [약관을](https://www.microsoft.com/licensing/product-licensing/products)참조하십시오.  


Azure 포털에서 장치를 삭제할 수 있습니다.
-   당신이 주문을 한 후 장치가 마이크로 소프트에 의해 준비되기 전에.
-   장치를 Microsoft로 반환한 후 Azure 데이터 센터에서 물리적 검사를 전달하고 Microsoft 지원에서 장치가 반환되어 있는지 확인합니다.

다른 구독 또는 위치에 대해 장치를 활성화한 경우 Microsoft는 주문이 영업일 1일 이내에 새 구독 또는 위치로 이동합니다. 순서를 이동한 후 이 리소스를 삭제할 수 있습니다.


Azure 포털에서 장치 및 리소스를 삭제하려면 다음 단계를 수행합니다.

1. Azure 포털에서 리소스로 이동한 다음 개요 로 **이동합니다.** 명령 모음에서 **삭제를**선택합니다.

    ![삭제 선택](media/data-box-edge-return-device/delete-resource-1.png)

2. 장치 **블레이드 삭제에서** 삭제할 장치의 이름을 입력하고 **삭제를**선택합니다.

    ![삭제 확인](media/data-box-edge-return-device/delete-resource-2.png)

장치가 삭제되고 관련 리소스가 성공적으로 삭제된 후 알림이 전송됩니다.

## <a name="get-a-replacement-device"></a>교체 장치 가져오기

기존 장치에 하드웨어 오류가 있거나 업그레이드가 필요한 경우 교체 장치가 필요합니다. 장치에 하드웨어 문제가 있는 경우 다음 단계를 수행합니다.

1. [하드웨어 문제에 대한 지원 티켓을 엽니다.](#open-a-support-ticket) Microsoft 지원팀은 이 인스턴스에 대해 FRU(필드 교체 장치)를 사용할 수 없거나 장치에 하드웨어 업그레이드가 필요한지 확인합니다. 두 경우 모두 지원에서 교체 장치를 주문합니다.
2. 대체 장치에 대한 [새 리소스를 만듭니다.](data-box-edge-deploy-prep.md#create-a-new-resource) 데이터 상자 가장자리 장치가 **있는 경우 확인란을**선택해야 합니다. 
3. 교체 장치를 받은 후 새 리소스에 대해 교체 장치를 [설치하고](data-box-edge-deploy-install.md) [활성화합니다.](data-box-edge-deploy-connect-setup-activate.md)
4. 모든 단계를 수행하여 원래 장치를 반환합니다.
    1. 다른 티켓을 열어 원래 장치를 반환합니다.
    2. [장치에서 데이터를 지웁볼](#erase-data-from-the-device)수 있습니다.
    3. [픽업을 예약합니다.](#schedule-a-pickup)
    5. 반환된 장치와 연결된 [리소스를 삭제합니다.](#delete-the-resource)



## <a name="next-steps"></a>다음 단계

- [대역폭을 관리](data-box-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
