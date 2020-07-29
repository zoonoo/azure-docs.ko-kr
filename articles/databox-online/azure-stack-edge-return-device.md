---
title: Azure Stack Edge 장치 반환 | Microsoft Docs
description: Azure Stack Edge 장치를 반환 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: 018fe9b97b343bc07cf3c04a1d0e84edaf6cc7ac
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283863"
---
# <a name="return-your-azure-stack-edge-device"></a>Azure Stack Edge 장치 반환

이 문서에서는 데이터를 지우고 Azure Stack Edge 디바이스를 반환하는 방법을 설명합니다. 장치를 반환한 후에는 장치와 연결 된 리소스를 삭제할 수도 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 디바이스의 데이터 디스크에서 데이터 지우기
> * Azure Portal에서 장치 반환 시작
> * 디바이스 포장 및 픽업 예약
> * Microsoft Azure Portal에서 리소스 삭제

## <a name="erase-data-from-the-device"></a>디바이스에서 데이터 지우기

디바이스의 데이터 디스크에서 데이터를 지우려면 디바이스를 초기화해야 합니다. 로컬 웹 UI 또는 PowerShell 인터페이스를 사용하여 디바이스를 초기화할 수 있습니다.

필요한 경우 초기화하기 전에 디바이스의 로컬 데이터를 복사합니다. 디바이스에서 Azure Storage 컨테이너로 데이터를 복사할 수 있습니다.

장치를 다시 설정 하기 전에도 장치 반환을 시작할 수 있습니다. 

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

## <a name="initiate-device-return"></a>장치 반환 시작

반환 프로세스를 시작하려면 다음 단계를 수행하세요.

1. Azure Portal에서 Azure Stack Edge/Data Box Gateway 리소스로 이동 합니다. **개요**에서 오른쪽 창의 명령 모음으로 이동 하 고 **장치 반환**을 선택 합니다. 

    ![장치 1 반환](media/azure-stack-edge-return-device/return-device-1.png)  

2. **반환 장치** 블레이드의 **기본 세부 정보**:

    1. 장치의 일련 번호를 제공 합니다. 장치 일련 번호를 가져오려면 장치의 로컬 웹 UI로 이동한 다음 **개요**로 이동 합니다.  
    
    ![장치 일련 번호 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. 장치에 고유한 5 개 이상의 문자 식별자 인 서비스 태그 번호를 입력 합니다. 서비스 태그는 장치의 오른쪽 아래 모퉁이에 있습니다 (장치를 향하는 경우). 정보 태그를 끌어옵니다 (슬라이드 아웃 레이블 패널). 이 패널에는 서비스 태그, NIC, MAC 주소 등의 시스템 정보가 포함 되어 있습니다. 
    
    ![서비스 태그 번호 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. 드롭다운 목록에서 반환 이유를 선택 합니다.

    ![장치 2 반환](media/azure-stack-edge-return-device/return-device-2.png) 

3. **배송 세부 정보**:

    1. 이름, 회사 이름 및 전체 회사 주소를 제공 합니다. 지역 번호 및 알림에 대 한 전자 메일 ID를 포함 하는 직장 전화를 입력 합니다.
    2. 반송 상자가 필요한 경우 요청할 수 있습니다. **반환용 빈 상자 필요** 질문에 **예**로 답합니다.

    ![장치 3 반환](media/azure-stack-edge-return-device/return-device-3.png)

4. **개인 정보 취급 방침** 을 검토 하 고이 확인란을 선택 하 여 개인 정보 취급 방침을 검토 하 고이에 동의 합니다.

5. **반환 시작**을 선택 합니다.

    ![장치 4 반환](media/azure-stack-edge-return-device/return-device-4.png) 

6. 장치 반환 세부 정보를 캡처하면 전자 메일을 통해 Azure Stack에 지 운영 팀에 알릴 수 있습니다. 전자 메일 응용 프로그램을 설치 및 구성 했다고 가정 하 고 전자 메일 응용 프로그램을 사용할 수 있습니다. 또한 데이터를 복사 하 여 전자 메일을 만들고 보낼 수 있습니다.

    ![장치 5 반환](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge 운영 팀이 전자 메일을 받은 후에는 역방향 배송 레이블을 보냅니다. 이 레이블을 받으면 캐리어를 사용 하 여 장치 픽업을 예약할 수 있습니다. 

## <a name="schedule-a-pickup"></a>픽업 예약

픽업을 예약 하려면 다음 단계를 수행 합니다.

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

- 장치가 그대로 도착 하 고 좋은 셰이프 이면 해당 리소스에 대 한 청구 측정기가 중지 됩니다. Edge 운영 팀 Azure Stack 장치가 반환 되었는지 확인 하기 위해 연락 합니다. 그러면 Microsoft Azure Portal에서 디바이스와 연결된 리소스를 삭제할 수 있습니다.
- 디바이스가 많이 손상된 상태로 도착하면 벌금이 부과될 수 있습니다. 자세한 내용은 [분실 또는 손상된 디바이스에 대한 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/)와 [제품 서비스 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요.  


Microsoft Azure Portal에서 디바이스를 삭제할 수 있습니다.

- 주문한 후 Microsoft에서 디바이스를 준비하기 전
- 장치를 Microsoft에 반환 하면 Azure 데이터 센터에서 물리적 검사를 전달 하 고, Edge 운영 팀 호출을 Azure Stack 하 여 장치가 반환 되었는지 확인 합니다.

다른 구독 또는 위치에 대해 디바이스를 활성화한 경우 Microsoft는 1일(업무일 기준) 이내에 주문을 새 구독 또는 위치로 이동합니다. 주문이 이동된 후 이 리소스를 삭제할 수 있습니다.


Microsoft Azure Portal에서 디바이스와 리소스를 삭제하려면 다음 단계를 수행하세요.

1. Microsoft Azure Portal에서 리소스로 이동한 다음, **개요**로 이동합니다. 명령 모음에서 **삭제**를 선택합니다.

    ![삭제 선택](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **디바이스 삭제** 블레이드에서 삭제하려는 디바이스의 이름을 입력하고 **삭제**를 선택합니다.

    ![삭제 확인](media/azure-stack-edge-return-device/delete-resource-2.png)

디바이스와 연결된 리소스가 성공적으로 삭제되면 알림을 받습니다.


## <a name="next-steps"></a>다음 단계

- [대체 Azure Stack Edge 장치를 가져오는](azure-stack-edge-replace-device.md)방법에 대해 알아봅니다.
