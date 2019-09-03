---
title: Azure Data Box 반송 자습서 | Microsoft Docs
description: Azure Data Box를 Microsoft에 배송하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.openlocfilehash: 368439d6e15d6c94bbb96d67fcb48ab006234c95
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098845"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>자습서: Azure Data Box 반송 및 Azure에 대한 데이터 업로드 확인

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box 반환 및 Azure에 대한 데이터 업로드 확인

::: zone-end

::: zone target="docs"

이 자습서에서는 Azure Data Box를 반송하고 Azure에 업로드된 데이터를 확인하는 방법을 설명합니다.

이 자습서에서는 다음과 같은 주제에 대해 학습합니다.

> [!div class="checklist"]
> * 필수 조건
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box에서 데이터 지우기

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

- [자습서: Azure Data Box에 데이터 복사 및 확인](data-box-deploy-copy-data.md)을 완료했는지 확인합니다. 
- 복사 작업이 완료되었습니다. 복사 작업이 진행 중이면 배송 준비를 실행할 수 없습니다.

## <a name="prepare-to-ship"></a>배송 준비

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

데이터 복사가 완료되면 디바이스를 준비하고 배송합니다. 디바이스가 Azure 데이터 센터에 도달하면 데이터가 자동으로 Azure에 업로드됩니다.

## <a name="prepare-to-ship"></a>배송 준비

배송을 준비하기 전에 복사 작업이 완료되었는지 확인합니다.

1. 로컬 웹 UI에서 **배송 준비** 페이지로 이동하여 배송 준비를 시작합니다. 
2. 로컬 웹 UI에서 디바이스를 끕니다. 디바이스에서 케이블을 뽑습니다. 

다음 단계는 디바이스를 반송하는 위치에 따라 결정됩니다.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box 반송

디바이스로 데이터 복사가 완료되고 **배송 준비** 실행이 성공했는지 확인합니다. 디바이스를 배송하는 지역에 따라 절차가 다릅니다.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>미국, 캐나다, 유럽에서 배송

미국, 캐나다 또는 유럽에서 디바이스를 반송하는 경우 다음 단계를 수행합니다.

1. 디바이스의 전원이 꺼져 있고 케이블이 분리되었는지 확인합니다. 
2. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
3. 배송 레이블이 E-ink 디스플레이에 표시되는지 확인하고 운송업체에 픽업을 예약합니다. 레이블이 손상 또는 손실되었거나 E-ink 디스플레이에 표시되지 않으면 Microsoft 지원에 문의하세요. 고객 지원팀에서 제안하는 경우 Azure Portal에서 **개요 > 배송 레이블 다운로드**로 차례로 이동합니다. 배송 레이블을 다운로드하여 디바이스에 부착합니다. 
4. 디바이스를 반송하는 경우 UPS로 픽업을 예약합니다. 픽업을 예약하려면 다음을 수행합니다.

    - 로컬 UPS(국가/지역별 무료 전화 번호)에 전화합니다.
    - 통화에서 E-ink 디스플레이 또는 인쇄된 레이블에서 표시한 대로 역방향 배송 추적 번호를 알려줍니다.
    - 추적 번호를 알려주지 않으면 픽업 시 UPS에서 추가 요금을 지불하도록 요구합니다.

    픽업을 예약하는 대신 가장 가까운 반납 위치에 Data Box를 반납할 수도 있습니다.
4. 운송업체에서 Data Box를 픽업하고 나면 포털의 주문 상태가 **픽업됨**으로 업데이트됩니다. 추적 ID도 표시됩니다.


## <a name="ship-in-australia"></a>오스트레일리아에서 배송

오스트레일리아의 Azure 데이터 센터에는 추가 보안 알림이 있습니다. 모든 인바운드 배송에는 고급 알림이 있어야 합니다. 오스트레일리아에서 배송하려면 다음 단계를 수행합니다.


1. 반품 발송을 위해 디바이스를 배송하는 데 사용된 원래 상자를 보관하세요.
2. 디바이스로 데이터 복사가 완료되고 **배송 준비** 실행이 성공했는지 확인합니다.
3. 디바이스의 전원을 끄고 케이블을 분리합니다.
4. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
5. Quantium Solutions에 픽업을 요청하는 이메일을 전송합니다. Azure Portal에 지정된 서비스 참조 번호를 참조합니다. 다음 이메일 템플릿을 사용합니다. *TAU 코드가 있는 역방향 배송 레이블 요청* 이메일에 포함되는 세부 정보는 다음과 같습니다. 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions 오스트레일리아가 반송 레이블을 이메일로 보내드립니다.
7. 반송 레이블을 인쇄하여 배송 상자에 부착합니다.
8. 택배 회사에 패키지를 전달합니다.

