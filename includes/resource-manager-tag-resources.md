---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132255"
---
사용자가 태그를 적용하는 데 필요한 액세스 권한이 없는 경우 **태그 기여자** 역할을 사용자에게 할당할 수 있습니다. 자세한 내용은 [자습서: RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여를](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)참조하십시오.

1. 리소스 또는 리소스 그룹에 대한 태그를 보려면 개요에서 기존 태그를 찾습니다. 이전에 태그를 적용하지 않은 경우 목록이 비어 있습니다.

   ![리소스 또는 리소스 그룹에 대한 태그 보기](./media/resource-manager-tag-resources/view-tags.png)

1. 태그를 추가하려면 **태그를 추가하려면 여기를 클릭**을 선택합니다.

1. 이름 및 값을 제공합니다.

   ![태그 추가](./media/resource-manager-tag-resources/add-tag.png)

1. 필요에 따라 태그를 계속 추가합니다. 완료되면 **저장**을 선택합니다.

   ![태그 저장](./media/resource-manager-tag-resources/save-tags.png)

1. 이제 태그가 개요에 표시됩니다.

   ![태그 표시](./media/resource-manager-tag-resources/view-new-tags.png)

1. 태그를 추가하거나 삭제하려면 **변경**을 선택합니다.

1. 태그를 삭제하려면 휴지통 아이콘을 선택합니다. 그런 다음 **저장을**선택합니다.

   ![태그 삭제](./media/resource-manager-tag-resources/delete-tag.png)

여러 리소스에 태그를 대량으로 할당하려면 다음을 수행합니다.

1. 모든 리소스 목록에서 태그를 할당하려는 리소스에 대한 확인란을 선택합니다. 그런 다음 **태그 할당을 선택합니다.**

   ![여러 리소스 선택](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. 이름과 값을 추가합니다. 완료되면 **저장**을 선택합니다.

   ![할당 선택](./media/resource-manager-tag-resources/select-assign.png)

태그가 있는 모든 리소스를 보려면

1. Azure 포털 메뉴에서 **태그를 검색합니다.** 사용 가능한 옵션에서 선택합니다.

   ![태그로 찾기](./media/resource-manager-tag-resources/find-tags-general.png)

1. 리소스를 보기 위한 태그를 선택합니다.

   ![태그 선택](./media/resource-manager-tag-resources/select-tag.png)

1. 해당 태그가 지정된 모든 리소스가 표시됩니다.

   ![태그로 리소스 보기](./media/resource-manager-tag-resources/view-resources-by-tag.png)
