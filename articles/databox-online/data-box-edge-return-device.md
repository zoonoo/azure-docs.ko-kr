---
title: Azure 데이터 상자 Edge 장치를 반환 합니다. | Microsoft Docs
description: Azure 데이터 상자 Edge 장치를 반환할 장치에 대 한 주문을 삭제 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468606"
---
# <a name="return-your-azure-data-box-edge-device"></a>Azure 데이터 상자 Edge 장치를 반환 합니다.

이 문서에서는 데이터를 초기화 한 다음 Azure 데이터 상자 Edge 장치를 반환 하는 방법을 설명 합니다. 장치를 반환한 후에, 한 후에 장치와 연결 된 리소스를 삭제할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 장치에서 데이터 디스크 데이터를 초기화 합니다.
> * 장치를 반환할 지원 티켓 열기
> * 장치를 압축 하 고는 픽업을 예약
> * Azure portal에서 리소스를 삭제 합니다.

## <a name="erase-data-from-the-device"></a>장치의 데이터 지우기

데이터 디스크 장치의 데이터를 초기화 하려면 장치를 초기화 해야 합니다. 로컬 웹 UI 또는 PowerShell 인터페이스를 사용 하 여 장치를 재설정할 수 있습니다.

다시 설정 하기 전에 필요한 경우 장치의 로컬 데이터의 복사본이 만듭니다. 장치에서 Azure Storage 컨테이너에 데이터를 복사할 수 있습니다.

로컬 웹 UI를 사용 하 여 장치를 재설정 하려면 다음 단계를 수행 합니다.

1. 로컬 웹 UI로 이동 **유지 관리 > 장치 재설정**합니다.
2. 선택 **재설정 한 장치**합니다.

    ![디바이스 다시 설정](media/data-box-edge-return-device/device-reset-1.png)

3. 확인 메시지가 표시 되 면 선택한 경고를 검토 **예** 를 계속 합니다.

    ![재설정 확인](media/data-box-edge-return-device/device-reset-2.png)  

재설정 장치 데이터 디스크의 데이터를 지웁니다. 장치에서 데이터의 양에 따라이 프로세스는 약 30 ~ 40 분 정도 걸립니다.

또는 PowerShell 인터페이스를 사용 하 여 장치에 연결 된 `Reset-HcsAppliance` 데이터 디스크에서 데이터를 지울 cmdlet. 자세한 내용은 [장치를 재설정](data-box-edge-connect-powershell-interface.md#reset-your-device)합니다.

> [!NOTE]
> - 교환 또는 새 장치를 업그레이드 하는 경우에 새 장치를 받은 후에 장치를 초기화 하는 것이 좋습니다.
> - 만 다시 설정 하는 장치에서이 장치는 모든 로컬 데이터를 삭제 합니다. 클라우드에 있는 데이터는 삭제 되지 않습니다 하 고 수집 [요금](https://azure.microsoft.com/pricing/details/storage/)합니다. 이 데이터와 같은 클라우드 저장소 관리 도구를 사용 하 여 별도로 삭제 해야 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)합니다.

## <a name="open-a-support-ticket"></a>지원 티켓 열기

이 과정을 시작 하려면 다음 단계를 수행 합니다.

1. 장치를 반환할 것인지를 나타내는 Microsoft 지원으로 지원 티켓을 엽니다. 문제 유형으로 **데이터 상자 가장자리 하드웨어**합니다.

    ![지원 티켓 열기](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어가 연락해 것입니다. 배송 세부 정보를 제공 합니다.
3. 반환 전달 상자에 필요한 경우에이 요청할 수 있습니다. 응답 **Yes** 질문에 대 한 **반환할 빈 상자를 필요한**.


## <a name="schedule-a-pickup"></a>픽업을 예약합니다

1. 디바이스를 종료합니다. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. 선택 **종료**합니다. 확인 대화 상자가 나타나면 클릭 **예** 를 계속 합니다. 자세한 내용은 [전원 관리](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)합니다.
3. 전원 케이블을 분리 하 고 장치에서 모든 네트워크 케이블을 제거 합니다.
4. 직접 또는 Azure에서 받은 빈 상자를 사용 하 여 배송 패키지를 준비 합니다. 장치 및 상자에서 장치를 사용 하 여 제공 된 전원 코드를 배치 합니다.
5. 접사 패키지에 대해 Azure에서 제공 된 배송 레이블을 확인해 보십시오.
6. 해당 지역 통신 업체를 사용 하 여는 픽업을 예약 합니다. 이동 통신사 미국에서 장치를 반환, 경우 UPS입니다. 픽업을 예약하려면 다음을 수행합니다.

    1. 로컬 UPS(국가별 무료 전화 번호)에 전화합니다.
    2. 호출에서 추적 번호 인쇄 레이블에 표시 된 것 처럼 역방향 배송을 인용 합니다.
    3. 추적 번호를 하지 인용 부호로 픽업 하는 동안 추가 비용을 지불할 수 있습니다 UPS 해야 합니다.

    픽업을 예약 하는 대신 가장 가까운 차 위치의 데이터 상자 가장자리 삭제할 수 있습니다.

## <a name="delete-the-resource"></a>리소스를 삭제 합니다.

Azure 데이터 센터에서 장치를 받은 후 장치 손상이 나 변조 모든 징후에 대해 검사 됩니다.

- 장치에서 그대로 유지 하 고 양호한 상태에 도착 하면 해당 리소스에 대 한 청구 측정기 중지 됩니다. Microsoft 지원에서 장치 반환 된 것인지 연락을 합니다. Azure portal에서 해당 장치와 연결 된 리소스를 삭제할 수 있습니다.
- 장치가 손상 크게 도착 하면 벌금 적용 될 수 있습니다. 세부 정보를 참조 하세요.는 [손실 되거나 손상 된 장치에 대 한 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) 하 고 [서비스 제품 약관](https://www.microsoft.com/licensing/product-licensing/products)합니다.  


Azure portal에서 장치를 삭제할 수 있습니다.
-   순서를 배치한 후 및 하기 전에 Microsoft에서 장치 준비 됩니다.
-   장치를 Microsoft로 반환한 후에, Azure 데이터 센터 실제 검사를 전달 하기 후 장치가 반환 된를 확인 하려면 Microsoft 지원 통화.

다른 구독 또는 위치에 대 한 장치를 활성화 한 경우 Microsoft는 1 영업일 이내에 순서를 새 구독 또는 위치로 이동 됩니다. 순서를 이동한 후에이 리소스를 삭제할 수 있습니다.


장치 및 Azure portal에서 리소스를 삭제 하려면 다음 단계를 수행 합니다.

1. Azure portal에서 리소스를 이동 하 고 **개요**합니다. 명령 모음에서 선택 **삭제**합니다.

    ![삭제 선택](media/data-box-edge-return-device/delete-resource-1.png)

2. 에 **장치 삭제** 블레이드에서 삭제 하 고 선택 하려는 장치의 이름을 입력 **삭제**합니다.

    ![삭제 확인](media/data-box-edge-return-device/delete-resource-2.png)

장치 후 알림이 표시 및 연결된 된 리소스가 성공적으로 삭제 합니다.

## <a name="next-steps"></a>다음 단계

- [대역폭을 관리](data-box-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