필요한 경우 Quantium Solutions 지원에 이메일(Azure@quantiumsolutions.com)을 보내거나 전화를 걸 수 있습니다.

전화 주문 관련 문의의 경우:

- 먼저 픽업을 위한 이메일을 보냅니다.
- 전화 상으로 주문 이름을 제공합니다.

## <a name="ship-in-japan"></a>일본에서 배송 

1. 반품 발송을 위해 디바이스를 배송하는 데 사용된 원래 상자를 보관하세요.
2. 디바이스의 전원을 끄고 케이블을 분리합니다.
3. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
4. 탁송 운송장에 회사 이름과 주소 정보를 보낸 사람 정보로 작성합니다.
5. 다음 이메일 템플릿을 사용하여 Quantium Solutions에 이메일을 전송합니다.

    - Japan Post Chakubarai 탁송 운송장이 포함되지 않거나 누락된 경우 이를 이메일에서 언급해야 합니다. Quantium Solutions Japan은 Japan Post에 픽업 시 탁송 운송장을 가져올 것을 요청합니다.
    - 주문이 여러 개인 경우에는 개별 픽업을 보장하도록 이메일을 보냅니다.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. 픽업을 예약한 후 Quantium Solutions에서 이메일 확인을 수신합니다. 이메일 확인에는 Chakubarai 탁송 운송장에 대한 정보도 포함되어 있습니다.

필요한 경우 다음 정보로 Quantium Solutions 지원(일본어)에 문의할 수 있습니다. 

- 이메일: Customerservice.JP@quantiumsolutions.com 
- 전화: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

Microsoft에서 디바이스를 수신하고 검사하면 주문 상태가 **수신됨**으로 업데이트됩니다. 디바이스에 손상이나 변조 흔적이 있는지 물리적으로 확인됩니다.

확인이 완료되면 Data Box가 Azure 데이터 센터의 네트워크에 연결됩니다. 데이터 복사가 자동으로 시작됩니다. 데이터 크기에 따라 복사 작업을 완료하는 데 몇 시간에서 몇 일이 걸릴 수 있습니다. 포털에서 복사 작업 진행 상황을 모니터링할 수 있습니다.

복사가 완료되면 주문 상태는 **완료됨**으로 업데이트됩니다.

원본에서 데이터를 삭제하기 전에 데이터가 Azure에 업로드되었는지 확인합니다. 데이터는 다음 위치에 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사할 때, 데이터 형식에 따라 Azure Storage 계정에서 다음 경로 중 하나에 업로드됩니다.

  - 블록 Blob 및 페이지 Blob의 경우: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files의 경우: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    또는 Azure Portal에서 Azure storage 계정으로 이동하여 찾아볼 수도 있습니다.

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문을 만들 때 지정된 리소스 그룹에 연결됩니다. 

    - Azure에서 관리 디스크에 성공적으로 복사되면 Azure Portal의 **주문 세부 정보**로 이동하여 관리 디스크에 대해 지정된 리소스 그룹을 기록할 수 있습니다.

        ![관리 디스크 리소스 그룹 식별](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        기록한 리소스 그룹으로 이동하여 관리 디스크를 찾습니다.

        ![리소스 그룹에 연결된 관리 디스크](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - VHDX 또는 동적/차이점 보관용 VHD를 복사한 경우 VHD/VHD가 준비 스토리지 계정에 페이지 Blob으로 업로드되지만 VHD를 관리 디스크로 변환하지 못합니다. 준비 **스토리지 계정 > Blob**으로 차례로 이동한 다음, 적절한 컨테이너(표준 SSD, 표준 HDD 또는 표준 HDD)를 선택합니다. VHD는 준비 스토리지 계정에 페이지 Blob으로 업로드됩니다.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone target="docs"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 필수 조건
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box에서 데이터 지우기

다음 문서로 이동하여 로컬 웹 UI를 통해 Data Box를 관리하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Data Box 관리](./data-box-local-web-ui-admin.md)

::: zone-end


