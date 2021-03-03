---
title: Azure Data Factory 데이터 흐름에 대 한 예약 할인 이해 | Microsoft Docs
description: ADF 데이터 흐름을 실행 하는 데 예약 할인이 적용 되는 방법에 대해 알아봅니다. 할인은 시간 단위로 이러한 데이터 흐름에 적용 됩니다.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716298"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Azure Data Factory 데이터 흐름에 예약 할인이 적용 되는 방법

ADF 데이터 흐름 예약 용량을 구입한 후에는 예약의 계산 유형 및 코어 수와 일치 하는 Azure integration runtime을 사용 하 여 데이터 흐름에 예약 할인이 자동으로 적용 됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 따라서 어떤 시간에도 일치 하는 Azure 통합 리소스를 사용 하지 않는 경우 해당 시간의 예약 수량이 손실 됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

데이터 흐름에 대 한 통합 런타임 사용을 중지 하면 예약 할인이 지정 된 범위에서 일치 하는 다른 리소스에 자동으로 적용 됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="discount-applied-to-adf-data-flows"></a>ADF 데이터 흐름에 적용 되는 할인

ADF 데이터 흐름 예약 된 용량 할인은 시간별로 통합 런타임을 실행 하는 데 적용 됩니다. 구매한 예약은 데이터 흐름에 대 한 통합 런타임에 사용 되는 계산 유형과 일치 합니다. 전체 시간을 실행 하지 않는 데이터 흐름의 경우 예약이 예약 특성과 일치 하는 다른 데이터 흐름에 자동으로 적용 됩니다. 할인은 동시에 실행 되는 데이터 흐름에도 적용할 수 있습니다. 예약 특성과 일치 하는 전체 시간 동안 실행 되는 데이터 흐름이 없는 경우 해당 시간에 대 한 예약 할인율의 모든 혜택을 받을 수 없습니다.

다음 예에서는 구매한 코어 수와 실행 시점에 따라 ADF 데이터 흐름 예약 용량 할인이 적용 되는 방법을 보여 줍니다.

- 시나리오 1: 메모리 최적화 계산 형식에 대 한 수량으로 80을 입력 하 여 메모리 최적화 계산의 1 시간 80 코어에 대 한 ADF 데이터 흐름 예약을 구입 합니다. Azure integration runtime을 사용 하 여 데이터 흐름을 실행 하 고 1 시간 동안 최적화 된 메모리의 144 코어로 설정 합니다. 1 시간 동안 데이터 흐름 사용의 64 코어에 대 한 종 량 제 가격이 청구 됩니다. 메모리 액세스에 최적화 된 사용의 1 시간 80 코어에 대 한 예약 할인이 제공 됩니다.
- 시나리오 2: 일반 용도의 계산 형식의 수량으로 32을 입력 하 여 범용 계산의 1 시간 32 코어에 대 한 ADF 데이터 흐름 예약을 구매 합니다. 일반 계산 Azure 통합 런타임의 32 코어를 사용 하 여 1 시간 동안 데이터 흐름을 디버그할 수 있습니다. 전체 사용 시간에 대 한 예약 할인을 받습니다.

청구 사용량 보고서에서 Azure 예약의 애플리케이션을 이해하고 보려면 [Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure Reservations에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)