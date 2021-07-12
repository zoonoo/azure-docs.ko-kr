---
title: Microsoft Azure Data Box Disk 자체 관리형 배송 | Microsoft Docs
description: Azure Data Box Disk 디바이스용 자체 관리형 배송 워크플로에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/08/2021
ms.author: alkohli
ms.openlocfilehash: 6323a077596a7ed13384bfbaace929b270c2d3f5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109754482"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Azure Portal에서 Azure Data Box Disk용 자체 관리형 배송 사용

이 문서에서는 Azure Data Box Disk를 주문, 픽업 및 반납하기 위한 자체 관리형 배송 작업에 대해 설명합니다. Azure Portal을 사용하여 Data Box Disk를 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Data Box Disk 주문](data-box-disk-deploy-ordered.md) 시 자체 관리형 배송을 옵션으로 사용할 수 있습니다. 자체 관리형 배송은 다음 지역에서만 사용할 수 있습니다.

* 미국 정부
* 영국
* 서부 유럽
* 오스트레일리아
* 일본
* 싱가포르
* 대한민국
* 남아프리카 공화국
* 인도(미리 보기)
* 브라질

## <a name="use-self-managed-shipping"></a>자체 관리형 배송 사용

Data Box Disk를 주문할 때 자체 관리형 배송 옵션을 선택할 수 있습니다.

1. Azure Data Box Disk 주문의 **연락처 세부 정보** 에서 **+ 배송 주소 추가** 를 선택합니다.

   ![배송 주소 추가 옵션이 호출된 연락처 세부 정보 단계를 보여주는 주문 마법사의 스크린샷.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 배송 유형을 선택할 때 **자체 관리형 배송** 옵션을 선택합니다. 이 옵션은 필수 구성 요소에 설명된 대로 지원되는 지역에서만 사용할 수 있습니다.

3. 배송 주소를 입력한 후 유효한지 검사하고 주문을 완료해야 합니다.

   ![배송 사용 옵션 및 배송 주소 추가 옵션이 호출된 배송 주소 추가 대화 상자의 스크린샷.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 디바이스가 준비되었다는 이메일 알림을 받으면 픽업을 예약할 수 있습니다. Azure Data Box Disk 주문에서 **개요** 로 이동한 후 **픽업 예약** 을 선택합니다.

   ![Data Box 디바이스 픽업 주문](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. **Azure 픽업 예약** 의 지침을 따릅니다. 인증 코드를 받으려면 [adbops@microsoft.com](mailto:adbops@microsoft.com)으로 메일을 보내 지역 데이터 센터에서 디바이스 픽업 일정을 예약해야 합니다.

   ![픽업 예약](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

   **브라질에 대한 지침:** 브라질에서 디바이스 픽업을 예약하는 경우 이메일에 다음 정보를 포함합니다. 데이터 센터는 인바운드 `Nota Fiscal`을 받은 후 픽업을 예약하며, 영업일 기준으로 최대 4일이 소요될 수 있습니다.

   ```
   Subject: Request Azure Data Box Disk pickup for order: <ordername>

   - Order name
   - Company name
   - Company legal name (if different) 
   - Tax ID 
   - Address 
   - Country 
   - Phone number 
   - Contact name of the person who will pick up the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.)   
   ```

6. 디바이스 픽업을 예약한 후에는 **Azure 픽업 예약** 에서 인증 코드를 볼 수 있습니다.

   ![픽업 인증 코드 텍스트 상자가 표시된 Azure 픽업 예약 대화 상자의 스크린샷.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   이 인증 코드를 적어둡니다. 디바이스를 픽업하는 사람이 코드를 가지고 있어야 합니다.

   보안 요구 사항에 따라 픽업 일정을 예약할 때 픽업하러 올 사용자의 이름과 세부 정보를 제공해야 합니다. 본인 또는 연락 담당자에게 데이터 센터에서 유효성을 검사할 정부 승인 사진 ID가 있어야 합니다.

7. 예약된 시간에 데이터 센터에서 Data Box Disk를 픽업합니다.

   디바이스를 픽업하는 사람은 다음을 제공해야 합니다.

   * Microsoft Operations의 데이터 센터를 방문하기 위한 이메일 확인 사본.

   * 인증 코드. 픽업 또는 반납을 위한 고유한 참조 번호이며 데이터 센터에서 확인됩니다.

   * 정부 승인 사진 ID. ID는 데이터 센터에서 확인되며, 픽업 예약 시 디바이스를 픽업하는 사람의 이름과 세부 정보를 제공해야 합니다.

   > [!NOTE]
   > 예약된 약속을 놓치면 새 약속을 예약해야 합니다.

8. 데이터 센터에서 디바이스를 픽업하면 주문이 자동으로 **픽업됨** 상태로 바뀝니다.

   ![선택됨](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

9. 디바이스를 픽업한 후에 사이트의 Data Box Disk에 데이터를 복사할 수 있습니다. 데이터 복사가 완료되면 Data Box Disk 배송 준비를 진행할 수 있습니다.

   데이터 복사를 완료한 후 Operations에 문의하여 반납 약속을 예약합니다. 디스크를 반납하기 위해 데이터 센터에 오는 사람의 세부 정보를 공유해야 합니다. 또한 데이터 센터는 반납 시 인증 코드를 확인해야 합니다. 반납 인증 코드는 Azure Portal의 **반납 예약** 에서 찾을 수 있습니다.

   > [!NOTE]
   > 메일을 통해 인증 코드를 공유하지 마세요. 인증 코드는 반납 시 데이터 센터에서만 확인할 수 있습니다.

   **브라질에 대한 지침:** 브라질에서 디바이스 반납을 예약하는 경우 다음 정보를 포함하여 [adbops@microsoft.com](mailto:adbops@microsoft.com)으로 이메일을 보냅니다.

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. 반납 약속을 받은 후, Azure Portal에서 주문은 **Azure 데이터 센터에서 수신 준비 완료** 상태가 됩니다.

    ![배송 사용 옵션 및 배송 주소 추가 옵션이 호출된 배송 주소 추가 대화 상자의 스크린샷.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

11. ID 및 인증 코드를 확인하고 데이터 센터에서 디바이스를 반납한 후에는 주문 상태가 **수신됨** 이 됩니다.

    ![수신 완료](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. 디바이스를 수신하면 데이터 복사가 계속됩니다. 복사가 완료되면 주문이 완료됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Azure Data Box Disk 배포](data-box-disk-quickstart-portal.md)
