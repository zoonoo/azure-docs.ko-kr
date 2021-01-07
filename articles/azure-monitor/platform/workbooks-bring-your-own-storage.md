---
title: 사용자 고유의 저장소를 가져오는 Azure Monitor 통합 문서
description: 통합 문서 콘텐츠를 저장소에 저장 하 여 통합 문서를 보호 하는 방법을 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 09ea29d8149298f906672979ea76da0558a2934c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371185"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>사용자 고유의 저장소로 통합 문서 저장

보안을 유지 하려는 쿼리 또는 비즈니스 논리가 있는 경우가 있습니다. 통합 문서는 통합 문서 콘텐츠를 저장소에 저장 하 여 통합 문서를 보호 하는 옵션을 제공 합니다. 그런 다음 Microsoft 관리 키를 사용 하 여 저장소 계정을 암호화 하거나 고유한 키를 제공 하 여 암호화를 관리할 수 있습니다. [저장소 서비스 암호화에 대 한 Azure 설명서를 참조 하세요.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>관리 id를 사용 하 여 통합 문서 저장

1. 저장소에 통합 문서를 저장 하려면 먼저 관리 되는 id (모든 서비스 > 관리 되는 id)를 만들고 `Storage Blob Data Contributor` 저장소 계정에 대 한 액세스 권한을 부여 해야 합니다. [관리 Id에 대 한 Azure 설명서를 참조 하세요.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![역할 할당 추가를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. 새 통합 문서를 만듭니다.
3. **저장** 단추를 선택 하 여 통합 문서를 저장 합니다.
4. 에는 옵션을 선택 하 여 `Save content to an Azure Storage Account` Azure Storage 계정에 저장할 수 있습니다.

    ![저장 된 대화 상자를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. 원하는 저장소 계정 및 컨테이너를 선택 합니다. 저장소 계정 목록은 위에서 선택한 구독에서 가져온 것입니다.

    ![저장소 옵션을 사용 하 여 저장 대화 상자를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. 그런 다음, **변경** 을 선택 하 여 이전에 만든 관리 되는 id를 선택 합니다.

    [![Id 변경 대화 상자를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. 저장소 옵션을 선택한 후 **저장** 을 눌러 통합 문서를 저장 합니다.

## <a name="limitations"></a>제한 사항

- 사용자 지정 저장소에 저장 하는 경우 개별 핀에 대시보드 자체의 보호 된 정보가 포함 되므로 통합 문서의 개별 부분을 대시보드에 고정할 수 없습니다. 사용자 지정 저장소를 사용 하는 경우 통합 문서 자체에 대 한 링크만 대시보드에 고정할 수 있습니다.
- 사용자 지정 저장소에 통합 문서를 저장 한 후에는 항상 사용자 지정 저장소에 저장 되므로 해제할 수 없습니다. 다른 곳에서 저장 하려면 "다른 이름으로 저장"을 사용 하 여 복사본을 사용자 지정 저장소에 저장 하지 않도록 선택할 수 있습니다.
- Application Insights 리소스의 통합 문서는 "레거시" 통합 문서 이며 사용자 지정 저장소를 지원 하지 않습니다. Application Insights 리소스의 최신 통합 문서는 "... 추가 "선택. 저장 시 레거시 통합 문서에는 구독 옵션이 없습니다.

   ![레거시 통합 문서를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>다음 단계

- 통합 문서에서 [지도](workbooks-map-visualizations.md) 시각화를 만드는 방법에 대해 알아봅니다.
- [통합 문서에서 그룹](workbooks-groups.md)을 사용 하는 방법을 알아봅니다.