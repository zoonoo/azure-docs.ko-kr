---
title: Azure Automation 계정 관리 ID 사용 안 함(미리 보기)
description: 이 문서에서는 Azure Automation 계정의 관리 ID를 사용하지 않도록 설정하고 제거하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519275"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Azure Automation 계정 관리 ID 사용 안 함(미리 보기)

Azure Automation에서 시스템 할당한 ID를 사용하지 않도록 설정하는 방법에는 두 가지가 있습니다. Azure Portal에서 또는 ARM(Azure Resource Manager) 템플릿을 사용하여 이 작업을 완료할 수 있습니다.

## <a name="disable-managed-identity-in-the-azure-portal"></a>Azure Portal에서 관리 ID 사용 안 함

원래 관리 ID를 설정한 방법에 관계없이 Azure Portal에서 관리 ID를 사용하지 않도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Automation 계정으로 이동하고 **계정 설정** 에서 **ID** 를 선택합니다.

1. **시스템 할당** 옵션을 **끄기** 로 설정하고 **저장** 을 누릅니다. 확인할지 묻는 메시지가 표시되면 **예** 를 누릅니다.

이제 관리 ID가 제거되었고 대상 리소스에 더 이상 액세스할 수 없습니다.

## <a name="disable-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용 안 함

Azure Resource Manager 템플릿을 사용하여 Automation 계정의 관리 ID를 만든 경우 해당 템플릿을 다시 사용하고 해당 설정을 수정하여 관리 ID를 사용하지 않도록 설정할 수 있습니다. ID 개체의 자식 속성 형식을 **없음** 으로 설정하고 다음 예제와 같이 템플릿을 다시 실행합니다.

```json
"identity": { 
   "type": "None" 
} 
```

이 방법으로 시스템 할당 ID를 제거하면 해당 ID가 Azure AD에서도 제거됩니다. 시스템 할당 ID가 할당된 앱 리소스가 삭제되면 해당 ID가 Azure AD에서도 자동으로 제거됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Automation에서 관리 ID를 사용하도록 설정하는 방법에 관한 자세한 내용은 [Automation의 관리 ID 사용하도록 설정 및 사용(미리 보기)](enable-managed-identity-for-automation.md)을 참조하세요.

- Automation 계정 보안의 개요는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.
