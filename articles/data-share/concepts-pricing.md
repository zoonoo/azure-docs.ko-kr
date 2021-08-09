---
title: Azure Data Share 가격 책정 이해
description: Azure Data Share 가격 책정 방식 알아보기
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88136924"
---
# <a name="understand-azure-data-share-pricing"></a>Azure Data Share 가격 책정 이해

스냅샷 기반 공유의 경우 Azure Data Share는 데이터 세트 스냅샷 및 스냅샷 실행에 대해 요금을 부과합니다. 이 문서에서는 스냅샷 기록 정보를 사용하여 데이터 세트 스냅샷 및 스냅샷 실행을 추정하는 방법을 설명합니다. 현재 데이터 제공자는 데이터 세트 스냅샷 및 스냅샷 실행에 대해 청구됩니다.

## <a name="dataset-snapshot"></a>데이터 세트 스냅샷

데이터 세트 스냅샷은 데이터 세트를 원본에서 대상으로 이동하는 작업입니다. 공유에 대한 스냅샷을 만들 때 공유 내 각 데이터 세트의 스냅샷은 데이터 세트 스냅샷 작업입니다. 데이터 세트 스냅샷 목록을 보려면 아래 단계를 따르세요. 

1. Azure Portal에서 Azure Data Share 리소스로 이동합니다.

1. 보낸 공유 또는 받은 공유에서 공유를 선택합니다.

1. **기록** 탭을 클릭합니다.

1. 스냅샷의 시작 시간을 클릭합니다.
 
    ![스냅샷 기록](./media/concepts/concepts-pricing/pricing-snapshot-history.png "스냅샷 기록") 

1. 데이터 세트 스냅샷 작업 목록을 봅니다. 각 줄 항목은 데이터 세트 스냅샷 작업에 해당합니다. 이 예에는 두 개의 데이터 세트 스냅샷이 있습니다.

    ![데이터 세트 스냅샷 작업](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "데이터 세트 스냅샷 작업")

## <a name="snapshot-execution"></a>스냅샷 실행

스냅샷 실행에는 소스에서 대상으로 데이터 세트를 이동하는 데 필요한 리소스가 포함됩니다. 각 데이터 세트 스냅샷 작업에 대해 스냅샷 실행은 vCore 수에 스냅샷 기간을 곱한 값으로 계산됩니다. 요금은 분 단위로 일할 계산되어 반올림됩니다. vCore의 수는 원본-대상 쌍 및 데이터 패턴을 기반으로 동적으로 선택됩니다. 데이터 세트 스냅샷 작업에 사용된 스냅샷 시작 시간, 종료 시간 및 vCore를 보려면 아래 단계를 따르세요.

1. Azure Portal에서 Azure Data Share 리소스로 이동합니다.

1. 보낸 공유 또는 받은 공유에서 공유를 선택합니다.

1. **기록** 탭을 클릭합니다.

1. 스냅샷의 시작 시간을 클릭합니다.

    ![스냅샷 기록](./media/concepts/concepts-pricing/pricing-snapshot-history.png "스냅샷 기록") 

1. 데이터 세트 스냅샷 작업의 상태를 클릭합니다.

    ![데이터 세트 스냅샷 상태](./media/concepts/concepts-pricing/pricing-snapshot-status.png "데이터 세트 스냅샷 상태")

1. 이 데이터 세트 스냅샷 작업에 사용된 시작 시간, 종료 시간 및 vCore를 봅니다. 

    ![스냅샷 실행](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "스냅샷 실행")

## <a name="next-steps"></a>다음 단계

- 최신 가격 정보 받기 - [Azure Data Share 가격 책정](https://azure.microsoft.com/pricing/details/data-share/)
- Azure 가격 계산기를 사용하여 비용 추정 - [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)
