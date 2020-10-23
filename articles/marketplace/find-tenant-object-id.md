---
title: Azure Marketplace에서 테 넌 트 ID, 개체 ID 및 파트너 연결 정보를 찾습니다.
description: Azure Marketplace에서 구독 ID의 테 넌 트 ID, 개체 ID 및 파트너 연결 정보를 찾는 방법입니다.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: db09943085cb7934bca5d7f2dc24ba692613ee19
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426898"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>테 넌 트 ID, 개체 ID 및 파트너 연결 정보 찾기

이 문서에서는 테 넌 트 ID, 개체 ID 및 파트너 연결 정보를 해당 구독 Id와 함께 찾는 방법을 설명 합니다.

Azure Cloud Shell에서 디버깅 지원에 사용할 수 있도록 이러한 항목의 스크린샷을 받으려면 아래로 이동 하 여 [디버깅을 위한 테 넌 트, 개체 및 파트너 ID 연결을 찾습니다](#find-ids-for-debugging).

>[!Note]
> 구독 소유자 에게만 이러한 단계를 수행할 수 있는 권한이 있습니다.

## <a name="find-tenant-id"></a>테 넌 트 ID 찾기

1. [Azure 포털](https://ms.portal.azure.com/)로 이동합니다.
2. **Azure Active Directory**를 선택합니다.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

3. **그룹**을 선택합니다. 테 넌 트 ID는 **테 넌 트 정보** 상자에 있습니다.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

## <a name="find-subscriptions-and-roles"></a>구독 및 역할 찾기

1. Azure Portal로 이동 하 고 위의 1 단계와 2 단계에서 설명한 대로 **Azure Active Directory** 를 선택 합니다.
2. **구독**을 선택합니다.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

3. 구독 및 역할을 봅니다.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

## <a name="find-partner-id"></a>파트너 ID 찾기

1. 이전 섹션에서 설명한 대로 구독 페이지로 이동 합니다.
2. 구독을 선택합니다.
3. **청구**에서 **파트너 정보**를 선택 합니다.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

## <a name="find-user-object-id"></a>사용자 찾기 (개체 ID)

1. 적절 한 관리 권한을 사용 하 여 원하는 테 넌 트의 계정으로 [Office 365 관리 포털](https://portal.office.com/adminportal/home) 에 로그인 합니다.
2. 왼쪽 메뉴에서 아래쪽의 **관리 센터** 섹션을 확장 한 다음 Azure Active Directory 옵션을 선택 하 여 새 브라우저 창에서 관리 콘솔을 시작 합니다.
3. **사용자**를 선택합니다.
4. 원하는 사용자를 찾아보거나 검색 한 다음 계정 이름을 선택 하 여 사용자 계정의 프로필 정보를 확인 합니다.
5. 개체 ID는 오른쪽의 Id 섹션에 있습니다.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

6. 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 하 고 **역할 할당**을 선택 하 여 **역할 할당** 을 찾습니다.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

## <a name="find-ids-for-debugging"></a>디버그할 Id 찾기

이 섹션에서는 디버깅 목적으로 테 넌 트, 개체 및 파트너 ID 연결을 찾는 방법에 대해 설명 합니다.

1. [Azure 포털](https://ms.portal.azure.com/)로 이동합니다.
2. 오른쪽 위에서 PowerShell 아이콘을 선택 하 여 Azure Cloud Shell을 엽니다.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

3. **PowerShell**을 선택 합니다.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

4. **구독** 상자를 선택 하 여 파트너가 연결 된 항목을 선택한 다음 **저장소를 만듭니다**. 일회성 작업입니다. 저장소를 이미 설정한 경우 다음 단계를 진행 합니다.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

5. 저장소를 만들거나 이미 있으면 Azure Cloud Shell 창이 열립니다.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

6. 파트너 연결 정보를 보려면 다음 명령을 사용 하 여 확장을 설치 합니다.<br>`az extension add --name managementpartner`.<br>확장이 이미 설치 되어 있는지 여부를 Azure Cloud Shell 합니다.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

7. 다음 명령을 사용 하 여 파트너 정보를 확인 합니다.<br>`az managementpartner show --partner-id xxxxxx`<br>예: `az managementpartner show --partner-id 4760962`.<br>다음과 유사 하 게 표시 되는 명령 결과의 스크린 샷을 스냅 합니다.

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Azure Portal의 Azure Active Directory 아이콘입니다.":::

>[!NOTE]
>여러 구독에 스크린샷을 요구 하는 경우 다음 명령을 사용 하 여 구독 간에 전환 합니다.<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>다음 단계

- [지원되는 국가 및 지역](sell-from-countries.md)