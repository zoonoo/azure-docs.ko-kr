---
title: 자동 증가 저장소 - Azure 포털 - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 Azure 포털을 사용하여 MariaDB용 Azure 데이터베이스에 대한 자동 증가 저장소를 사용하도록 설정하는 방법에 대해 설명합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528900"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure 포털을 사용하여 MariaDB용 Azure 데이터베이스의 저장소 자동 증가
이 문서에서는 워크로드에 영향을 주지 않고 AzureDB 서버 저장소를 증가하도록 Azure Database를 구성하는 방법에 대해 설명합니다.

서버가 할당된 저장소 제한에 도달하면 서버는 읽기 전용으로 표시됩니다. 그러나 저장소 자동 증가를 사용하도록 설정하면 증가하는 데이터를 수용하기 위해 서버 저장소가 증가합니다. 100GB 미만의 프로비저닝된 저장소가 있는 서버의 경우 무료 저장소가 프로비저닝된 저장소의 1GB 또는 10% 미만이면 프로비저닝된 저장소 크기가 5GB 증가합니다. 프로비저닝된 저장소가 100GB를 초과하는 서버의 경우 여유 저장소 공간이 프로비저닝된 저장소 크기의 5% 미만일 때 프로비저닝된 저장소 크기가 5% 증가합니다. [여기에](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) 명시된 최대 저장소 제한이 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [MariaDB 서버에 대한 Azure 데이터베이스](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>스토리지 자동 성장 지원 

다음 단계에 따라 MariaDB 서버 저장소 자동 성장을 설정합니다.

1. Azure [포털에서](https://portal.azure.com/)MariaDB 서버에 대한 기존 Azure 데이터베이스를 선택합니다.

2. MariaDB 서버 페이지에서 **설정** 제목 아래에서 **가격 책정 계층을** 클릭하여 가격 책정 계층 페이지를 엽니다.

3. 자동 증가 섹션에서 **예(예)를** 선택하여 저장소 자동 증가를 활성화합니다.

    ![MariaDB용 Azure 데이터베이스 - Settings_Pricing_tier - 자동 증가](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. **확인**을 클릭하여 변경 내용을 저장합니다.

5. 자동 증가가 성공적으로 활성화되었다는 알림이 표시됩니다.

    ![MariaDB용 Azure 데이터베이스 - 자동 성장 성공](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법에](howto-alert-metric.md)대해 알아봅니다.
