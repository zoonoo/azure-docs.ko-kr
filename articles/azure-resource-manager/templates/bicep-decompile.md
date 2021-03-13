---
title: JSON과 Bicep 간에 템플릿 변환
description: Azure Resource Manager 템플릿을 Bicep에서 JSON으로, JSON에서 Bicep로 변환 하는 명령에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103422184"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>JSON과 Bicep 간에 ARM 템플릿 변환

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿)을 JSON에서 Bicep으로, Bicep에서 JSON으로 변환 하는 방법을 설명 합니다.

변환 명령을 실행 하려면 [BICEP CLI가 설치](bicep-install.md) 되어 있어야 합니다.

변환 명령은 기능적으로 동일한 템플릿을 생성 합니다. 그러나 구현에서 정확 하 게 일치 하지 않을 수 있습니다. 템플릿을 JSON에서 Bicep로 변환 하 고 다시 JSON으로 변환 하면 원래 템플릿과 구문이 다른 템플릿이 생성 될 수 있습니다. 배포 된 경우 변환 된 템플릿은 동일한 결과를 생성 합니다.

## <a name="convert-from-json-to-bicep"></a>JSON에서 Bicep로 변환

Bicep CLI는 기존 JSON 템플릿을 Bicep 파일로 디컴파일 하는 명령을 제공 합니다. JSON 파일을 디컴파일 하려면 다음을 사용 합니다.

```azurecli
bicep decompile mainTemplate.json
```

이 명령은 Bicep 작성을 위한 시작점을 제공 합니다. 명령은 모든 템플릿에서 작동 하지 않습니다. 현재 중첩 된 템플릿은 ' inner ' 식 계산 범위를 사용 하는 경우에만 디컴파일된 수 있습니다. 복사 루프를 사용 하는 템플릿은 디컴파일된 수 없습니다.

## <a name="convert-from-bicep-to-json"></a>Bicep에서 JSON으로 변환

또한 Bicep CLI는 Bicep를 JSON으로 변환 하는 명령을 제공 합니다. JSON 파일을 작성 하려면 다음을 사용 합니다.

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>템플릿 내보내기 및 변환

리소스 그룹에 대 한 템플릿을 내보낸 다음,이를 디컴파일 명령에 직접 전달할 수 있습니다. 다음 예제에서는 내보낸 템플릿을 디컴파일 하는 방법을 보여 줍니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 통해 [템플릿을 내보냅니다](export-template-portal.md) .

`bicep decompile <filename>`다운로드 한 파일에서를 사용 합니다.

---

## <a name="side-by-side-view"></a>Side-by-side 보기

[Bicep 필드](https://aka.ms/bicepdemo) 를 사용 하면 동등한 JSON 및 Bicep 파일을 나란히 볼 수 있습니다. 샘플 템플릿을 선택 하 여 두 버전을 모두 볼 수 있습니다. 또는 `Decompile` 고유한 JSON 템플릿을 업로드 하 고 해당 하는 Bicep 파일을 보려면 선택 합니다.

## <a name="next-steps"></a>다음 단계

Bicep에 대 한 자세한 내용은 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 참조 하십시오.
