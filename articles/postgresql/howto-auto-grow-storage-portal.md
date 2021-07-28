---
title: 자동 증가 스토리지 - Azure Portal - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL - 단일 서버에서 Azure Portal을 사용하여 스토리지 자동 증가를 구성하는 방법을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366133"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL에서 Azure Portal을 사용하는 자동 증가 스토리지 - 단일 서버
이 문서에서는 워크로드에 영향을 주지 않고 확장되도록 Azure Database for PostgreSQL 서버 스토리지를 구성하는 방법을 설명합니다.

서버가 할당된 스토리지 제한에 도달하면 서버가 읽기 전용으로 표시됩니다. 그러나 스토리지 자동 증가를 사용하도록 설정하면 증가하는 데이터에 맞게 서버 스토리지가 늘어납니다. 프로비전된 스토리지가 100GB 미만인 서버의 경우 프로비전된 스토리지 크기는 사용 가능한 스토리지가 1GB보다 크거나 프로비전된 스토리지의 10%보다 적으면 즉시 5GB씩 증가합니다. 프로비전된 스토리지가 100GB를 초과하는 서버의 경우, 사용 가능한 스토리지 공간이 10GB의 프로비전된 스토리지 크기 미만이면 프로비전된 스토리지 크기가 5%씩 증가합니다. [여기](./concepts-pricing-tiers.md#storage)에 지정된 대로 최대 스토리지 제한이 적용됩니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [PostgreSQL용 Azure Database 서버](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>스토리지 자동 증가 사용 

다음 단계에 따라 PostgreSQL 서버 스토리지 자동 증가를 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for PostgreSQL 서버를 선택합니다.

2. PostgreSQL 서버 페이지의 **설정** 아래에서 **가격 책정 계층** 을 클릭하여 가격 책정 계층 페이지를 엽니다.

3. **자동 증가** 섹션에서 **예** 를 선택하여 스토리지 자동 증가를 사용하도록 설정합니다.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL - Settings_Pricing_tier - 자동 증가":::

4. **확인** 을 클릭하여 변경 내용을 저장합니다.

5. 자동 증가가 성공적으로 활성화되었음을 알리는 알림이 표시됩니다.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL - 자동 증가 성공":::

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.
