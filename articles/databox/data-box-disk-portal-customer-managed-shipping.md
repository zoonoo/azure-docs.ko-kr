---
title: Microsoft Azure Data Box Disk 자체 관리형 배송 | Microsoft Docs
description: Azure Data Box Disk 디바이스용 자체 관리형 배송 워크플로에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526333"
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
* 인도 (미리 보기)

## <a name="use-self-managed-shipping"></a>자체 관리형 배송 사용

Data Box Disk를 주문할 때 자체 관리형 배송 옵션을 선택할 수 있습니다.

1. Azure Data Box Disk 주문의 **연락처 세부 정보** 에서 **+ 배송 주소 추가** 를 선택합니다.

   ![발송 주소 추가 옵션을 호출 하 여 연락처 정보 단계를 보여 주는 주문 마법사의 스크린샷](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 배송 유형을 선택할 때 **자체 관리형 배송** 옵션을 선택합니다. 이 옵션은 필수 구성 요소에 설명된 대로 지원되는 지역에서만 사용할 수 있습니다.

3. 배송 주소를 제공한 후에는 유효성을 검사 하 고 주문을 완료 해야 합니다.

   ![배송 사용 옵션 및 배송 주소 추가 옵션이 호출된 배송 주소 추가 대화 상자의 스크린샷](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 장치가 준비 되 고 전자 메일 알림을 받으면 픽업을 예약할 수 있습니다. Azure Data Box Disk 주문에서 **개요** 로 이동한 후 **픽업 예약** 을 선택합니다.

   ![Data Box 디바이스 픽업 주문](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. **Azure 픽업 예약** 의 지침을 따릅니다. 인증 코드를 받으려면 [adbops@microsoft.com](mailto:adbops@microsoft.com)으로 메일을 보내 지역 데이터 센터에서 디바이스 픽업 일정을 예약해야 합니다.

   ![픽업 예약](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. 장치 픽업을 예약 하면 **Azure에 대 한 일정 픽업** 에서 권한 부여 코드를 볼 수 있습니다.

   ![픽업 텍스트 상자에 대 한 인증 코드를 호출 하 여 Azure에 대 한 선택 일정 대화 상자 스크린샷](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   이 인증 코드를 기록해 둡니다.

   보안 요구 사항에 따라 선택 일정을 예약 하는 시점에는 선택에 도착할 사용자의 이름을 제공 해야 합니다.

   또한 선택 항목의 데이터 센터로 이동 하는 사용자에 대 한 세부 정보를 제공 해야 합니다. 데이터 센터에서 유효성을 검사 하는 정부 승인 사진을 받아야 합니다.

   장치를 선택 하는 사람에 게는 인증 코드도 있어야 합니다. 권한 부여 코드는 선택 또는 드롭다운에 대해 고유 하며 데이터 센터에서 유효성을 검사 합니다.

7. 데이터 센터에서 장치를 선택 하면 주문이 자동으로 **선택** 된 상태로 전환 됩니다.

   ![선택됨](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. 디바이스를 픽업한 후에 사이트의 Data Box Disk에 데이터를 복사할 수 있습니다. 데이터 복사가 완료되면 Data Box Disk 배송 준비를 진행할 수 있습니다.

   데이터 복사를 완료 한 후 작업에 연결 하 여 해당 드롭다운에 대 한 약속을 예약 합니다. 데이터 센터에 들어오는 사람의 세부 정보를 공유 하 여 디스크를 삭제 해야 합니다. 또한 데이터 센터는 반납 시 인증 코드를 확인해야 합니다. **Schedule drop off** 의 Azure Portal에서 드롭다운에 대 한 권한 부여 코드를 찾을 수 있습니다.

   > [!NOTE]
   > 메일을 통해 인증 코드를 공유하지 마세요. 이는 드롭다운에서 데이터 센터 에서만 확인할 수 있습니다.

9. 드롭다운에 대 한 약속을 받은 후에는 Azure Portal에서 **Azure 데이터 센터에 받을 준비가 완료** 된 상태 여야 합니다.

   ![발송을 사용 하는 옵션을 제공 하는 발송 주소 추가 대화 상자와 전달 주소 추가 옵션의 스크린샷](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. ID 및 인증 코드를 확인 하 고 데이터 센터에서 장치를 삭제 한 후에는 주문 상태를 **받아야** 합니다.

    ![수신 완료](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. 디바이스를 수신하면 데이터 복사가 계속됩니다. 복사가 완료되면 주문이 완료됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Azure Data Box Disk 배포](data-box-disk-quickstart-portal.md)
