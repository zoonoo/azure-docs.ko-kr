---
title: Azure App Service 격리 된 스탬프에 예약 할인이 적용 되는 방법
description: 예약 할인이 Azure App Service 격리 된 스탬프에 적용 되는 방법에 대해 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298257"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Azure App Service 격리 된 스탬프에 예약 할인이 적용 되는 방법

App Service 격리 스탬프 요금 예약 용량을 구입 하 고 나면 예약 할인이 지역의 스탬프 요금에 자동으로 적용 됩니다. 예약 할인은 격리 된 스탬프 요금 측정기에서 내보낸 사용량에 적용 됩니다. 작업자, 추가 프런트 엔드 및 스탬프와 연결 된 다른 모든 리소스는 계속 해 서 정기적으로 청구 됩니다.

## <a name="reservation-discount-application"></a>예약 할인 응용 프로그램

App Service 격리 스탬프 요금 할인은 시간 단위로 격리 된 스탬프를 실행 하는 데 적용 됩니다. 한 시간 동안 배포 된 스탬프가 없는 경우 해당 시간 동안 예약 된 용량이 낭비 됩니다. 이 함수는 다루지 않습니다.

구매 후 구매한 예약은 특정 지역에서 실행 되는 격리 된 스탬프와 일치 합니다. 해당 스탬프를 종료 하면 예약 할인이 지역에서 실행 되는 다른 스탬프에 자동으로 적용 됩니다. 스탬프가 없는 경우 해당 지역에 생성 되는 다음 스탬프에 예약이 적용 됩니다.

스탬프가 전체 시간 동안 실행 되지 않으면 동일한 시간 동안 동일한 지역의 다른 일치 하는 스탬프에 예약이 자동으로 적용 됩니다.

## <a name="choose-a-stamp-type---windows-or-linux"></a>스탬프 유형 선택-Windows 또는 Linux

비어 있는 격리 된 스탬프는 기본적으로 Windows 스탬프 측정기를 내보냅니다. 예를 들어, 작업 자가 배포 되지 않은 경우입니다. Windows 작업자를 배포할 때 계속 해 서 측정기를 내보냅니다. Linux 작업자를 배포 하는 경우 측정기는 Linux 스탬프 측정기로 변경 됩니다. 스탬프는 Linux 및 Windows 작업자를 모두 배포할 때 Windows 측정기를 내보냅니다.

그 결과 스탬프 측정기는 스탬프 수명 동안 Windows와 Linux 사이에서 변경 될 수 있습니다. 한편, 예약은 운영 체제 마다 다릅니다. 스탬프에 배포 하려는 작업자를 지 원하는 예약을 구매 해야 합니다. Windows 전용 스탬프 및 혼합 스탬프로는 Windows 예약이 사용 됩니다. Linux 작업자만 포함 된 스탬프는 Linux 예약을 사용 합니다.

Linux 예약을 구입 해야 하는 유일한 시간은 스탬프에 Linux 작업자 _만_ 있는 경우입니다.

## <a name="discount-examples"></a>할인 예

다음 예에서는 배포에 따라 격리 된 스탬프 요금 예약 인스턴스 할인이 적용 되는 방법을 보여 줍니다.

- **예 1**: App Service 격리 스탬프가 없는 지역에 격리 된 예약 된 스탬프 용량의 인스턴스를 하나 구매 합니다. 영역에 새 스탬프를 배포 하 고 해당 스탬프에 대해 예약 요금을 지불 합니다.
- **예제 2**: 이미 App Service 격리 스탬프가 배포 된 지역에서 격리 된 예약 된 스탬프 용량의 인스턴스 하나를 구입할 수 있습니다. 배포 된 스탬프의 예약 된 요금 수신을 시작 합니다.
- **예 3**: App Service 격리 스탬프가 이미 배포 된 지역에서 격리 된 예약 된 스탬프 용량의 인스턴스 하나를 구입할 수 있습니다. 배포 된 스탬프에 대해 예약 된 요금 수신을 시작 합니다. 나중에 스탬프를 삭제 하 고 새 스탬프를 배포 합니다. 새 스탬프의 예약 된 비율이 표시 됩니다. 배포 된 스탬프가 없는 기간 동안에는 할인이 수행 하지 않습니다.
- **예 4**: 한 지역에서 격리 된 Linux 예약 된 스탬프 용량의 인스턴스 하나를 구매한 후 영역에 새 스탬프를 배포 합니다. 처음에는 타임 스탬프를 작업자 없이 배포할 때 Windows 스탬프 측정기를 내보냅니다. 할인이 수신 되지 않습니다. 첫 번째 Linux 작업자는 스탬프를 배포할 때 Linux 스탬프 측정기를 내보내고 예약 할인이 적용 됩니다. Windows 작업자를 나중에 스탬프에 배포 하는 경우 스탬프 측정기가 Windows로 되돌아갑니다. 격리 된 Linux 예약 된 스탬프 예약에 대 한 할인을 더 이상 받지 않습니다.

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.
- 비용 절감을 위해 사전 구매 App Service 격리 스탬프 예약 된 용량에 대 한 자세한 내용은 [예약 된 용량을 사용 하 여 선불 for Azure App Service 격리 된 스탬프 요금](billing-prepay-app-service-isolated-stamp.md)을 참조 하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure Reservations란?](billing-save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](billing-manage-reserved-vm-instance.md)
  - [종 량 제 요금으로 구독에 대 한 예약 사용 이해](billing-understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
