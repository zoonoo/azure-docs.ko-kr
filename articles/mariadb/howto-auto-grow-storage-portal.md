---
title: 자동으로 Azure portal을 사용 하 여 MariaDB에 대 한 Azure Database의 저장소 증가
description: 이 문서에서는 자동 설정 하는 방법에 대해 설명 합니다. Azure portal을 사용 하 여 MariaDB에 대 한 Azure Database에 대 한 저장소 증가
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: bb3291b66776a5f0f6be16069b2d6a999b2d1f32
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676880"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>자동으로 Azure portal을 사용 하 여 MariaDB에 대 한 Azure Database의 저장소 증가
이 문서에서는 Azure Database for MariaDB 서버 저장소 워크 로드에 영향을 주는 없이 증가 하도록 구성 하는 방법을 설명 합니다.

서버에서 할당 된 저장소 용량 한도 도달 하면 서버는 읽기 전용으로 표시 됩니다. 그러나 사용 하도록 설정 하면 저장소 자동으로 증가 하면 서버 저장소 증가 하는 데이터에 맞게 증가 합니다. 100GB 프로 비전 된 저장소 보다 더 적은 노력으로 서버에 대 한 사용 가능한 메모리를 1GB 또는 프로 비전된 된 저장소의 10% 중 더 큰 아래는 즉시 프로 비전 된 저장소 크기를 5GB 만큼 늘어납니다. 100GB 이상 프로 비전 된 저장소를 사용 하 여 서버에 대 한 사용 가능한 저장소 공간 프로 비전 된 저장소 크기의 5% 미만인 경우 프로 비전 된 저장소 크기가 5% 증가 합니다. 지정 된 대로 최대 저장소 제한 [여기](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) 적용 합니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for MariaDB 서버](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>사용 저장소 자동 증가 

MariaDB 서버 저장소 자동 증가 설정 하려면 다음이 단계를 수행 합니다.

1. 에 [Azure portal](https://portal.azure.com/), MariaDB 서버용 Azure Database를 선택 합니다.

2. MariaDB 서버가 페이지에서 아래 **설정을** 머리글을 클릭 **가격 책정 계층** 가격 책정 계층 페이지를 엽니다.

3. 자동 증가 섹션에서 선택 **예** 저장소 자동 증가가 사용 하도록 설정 합니다.

    ![Azure Database for MariaDB-Settings_Pricing_tier-자동 증가](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. **확인** 을 클릭하여 변경 내용을 저장합니다.

5. 알림을 해당 자동 증가가 확인은 성공적으로 설정 합니다.

    ![Azure Database for MariaDB-자동 증가 성공](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 [메트릭에 대해 경고를 생성 하는 방법을](howto-alert-metric.md)합니다.
