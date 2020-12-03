---
title: 방화벽 뒤에 있는 Azure SQL DB 검색
description: Azure 부서의 범위 포털을 사용 하 여 방화벽 뒤에 있는 리소스를 검색 하는 방법을 알아봅니다.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553809"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Azure 부서의 범위에서 방화벽 뒤에 있는 Azure SQL DB를 검색 합니다.

이 문서에서는 Azure 부서의 범위를 사용 하 여 방화벽 뒤에 있는 리소스를 검색 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 사용자 고유의 [Azure Active Directory 테 넌 트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="set-up-sql-storage-behind-a-firewall"></a>방화벽 뒤에 SQL 저장소 설정

첫 번째 단계는 등록을 통해 Azure SQL DB에 카탈로그의 MSI를 추가 하 [고 Azure SQL Database를 검색](register-scan-azure-sql-database.md)하는 것입니다.

## <a name="scan-sql-db-from-purview"></a>부서의 범위에서 SQL DB 검색

1. 부서의 범위 홈 페이지로 이동 합니다.

1. 왼쪽 탐색 모음에서 **관리 센터** 를 선택 합니다.

1. **원본 및 검색** 에서 **데이터 원본** 을 선택 합니다.

1. **+ 새로 만들기** 를 선택 하 여 데이터 원본을 추가 합니다.

1. **Azure SQL Database** 를 선택합니다.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Sql server를 선택 하는 스크린샷":::

1. 새 데이터 원본의 이름을 제공 하 고, **Azure 구독에서** 를 선택 하 고, 드롭다운에서 구독 및 서버 이름을 선택 합니다.

   **마침** 을 선택 하 여 등록을 완료 합니다. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="선택 영역을 완료 하기 위한 스크린샷":::

1. 방화벽 뒤에 있는 저장소에 대 한 **검색 설정** 을 선택 하 고 연결을 테스트 합니다. 연결에 성공이 표시 됩니다. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="선택 t0 설정 검색의 스크린샷":::

1. 검색 빈도를 설정 하 고 **계속** 을 선택 합니다.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Sql 검색을 시작 하기 위한 선택 항목의 스크린샷":::

1.  검색이 완료 되 고 데이터 원본 목록에서 상태가 업데이트 됩니다.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="메시지 검색 완료 스크린샷":::
   
## <a name="next-steps"></a>다음 단계

다음으로, 검색 규칙 집합을 설정 하 여 검색 [규칙 집합 만들기](create-a-scan-rule-set.md) 를 그룹 검색으로 그룹화 할 수 있습니다.
