---
title: 저장소 계정 이름 오류
description: 스토리지 계정 이름을 지정할 때 발생할 수 있는 오류를 설명합니다.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76153975"
---
# <a name="resolve-errors-for-storage-account-names"></a>스토리지 계정 이름 오류 해결

이 문서에서는 스토리지 계정을 배포할 때 발생할 수 있는 명명 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

스토리지 계정 이름에 금지된 문자가 포함되면 다음 오류가 표시됩니다.

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

스토리지 계정에 대해서는 Azure에서 고유한 리소스 이름을 제공해야 합니다. 고유한 이름을 제공하지 않으면 다음과 같은 오류 메시지가 표시됩니다.

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

구독에서 기존 스토리지 계정과 동일한 이름의 스토리지 계정을 배포하지만 다른 위치를 제공하는 경우 해당 스토리지 계정이 이미 다른 위치에 이미 있다고 나타내는 오류 메시지가 표시됩니다. 이 경우 기존 스토리지 계정을 삭제하거나 기존 스토리지 계정과 동일한 위치를 제공합니다.

## <a name="cause"></a>원인

Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다. 이름은 고유해야 합니다.

## <a name="solution"></a>솔루션

스토리지 계정 이름이 고유한지 확인합니다. 명명 규칙과 [uniqueString](template-functions-string.md#uniquestring) 함수 결과를 연결하여 고유한 이름을 만들 수 있습니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

스토리지 계정 이름이 24자를 초과하지 않는지 확인합니다. [uniqueString](template-functions-string.md#uniquestring) 함수는 13자를 반환합니다. **uniqueString** 결과에 접두사 또는 접미사를 연결하는 경우 11자 이하의 값을 제공합니다.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

스토리지 계정 이름에 대문자 또는 특수 문자가 포함되지 않았는지 확인합니다.