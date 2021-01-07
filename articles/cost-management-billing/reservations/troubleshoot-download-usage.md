---
title: Azure 예약 다운로드 사용 세부 정보 문제 해결
description: 이 문서는 Azure Portal에서 예약 인스턴스 사용 세부 정보 다운로드를 사용할 수 없는 이유를 이해하고 문제를 해결하는 데 도움을 줍니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147340"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Azure 예약 다운로드 사용 세부 정보 문제 해결

이 문서는 Azure Portal에서 예약 인스턴스 사용 세부 정보 다운로드를 사용할 수 없는 이유를 이해하고 문제를 해결하는 데 도움을 줍니다.

## <a name="symptoms"></a>증상

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, **예약** 으로 이동합니다.
1. 예약을 선택합니다.
1. 예약 개요 페이지에서 기본 보기는 지난 7일 동안의 사용량을 보여줍니다. **CSV로 다운로드** 를 선택하여 예약의 사용량 세부 정보를 다운로드할 수 있습니다.
1. 시간 범위를 변경하면 **CSV로 다운로드** 옵션을 사용할 수 없게 됩니다.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="CSV로 다운로드를 사용할 수 없음을 보여주는 예제" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>원인

기술적 제약으로 인해 Azure는 7일을 초과하는 기간 동안 데이터 다운로드를 지원하지 않습니다. 많은 양의 예약을 한 고객의 긴 기간은 많은 양의 데이터를 생성합니다. API를 통해 데이터를 반환하면 Azure 리소스에 부담이 됩니다.

## <a name="solution"></a>해결 방법

고객이 더 오랜 기간 동안 데이터를 다운로드하기를 원한다는 것을 알고 있습니다. 하지만, 현재 장기간 예약 사용량 데이터를 다운로드할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

- 예약에 대한 자세한 내용은 [Azure Reservations란?](save-compute-costs-reservations.md)을 참조하세요.