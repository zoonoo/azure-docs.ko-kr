---
title: Microsoft Azure Data Box 주문 | Microsoft Docs
description: 배포 필수 구성 요소 및 Azure Data Box를 주문하는 방법을 알아봅니다
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: alkohli
ms.openlocfilehash: 5ce8217629cff702ecd3ec274d77918e54efacc6
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359122"
---
# <a name="tutorial-order-azure-data-box"></a>자습서: Azure Data Box 주문

Azure Data Box는 빠르고 쉽게 신뢰할 수 있는 방식으로 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션입니다. Microsoft에서 제공한 80TB(사용 가능한 용량) 저장소 디바이스에 데이터를 전송한 다음, 디바이스를 다시 제공합니다. 그런 다음, 이 데이터는 Azure에 업로드됩니다.

이 자습서에서는 Azure Data Box를 주문하는 방법에 대해 설명합니다. 이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box를 배포하기 위한 필수 구성 요소
> * Data Box 주문
> * 주문 추적
> * 주문 취소

## <a name="prerequisites"></a>필수 조건

디바이스를 배포하기 전에 Data Box 서비스 및 디바이스에 대해 다음 필수 구성 요소를 완료합니다.

### <a name="for-service"></a>서비스의 경우

시작하기 전에 다음 사항을 확인합니다.
- 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.
- Data Box 서비스에 사용할 구독이 다음 형식 중 하나인지 확인합니다.
    - Microsoft EA(기업계약). [EA 구독](https://azure.microsoft.com/pricing/enterprise-agreement/)에 대해 자세히 알아보세요.
    - CSP(클라우드 솔루션 공급자). [CSP 프로그램](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)에 대해 자세히 알아보세요.
    - Microsoft Azure 스폰서쉽. [Azure 스폰서쉽 프로그램](https://azure.microsoft.com/offers/ms-azr-0036p/)에 대해 자세히 알아보세요. 

- Data Box 주문을 작성할 구독에 대한 소유자 또는 참가자 권한이 있는지 확인합니다.

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.
- 호스트 컴퓨터를 데이터 센터 네트워크에 연결해야 합니다. Data Box는 이 컴퓨터에서 데이터를 복사합니다. [Azure Data Box 시스템 요구 사항](data-box-system-requirements.md)에 설명된 대로 호스트 컴퓨터는 지원되는 운영 체제를 실행해야 합니다.
- 데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용할 수 있지만 복사 속도에 영향을 미칩니다.


## <a name="order-data-box"></a>Data Box 주문

디바이스를 주문하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음 URL에서 로그인합니다. [https://portal.azure.com](https://portal.azure.com)
2. **+ 리소스 만들기**를 클릭하고 *Azure Data Box*를 검색합니다. **Azure Data Box**를 클릭합니다.
    
   [![Azure Data Box 1 검색](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. **만들기**를 클릭합니다.

4. 해당 지역에서 Data Box 서비스를 사용할 수 있는지 확인합니다. 다음 정보를 입력하거나 선택하고 **적용**을 클릭합니다. 
    |설정  |값  |
    |---------|---------|
    |구독     | Data Box 서비스에 대한 EA, CSP 또는 Azure 스폰서쉽 구독을 선택합니다. <br> 구독은 대금 청구 계정에 연결됩니다.       |
    |전송 형식     | **Azure로 가져오기**를 선택합니다.        |
    |원천 국가     |   현재 데이터가 있는 국가를 선택합니다.         |
    |대상 Azure 지역     |     데이터를 전송하려는 Azure 지역을 선택합니다.        |

5. Data **Box**를 선택합니다. 단일 주문에 대한 최대 솔루션 용량은 80TB입니다. 더 큰 데이터 크기에 대해 여러 개의 주문을 만들 수 있습니다.

      [![Data Box 옵션 1 선택](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. **주문**에서 **주문 세부 정보**를 지정합니다. 다음 정보를 입력하거나 선택하고 **다음**을 클릭합니다.
    
    |설정  |값  |
    |---------|---------|
    |이름     |  주문을 추적하는 데 친숙한 이름을 입력합니다. <br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.      |
    |리소스 그룹     |   기존 그룹을 사용하거나 새 그룹을 만듭니다. <br> 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다.         |
    |대상 Azure 지역     | 저장소 계정에 대한 지역을 선택합니다. <br> 자세한 내용은 [지역 가용성](data-box-overview.md#region-availability)을 참조하세요.        |
    |저장소 계정     | 지정된 Azure 지역에 따라 필터링된 기존 저장소 계정 목록에서 하나 이상의 저장소 계정을 선택합니다. Data Box는 최대 10개의 저장소 계정과 연결할 수 있습니다. <br> 새 **범용 v1**, **범용 v2** 또는 **Blob Storage 계정**도 만들 수 있습니다. 규칙이 구성된 스토리지 계정은 사용할 수 없습니다. 스토리지 계정은 fireworks 및 가상 네트워크 섹션의 **모든 네트워크에서 액세스**할 수 있어야 합니다.|
    
7. **배송 주소**에 사용자의 성과 이름, 회사의 이름과 우편 주소 및 유효한 전화 번호를 입력합니다. **주소 확인**을 클릭합니다. 서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다. **다음**을 클릭합니다.

8. **알림 세부 정보**에서 이메일 주소를 지정합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

9. 주문, 연락처, 알림 및 개인 정보 처리 방침과 관련된 정보 **요약**을 검토합니다. 개인 정보 처리 방침에 대한 계약에 해당하는 확인란을 선택합니다.

10. **주문**을 클릭합니다. 주문을 만드는 데 몇 분 정도 걸립니다. 


## <a name="track-the-order"></a>주문 추적

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box 주문, **개요**로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

디바이스를 사용할 수 없는 경우 알림을 받습니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 디바이스를 준비하는 동안 수행되는 작업은 다음과 같습니다.

- 디바이스와 연결된 각 저장소 계정에 대해 SMB 공유가 생성됩니다. 
- 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
- 디바이스의 잠금을 해제하는 데 도움이 되는 디바이스 암호도 생성됩니다. 
- Data Box는 언제든지 디바이스에 대한 권한이 없는 액세스를 방지하기 위해 잠겨 있습니다.

디바이스 준비가 완료되면 포털에서는 해당 주문을 **처리됨** 상태로 표시합니다.

![Data Box 주문 처리됨](media/data-box-overview/data-box-order-status-processed.png)

그런 다음, Microsoft에서는 디바이스를 준비하고 지역 배송업체를 통해 발송합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![Data Box 주문 발송됨](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>주문 취소

이 주문을 취소하려면 Azure Portal에서 **개요**로 이동하고, 명령 모음에서 **취소**를 클릭합니다.

주문이 완료되면 주문 상태가 처리됨으로 표시되기 전까지는 취소할 수 있습니다.
 
취소된 주문을 삭제하려면 **개요**로 이동하고, 명령 모음에서 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box를 배포하기 위한 필수 구성 요소
> * Data Box 주문
> * 주문 추적
> * 주문 취소

Data Box를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box 설정](./data-box-deploy-set-up.md)


