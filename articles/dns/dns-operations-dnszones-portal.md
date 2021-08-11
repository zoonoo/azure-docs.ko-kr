---
title: Azure DNS에서 DNS 영역 관리 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 DNS 영역을 관리할 수 있습니다. 이 문서에서는 Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법을 설명합니다.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 58118480c16f2e318bab7435a79e27629880acc5
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203217"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Azure Portal에서 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [포털](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure 클래식 CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

이 문서는 Azure Portal을 사용하여 DNS 영역을 관리하는 방법을 보여줍니다. 플랫폼 간 [Azure CLI](dns-operations-dnszones-cli.md) 또는 Azure [PowerShell](dns-operations-dnszones.md)을 사용하여 DNS 영역을 관리할 수도 있습니다.

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** 를 선택하고 **DNS 영역** 을 검색합니다. 그런 다음 **생성** 를 선택합니다.

    :::image type="content" source="./media/dns-operations-dnszones-portal/search-dns-zone.png" alt-text="DNS 영역에 대한 리소스 검색 만들기 스크린샷":::

1. **DNS 영역 만들기** 페이지에서 다음 값을 입력한 다음, **만들기** 를 선택합니다.

    | Setting | 세부 정보 |
    | --- | --- |
    | **구독** | DNS 영역을 만들 구독을 선택합니다.|
    | **리소스 그룹** | 새 리소스 그룹을 선택하거나 만듭니다. 리소스 그룹에 대해 자세히 알아보려면 [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 개요 문서를 참조하세요.|
    | **이름** | DNS 영역의 이름을 입력합니다. 예: **contoso.com** |
    | **위치** | 리소스 그룹의 위치를 선택합니다. 이전에 만든 리소스 그룹을 사용하는 경우 위치가 미리 선택됩니다.  |

> [!NOTE]
> 리소스 그룹은 리소스 그룹의 위치를 나타내며 DNS 영역에 영향을 미치지 않습니다. DNS 영역 위치는 항상 "전역"이며 표시되지 않습니다.

## <a name="list-dns-zones"></a>DNS 영역 나열

Azure Portal 위쪽의 검색 리소스에서 **DNS 영역** 을 검색합니다. 각 DNS 영역은 자체 리소스입니다. 레코드 집합의 수 및 이름 서버와 같은 정보를 이 페이지에서 볼 수 있습니다. 열 **이름 서버** 는 기본 보기에 없습니다. 이를 추가하려면 **관리형 보기 > 열 편집 > + 열 추가** 를 선택한 다음, 드롭다운에서 **이름 서버** 를 선택합니다. **저장** 을 선택하여 새 열을 적용합니다.

:::image type="content" source="./media/dns-operations-dnszones-portal/list-zones.png" alt-text="DNS 영역 목록 페이지의 스크린샷":::

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

포털에서 DNS 영역으로 이동합니다. 선택한 **DNS 영역** 개요 페이지에서 **영역 삭제** 를 선택합니다. 그런 다음, DNS 영역을 삭제할지 확인하는 메시지가 표시됩니다. DNS 영역을 삭제하면 해당 영역에 포함된 모든 레코드도 삭제됩니다.

:::image type="content" source="./media/dns-operations-dnszones-portal/delete-zone.png" alt-text="개요 페이지의 DNS 영역 삭제 단추의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 Azure DNS 시작](dns-getstarted-portal.md)을 방문하여 DNS 영역 및 레코드 사용 방법을 알아봅니다.
