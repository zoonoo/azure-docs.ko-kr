---
title: "Azure Storage에서 보안 전송 필요 | Microsoft Docs"
description: "Azure Storage에 대한 \"보안 전송 필요\" 기능과 이 기능을 사용하도록 설정하는 방법에 대해 알아봅니다."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 96c641672ce6515fad3abc3fc0b8a6af037de346
ms.contentlocale: ko-kr
ms.lasthandoff: 08/31/2017

---
# <a name="require-secure-transfer"></a>보안 전송 필요

"보안 전송 필요" 옵션으로 안전한 연결에서 저장소 계정으로만 요청을 허용하여 저장소 계정의 보안을 강화합니다. 예를 들어 저장소 계정에 액세스하는 데 REST API를 호출하는 경우 HTTPS를 사용하여 연결해야 합니다. "보안 전송 필요"가 설정된 경우 HTTP를 사용한 모든 요청이 거부됩니다.

Azure 파일 서비스를 사용하는 경우 "보안 전송 필요"가 설정된 경우 암호화되지 않은 모든 연결이 실패합니다. 여기에는 암호화되지 않은 SMB 2.1, SMB 3.0을 사용하는 시나리오와 Linux SMB 클라이언트의 일부 유형이 포함됩니다. 

기본적으로 "보안 전송 필요" 옵션은 사용되지 않습니다.

> [!NOTE]
> Azure Storage에서 사용자 지정 도메인 이름에 대해 HTTPS를 지원하지 않으므로 사용자 지정 도메인 이름을 사용할 때 이 옵션이 적용되지 않습니다.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Azure Portal에서 "보안 전송 필요"를 사용하도록 설정

[Azure Portal](https://portal.azure.com)에서 저장소 계정을 만들 경우 및 기존 저장소 계정 모두에 "보안 전송 필요" 설정을 사용하도록 설정할 수 있습니다.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>저장소 계정을 만들 때 보안 전송 필요

1. Azure Portal에서 **저장소 계정 만들기** 블레이드를 엽니다.
1. **보안 전송 필요** 아래에서 **사용**을 선택합니다.

  ![스크린샷](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>기존 저장소 계정에 대해 보안 전송 필요

1. Azure Portal에서 기존 저장소 계정을 선택합니다.
1. 저장소 계정 메뉴 블레이드의 **설정** 아래에서 **구성**을 선택합니다.
1. **보안 전송 필요** 아래에서 **사용**을 선택합니다.

  ![스크린샷](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>프로그래밍 방식으로 "보안 전송 필요" 사용

설정 이름은 저장소 계정 속성에서 _supportsHttpsTrafficOnly_입니다. REST API, 도구 또는 라이브러리에서 사용할 수 있습니다.

* [REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts)(버전: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)(버전: 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11)(버전: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)(버전: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)(버전: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)(버전: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)(버전: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>REST API를 사용하여 "보안 전송 필요" 설정 사용

REST API를 사용하여 테스트를 단순화하기 위해 [ArmClient](https://github.com/projectkudu/ARMClient)를 사용하여 명령줄에서 호출할 수 있습니다.

 아래 명령줄을 사용하여 REST API로 설정을 확인할 수 있습니다.

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

응답에서 _supportsHttpsTrafficOnly_ 설정을 찾을 수 있습니다. 샘플:

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

아래 명령줄을 사용하여 REST API로 설정을 사용할 수 있습니다.

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

Input.json의 샘플:
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>다음 단계
Azure Storage는 여러 개발자가 보안 응용 프로그램을 함께 빌드할 수 있도록 하는 포괄적인 보안 기능을 제공합니다. 자세한 내용은 [저장소 보안 가이드](storage-security-guide.md)를 참조하세요.

