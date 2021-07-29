---
title: Power BI 테넌트 등록 및 검사(미리 보기)
description: Azure Purview Portal을 활용하여 Power BI 테넌트를 등록하고 검사하는 방법을 알아봅니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 402618872c5a3ee8dd01c35021656312f1fe3371
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108228445"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI 테넌트 등록 및 검사(미리 보기)

본 문서에서는 Azure Purview Portal을 활용하여 Power BI 테넌트를 등록하고 검사하는 방법을 보여 줍니다.

> [!Note]
> Purview 인스턴스와 Power BI 테넌트가 동일한 Azure 테넌트 내에 존재하는 경우, Power BI 테넌트 검사를 설정하는 데에는 관리 ID(MSI) 인증만을 사용할 수 있습니다. 

## <a name="create-a-security-group-for-permissions"></a>사용 권한에 대한 보안 그룹 만들기

인증을 설정하려면 보안 그룹을 만들어 Purview 관리 ID를 여기에 추가하여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 검색합니다.
1. [Azure Active Directory를 이용하여 기본 그룹 만들고 멤버 추가하기](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)에 따라 Azure Active Directory에 새로운 보안 그룹을 만듭니다.

    > [!Tip]
    > 사용하려는 보안 그룹을 이미 보유한 상태에서는 이 단계를 건너뛸 수 있습니다.

1. **그룹 형식** 으로 **보안** 을 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="보안 그룹 형식":::

1. Purview 관리 ID를 해당 보안 그룹에 추가합니다. **멤버** 를 선택한 다음, **+ 멤버 추가** 를 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="카탈로그의 관리형 인스턴스를 그룹에 추가하기":::

1. 자신의 Purview 관리 ID를 찾아 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="카탈로그를 검색하여 추가하기":::

    추가되었음을 알리는 성공 알림을 확인할 수 있습니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="카탈로스 MSI 추가 성공":::

## <a name="associate-the-security-group-with-the-tenant"></a>테넌트에 보안 그룹 연결하기

1. [Power BI 관리자 포털](https://app.powerbi.com/admin-portal/tenantSettings)에 로그인합니다.
1. **테넌트 설정** 페이지를 선택합니다.

    > [!Important]
    > 테넌트 설정 페이지를 확인하려면 Power BI 관리자여야 합니다.

1. **관리자 API 설정** > **서비스 사용자에게 읽기 전용 Power BI 관리자 APIs 사용을 허용하기(미리 보기)** 를 선택합니다.
1. **특정 보안 그룹** 을 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="서비스 사용자가 읽기 전용 Power BI 관리자 API 권한을 가져오도록 허용하는 방법을 보여 주는 이미지":::

    > [!Caution]
    > (자신의 Purview 관리 ID를 멤버로 하는) 만들어 놓은 보안 그룹이 읽기 전용 Power BI 관리자 API를 사용하도록 하는 경우, 해당 테넌트 내의 모든 Power BI 아티팩트에 관한 메타데이터(예: 대시보드 및 보고서 이름, 소유자, 설명 등)에도 해당 보안 그룹이 액세스할 수 있게 됩니다. 메타데이터를 Azure Purview에 끌어오면 Power BI 권한이 아니라 Purview 권한이 해당 메타데이터를 확인할 수 있는 인원을 결정합니다.

    > [!Note]
    > 개발자 설정에서 해당 보안 그룹을 제거할 수는 있지만 이미 추출된 메타데이터는 해당 Purview 계정에서 제거되지 않습니다. 원하는 경우 이를 개별적으로 삭제할 수는 있습니다.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Power BI 등록 및 검사 설정

이제 Purview 관리 ID 권한이 Power BI 테넌트의 관리자 API에 대한 연결 권한을 확보한 상태이므로, Azure Purview Studio에서 검사를 설정할 수 있습니다.

1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.

1. 그런 다음, **등록** 을 선택합니다.

    데이터 소스로 **Power BI** 를 선택합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="선택할 수 있는 데이터 소스의 목록을 보여 주는 이미지":::

3. Power BI 인스턴스에 친숙한 이름을 부여합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI 데이터 소스-친숙한 이름을 보여 주는 이미지":::

    이름은 3~63자 길이 내의 문자, 숫자, 밑줄, 하이픈으로만 작성되어야 합니다.  공백이 없어야 합니다.

    기본적으로 시스템은 동일 Azure 구독 내에 존재하는 Power BI 테넌트를 찾습니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="등록된 Power BI 데이터 소스":::

    > [!Note]
    > Power BI의 경우, 데이터 소스 등록과 검사는 하나의 인스턴스에만 할 수 있습니다.


4. 검사 이름을 짓습니다. 그런 다음 개인 작업 영역 포함 여부를 선택합니다. 지원되는 유일한 인증 방법은 **관리 ID** 입니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI 검사 설정을 보여 주는 이미지":::

    > [!Note]
    > * 검사가 개인 작업 영역 포함 여부에 대한 구성을 전환하면 PowerBI 소스에 대한 전체 검사가 트리거됩니다.
    > * 검사 이름은 3~63자 길이 내의 문자, 숫자, 밑줄, 하이픈으로만 작성되어야 합니다. 공백이 없어야 합니다.
    > * 스키마 탭에서는 스키마를 사용할 수 없습니다.

5. 검사 트리거를 설정합니다. 옵션으로는 **1회**, **매 7일마다**, **매 30일마다** 가 있습니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="검사 트리거 이미지":::

6. **새로운 검사 검토하기** 에서, **저장 후 실행** 을 선택하여 검사를 시작합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI 저장 후 실행 화면 이미지":::

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
