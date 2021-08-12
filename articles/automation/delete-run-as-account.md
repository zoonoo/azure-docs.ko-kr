---
title: Azure Automation 실행 계정 삭제
description: 이 문서에서는 PowerShell 또는 Azure Portal에서 실행 계정을 삭제하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99055895"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Azure Automation 실행 계정 삭제

Azure Automation의 실행 계정은 Automation Runbook 및 기타 Automation 기능을 사용하여 Azure Resource Manager 또는 Azure 클래식 배포 모델에서 리소스를 관리하기 위한 인증을 제공합니다. 이 문서에서는 실행 계정 또는 클래식 실행 계정을 삭제하는 방법을 설명합니다. 이 작업을 실행하는 경우 Automation 계정이 보존됩니다. 실행 계정을 삭제한 후 Azure Portal에서 또는 제공된 PowerShell 스크립트를 사용하여 다시 만들 수 있습니다.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>실행 또는 클래식 실행 계정 삭제

1. Azure Portal에서 Automation 계정을 엽니다.

2. 왼쪽 창의 계정 설정 섹션에서 **실행 계정** 을 선택합니다.

3. 실행 계정 속성 페이지에서 삭제하려는 실행 계정 또는 클래식 실행 계정을 선택합니다.

4. 그런 다음, 선택한 계정의 속성 창에서 **삭제** 를 클릭합니다.

   ![실행 계정 삭제](media/delete-run-as-account/automation-account-delete-run-as.png)

5. 계정이 삭제되는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

## <a name="next-steps"></a>다음 단계

실행 계정 또는 클래식 실행 계정을 다시 만들려면 [실행 계정 만들기](create-run-as-account.md)를 참조하세요.