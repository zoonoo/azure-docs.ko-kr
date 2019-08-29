---
title: Azure Portal를 사용 하 여 Azure Database for MariaDB의 저장소 자동 증가
description: 이 문서에서는를 사용 하 여 Azure Database for MariaDB에 대 한 저장소 자동 증가를 사용 하도록 설정할 수 있는 방법을 설명 Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9d7b126b5c07cc8661cf50b4c4e13f78592b118e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136716"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MariaDB의 저장소 자동 증가
이 문서에서는 워크 로드에 영향을 주지 않고 확장 되도록 Azure Database for MariaDB server 저장소를 구성 하는 방법을 설명 합니다.

서버가 할당 된 저장소 제한에 도달 하면 서버가 읽기 전용으로 표시 됩니다. 그러나 저장소 자동 증가를 사용 하도록 설정 하면 증가 하는 데이터에 맞게 서버 저장소가 늘어납니다. 프로 비전 된 저장소 수가 100 미만인 서버의 경우, 사용 가능한 저장소가 프로 비전 된 저장소의 1gb 또는 10%를 초과 하는 즉시 프로 비전 된 저장소 크기는 5gb 씩 증가 합니다. 프로 비전 된 저장소 수가 100 GB를 넘는 서버에서는 사용 가능한 저장소 공간이 프로 비전 된 저장소 크기의 5% 미만이 면 프로 비전 된 저장소 크기가 5% 증가 합니다. [여기](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) 에 지정 된 대로 최대 저장소 제한이 적용 됩니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for MariaDB 서버](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>저장소 자동 증가 사용 

다음 단계를 수행 하 여 MariaDB 서버 저장소 자동 증가를 설정 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MariaDB 서버를 선택 합니다.

2. MariaDB 서버 페이지의 **설정** 머리글 아래에서 **가격 책정 계층** 을 클릭 하 여 가격 책정 계층 페이지를 엽니다.

3. 자동 증가 섹션에서 **예** 를 선택 하 여 저장소 자동 증가를 사용 하도록 설정 합니다.

    ![Azure Database for MariaDB Settings_Pricing_tier-자동 증가](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. **확인** 을 클릭하여 변경 내용을 저장합니다.

5. 자동 증가가 성공적으로 설정 되었음을 알리는 알림이 나타납니다.

    ![Azure Database for MariaDB-자동 증가 성공](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>다음 단계

[메트릭에 대 한 경고를 만드는 방법](howto-alert-metric.md)에 대해 알아봅니다.
