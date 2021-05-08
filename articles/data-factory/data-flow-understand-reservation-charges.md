---
title: Azure Data Factory 데이터 흐름에 대한 예약 할인 이해 | Microsoft Docs
description: 실행 중인 ADF 데이터 흐름에 예약 할인이 적용되는 방법을 알아봅니다. 해당 데이터 흐름에는 시간 단위로 할인이 적용됩니다.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716298"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Azure Data Factory 데이터 흐름에 예약 할인이 적용되는 방법

ADF 데이터 흐름 예약된 용량을 구매한 후에는 예약의 컴퓨팅 형식 및 코어 수와 일치하는 Azure 통합 런타임을 사용하여 데이터 흐름에 예약 할인이 자동으로 적용됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 따라서 모든 시간에 대해 일치하는 Azure 통합 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

데이터 흐름에 대한 통합 런타임 사용을 중지하면 지정된 범위에서 일치하는 다른 리소스에 예약 할인이 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="discount-applied-to-adf-data-flows"></a>ADF 데이터 흐름에 적용되는 할인

ADF 데이터 흐름 예약된 용량 할인은 시간 단위로 통합 런타임을 실행하는 데 적용됩니다. 구매한 예약은 데이터 흐름에 대한 통합 런타임에 사용되는 컴퓨팅 유형과 일치합니다. 전체 시간 동안 실행되지 않는 데이터 흐름의 경우 예약 특성과 일치하는 다른 데이터 흐름에 예약이 자동으로 적용됩니다. 할인은 동시에 실행되는 데이터 흐름에도 적용할 수 있습니다. 전체 시간 동안 실행되는 예약 특성과 일치하는 데이터 흐름이 없는 경우 해당 시간 동안 예약 할인의 모든 혜택을 활용할 수 없습니다.

다음 예제에서는 구매한 코어 수 및 실행되는 시기에 따라 ADF 데이터 흐름 예약된 용량 할인이 적용되는 방법을 보여 줍니다.

- 시나리오 1: 메모리 최적화 컴퓨팅 형식의 수량으로 80을 입력하여 메모리 최적화 컴퓨팅 80코어의 1시간에 대한 ADF 데이터 흐름 예약을 구입합니다. 1시간 동안 메모리 최적화된 144코어로 설정된 Azure 통합 런타임을 사용하여 데이터 흐름을 실행합니다. 1시간 동안의 데이터 흐름 사용량인 64코어에 대한 종량제 가격이 청구되었습니다. 메모리 최적화 사용량의 80코어 1시간에 대한 예약 할인이 제공됩니다.
- 시나리오 2: 범용 컴퓨팅 형식의 수량으로 32를 입력하여 범용 컴퓨팅의 1시간 32코어에 대한 ADF 데이터 흐름 예약을 구매합니다. 일반 컴퓨팅 Azure 통합 런타임의 32코어를 사용한 1시간 동안 데이터 흐름을 디버그합니다. 사용량의 전체 시간에 대해 예약 할인이 제공됩니다.

청구 사용량 보고서에서 Azure 예약의 애플리케이션을 이해하고 보려면 [Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참고하세요.

- [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)