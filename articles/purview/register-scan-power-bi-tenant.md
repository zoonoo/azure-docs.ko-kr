---
title: Power BI 테 넌 트 등록 및 검사 (미리 보기)
description: Azure 부서의 범위 포털을 사용 하 여 Power BI 테 넌 트를 등록 하 고 검색 하는 방법을 알아봅니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695750"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI 테 넌 트 등록 및 검사 (미리 보기)

이 문서에서는 Azure 부서의 범위 포털을 사용 하 여 Power BI 테 넌 트를 등록 하 고 검색 하는 방법을 보여 줍니다.

> [!Note]
> 부서의 범위 인스턴스와 Power BI 테 넌 트가 동일한 Azure 테 넌 트에 있는 경우 관리 되는 id (MSI) 인증만 사용 하 여 Power BI 테 넌 트의 검색을 설정할 수 있습니다. 

## <a name="create-a-security-group-for-permissions"></a>사용 권한에 대 한 보안 그룹 만들기

인증을 설정 하려면 보안 그룹을 만들고 부서의 범위 관리 id를 추가 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 검색 합니다.
1. [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 멤버 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 수행 하 여 Azure Active Directory에서 새 보안 그룹을 만듭니다.

    > [!Tip]
    > 사용 하려는 보안 그룹이 이미 있는 경우이 단계를 건너뛸 수 있습니다.

1. **그룹 유형** 으로 **보안** 을 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="보안 그룹 종류":::

1. 부서의 범위 관리 id를이 보안 그룹에 추가 합니다. **멤버** 를 선택 하 고 **+ 구성원 추가** 를 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="카탈로그의 관리 되는 인스턴스를 그룹에 추가 합니다.":::

1. 부서의 범위 관리 id를 검색 하 고 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="검색 하 여 카탈로그 추가":::

    추가 되었음을 나타내는 성공 알림이 표시 됩니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="카탈로그 MSI 추가 성공":::

## <a name="associate-the-security-group-with-the-tenant"></a>보안 그룹을 테 넌 트와 연결

1. [Power BI 관리 포털](https://app.powerbi.com/admin-portal/tenantSettings)에 로그인 합니다.
1. **테 넌 트 설정** 페이지를 선택 합니다.

    > [!Important]
    > 테 넌 트 설정 페이지를 보려면 Power BI 관리자 여야 합니다.

1.   >  **서비스 사용자가 읽기 전용 Power BI 관리 api (미리 보기)를 사용할 수 있도록** 관리 api 설정을 선택 합니다.
1. **특정 보안 그룹** 을 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="서비스 사용자가 읽기 전용 Power BI admin API 권한을 가져오도록 허용 하는 방법을 보여 주는 이미지":::

    > [!Caution]
    > 사용자가 만든 보안 그룹 (부서의 범위 관리 되는 id가 멤버인 경우)을 사용 하 여 읽기 전용 Power BI 관리 Api를 사용 하는 경우이 테 넌 트의 모든 Power BI 아티팩트에 대 한 메타 데이터 (예: 대시보드 및 보고서 이름, 소유자, 설명 등)에 액세스 하도록 허용할 수도 있습니다. 메타 데이터를 Azure 부서의 범위로 끌어온 후에는 Power BI 권한이 없는 부서의 범위의 사용 권한을 통해 해당 메타 데이터를 볼 수 있는 사람을 결정 합니다.

    > [!Note]
    > 개발자 설정에서 보안 그룹을 제거할 수 있지만 이전에 추출 된 메타 데이터는 부서의 범위 계정에서 제거 되지 않습니다. 원할 경우 별도로 삭제할 수 있습니다.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Power BI 등록 및 검색 설정

Power BI 테 넌 트의 관리 API에 연결 하기 위한 부서의 범위 관리 Id 권한을 제공 했으므로 Azure 부서의 범위 Studio에서 검색을 설정할 수 있습니다.

먼저 부서의 범위 URL에 특수 기능 플래그를 추가 합니다. 

1. **관리 센터** 아이콘을 선택 합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="관리 센터 아이콘.":::

1. 그런 다음 **데이터 원본** 에서 **+ 새로 만들기** 를 선택 합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="새 데이터 원본 단추 이미지":::

    데이터 원본으로 **Power BI** 를 선택 합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="선택할 수 있는 데이터 원본 목록을 보여 주는 이미지":::

3. Power BI 인스턴스에 친숙 한 이름을 지정 합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI 데이터 원본-친근 한 이름 표시 이미지":::

    이름은 3-63 자 사이 여야 하며 문자, 숫자, 밑줄 및 하이픈만 포함 해야 합니다.  공백은 허용 되지 않습니다.

    기본적으로 시스템은 동일한 Azure 구독에 있는 Power BI 테 넌 트를 찾습니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="등록 된 Power BI 데이터 원본":::

    > [!Note]
    > Power BI의 경우 데이터 원본 등록과 검색은 하나의 인스턴스에만 허용 됩니다.


4. 검색에 이름을 지정 합니다. 그런 다음 개인 작업 영역을 포함 하거나 제외 하는 옵션을 선택 합니다. 유일 하 게 지원 되는 인증 방법은 **관리** 되는 id입니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI 검사 설치를 보여 주는 이미지":::

    > [!Note]
    > * 개인 작업 영역을 포함 하거나 제외 하도록 검색 구성을 전환 하면 PowerBI 원본의 전체 검사가 트리거됩니다.
    > * 검색 이름은 3-63 자 사이 여야 하며 문자, 숫자, 밑줄 및 하이픈만 포함 해야 합니다. 공백은 허용 되지 않습니다.

5. 검색 트리거를 설정 합니다. 옵션은 1, **7 일**, **30 일 마다** 한 **번** 입니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="검사 트리거 이미지":::

6. **새 검색 검토** 에서 **저장 및 실행** 을 선택 하 여 검색을 시작 합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI 화면 이미지를 저장 하 고 실행 합니다.":::

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
