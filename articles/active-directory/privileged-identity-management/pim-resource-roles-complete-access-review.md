---
title: Azure 리소스에 대한 Privileged Identity Management - Azure 리소스에 대한 액세스 검토 완료 | Microsoft Docs
description: Azure 리소스에 대한 액세스 검토를 완료하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - 리소스 역할 - 액세스 검토 완료
[보안 검토가 시작](pim-resource-roles-start-access-review.md)된 후에 권한 있는 역할 관리자가 권한이 있는 액세스를 검토할 수 있습니다. Azure 리소스에 대한 PIM(Privileged Identity Management)에서는 사용자에게 해당 액세스를 검토하도록 요청하는 이메일을 자동으로 전송합니다. 사용자가 메일을 받지 못한 경우 [보안 검토를 수행하는 방법](pim-resource-roles-perform-access-review.md)의 지침을 보낼 수 있습니다.

보안 검토 기간이 끝나거나 모든 사용자가 자체 검토를 완료하면 이 문서의 단계에 따라 검토를 관리하고 결과를 표시합니다.

## <a name="manage-security-reviews"></a>보안 검토 관리
1. [Azure Portal](https://portal.azure.com/) 로 이동하고 대시보드에서 **Azure 리소스** 응용 프로그램을 선택합니다.
2. 리소스를 선택합니다.
3. 대시보드의 **액세스 검토** 섹션을 선택합니다.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. 관리하려는 액세스 검토를 선택합니다.

액세스 검토의 세부 정보 블레이드에는 검토를 관리하는 다양한 옵션이 있습니다.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>중지
모든 액세스 검토는 종료 날짜가 있지만, **중지** 단추를 사용하여 일찍 완료할 수 있습니다. 만일 이 때까지 검토하지 않은 사용자가 있다면, 검토를 중지한 후에는 그들을 검토할 수 없습니다. 검토를 중단한 후에는 다시 시작할 수 없습니다.

### <a name="reset"></a>다시 설정
액세스 검토를 다시 설정하여 모든 결정 사항을 제거할 수 있습니다. 액세스 검토를 다시 설정하면 모든 사용자가 다시 검토되지 않음으로 표시됩니다. 

### <a name="apply"></a>적용
종료 날짜가 되었거나 수동으로 중지하여 액세스 검토가 완료되면, **적용** 단추가 검토 결과를 구현합니다. 사용자의 액세스가 검토 중에 거부되었다면, 이는 그들의 역할 할당을 제거할 단계입니다.  

### <a name="delete"></a>삭제
더 이상 검토가 필요 없는 경우 검토를 삭제합니다. **삭제** 단추는 PIM 응용 프로그램에서 검토를 제거합니다.

## <a name="results"></a>결과
결과 탭에서 검토 결과 목록을 보고 다운로드합니다. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>검토자
검토자를 보고 기존 액세스 검토에 추가합니다. 검토자에게 검토를 완료하라는 메시지를 표시합니다.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



