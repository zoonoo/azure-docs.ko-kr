---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5a9d60cd019e044fd19a8670c0843a8d155f5433
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166589"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[리소스 그룹에 태그 추가](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |이 태그를 누락하는 리소스 그룹을 만들거나 업데이트할 때 지정된 태그 및 값을 추가합니다. 기존 리소스 그룹은 수정 작업을 트리거하여 수정할 수 있습니다. 태그가 다른 값으로 존재하는 경우 변경되지 않습니다. |수정 |1.0.0 |
|[리소스에 태그 추가](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |이 태그를 누락하는 리소스를 만들거나 업데이트할 때 지정된 태그 및 값을 추가합니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다. 태그가 다른 값으로 존재하는 경우 변경되지 않습니다. 리소스 그룹에 대한 태그는 수정하지 않습니다. |수정 |1.0.0 |
|[리소스 그룹에 태그 추가 또는 바꾸기](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |리소스 그룹을 만들거나 업데이트할 때 지정된 태그 및 값을 추가하거나 바꿉니다. 기존 리소스 그룹은 수정 작업을 트리거하여 수정할 수 있습니다. |수정 |1.0.0 |
|[리소스에 태그 추가 또는 바꾸기](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |리소스를 만들거나 업데이트할 때 지정된 태그 및 값을 추가하거나 바꿉니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다. 리소스 그룹에 대한 태그는 수정하지 않습니다. |수정 |1.0.0 |
|[태그 및 기본값 추가](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |이 태그를 누락하는 리소스를 만들거나 업데이트할 때 지정된 태그 및 값을 추가합니다. 해당 리소스가 변경될 때까지 이 정책을 적용하기 전에 만들어진 리소스의 태그를 수정하지 않습니다. 리소스 그룹에는 적용되지 않습니다. 기존 리소스에 대한 태그 수정을 지원하는 새로운 '수정' 효과 정책을 사용할 수 있습니다(https://aka.ms/modifydoc) 참조). |추가 |1.0.0 |
|[리소스 그룹에 태그 및 기본값 추가](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |이 태그를 누락하는 리소스 그룹을 만들거나 업데이트할 때 지정된 태그 및 값을 추가합니다. 해당 리소스 그룹이 변경될 때까지 이 정책을 적용하기 전에 만들어진 리소스 그룹의 태그를 수정하지 않습니다. 기존 리소스에 대한 태그 수정을 지원하는 새로운 '수정' 효과 정책을 사용할 수 있습니다(https://aka.ms/modifydoc) 참조). |추가 |1.0.0 |
|[리소스 그룹에서 태그 및 기본값 추가](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |이 태그를 누락하는 리소스를 만들거나 업데이트할 때 리소스 그룹의 해당 값으로 지정된 태그를 추가합니다. 해당 리소스가 변경될 때까지 이 정책을 적용하기 전에 만들어진 리소스의 태그를 수정하지 않습니다. 기존 리소스에 대한 태그 수정을 지원하는 새로운 '수정' 효과 정책을 사용할 수 있습니다(https://aka.ms/modifydoc) 참조). |추가 |1.0.0 |
|[리소스 그룹에서 태그 상속](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |리소스를 만들거나 업데이트할 때 부모 리소스 그룹의 지정된 태그 및 값을 추가하거나 바꿉니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다. |수정 |1.0.0 |
|[없는 경우 리소스 그룹에서 태그 상속](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |이 태그를 누락하는 리소스를 만들거나 업데이트할 때 부모 리소스 그룹의 해당 값으로 지정된 태그를 추가합니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다. 태그가 다른 값으로 존재하는 경우 변경되지 않습니다. |수정 |1.0.0 |
|[지정된 태그 필요](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |태그의 존재를 적용합니다. 리소스 그룹에는 적용되지 않습니다. |deny |1.0.0 |
|[리소스 그룹에 지정된 태그 필요](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |리소스 그룹에 태그 사용을 적용합니다. |deny |1.0.0 |
|[태그와 해당 값 필요](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |필수 태그와 해당 값을 적용합니다. 리소스 그룹에는 적용되지 않습니다. |deny |1.0.0 |
|[리소스 그룹에 태그 및 해당 값 필요](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |리소스 그룹에 필요한 태그와 해당 값을 적용합니다. |deny |1.0.0 |
