---
title: JSON과 Bicep 간에 템플릿 변환
description: Azure Resource Manager 템플릿을 Bicep에서 JSON으로 변환하고 JSON에서 Bicep으로 변환하는 명령을 설명합니다.
ms.topic: conceptual
ms.date: 05/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed273e9cf9af6f29ecc495536fd5d504d2b168f2
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353588"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>JSON과 Bicep 간에 ARM 템플릿 변환

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 JSON에서 Bicep으로, Bicep에서 JSON으로 변환하는 방법을 설명합니다.

변환 명령을 실행하려면 [Bicep CLI 설치](bicep-install.md)가 되어 있어야 합니다.

변환 명령은 기능적으로 동일한 템플릿을 생성합니다. 그러나 구현은 정확하게 일치하지 않을 수 있습니다. 템플릿을 JSON에서 Bicep으로 변환한 다음 다시 JSON으로 변환하면 원래 템플릿과 구문이 다른 템플릿이 생성될 수 있습니다. 배포되었을 때, 변환된 템플릿은 동일한 결과를 생성합니다.

## <a name="convert-from-json-to-bicep"></a>JSON에서 Bicep으로 변환

Bicep CLI는 기존 JSON 템플릿을 Bicep 파일로 디컴파일하는 명령을 제공합니다.

Azure CLI 버전 2.20.0 이상이 있는 경우 Bicep CLI가 자동으로 설치됩니다. JSON 파일을 디컴파일하려면 다음 명령을 사용합니다.

```azurecli
az bicep decompile mainTemplate.json
```

최신 버전의 Azure CLI가 없고 대신 Bicep CLI를 수동으로 설치한 경우 다음을 사용합니다.

```bash
bicep decompile mainTemplate.json
```

해당 명령은 Bicep 작성을 위한 시작점을 제공합니다. 명령이 모든 템플릿에서 작동하지는 않습니다. 현재 중첩된 템플릿은 ‘내부’식 평가 범위를 사용하는 경우에만 디컴파일할 수 있습니다. 복사 루프를 사용하는 템플릿은 디컴파일할 수 없습니다.

## <a name="convert-from-bicep-to-json"></a>Bicep에서 JSON으로 변환

Bicep CLI는 Bicep을 JSON으로 변환하는 명령도 제공합니다. 

Azure CLI 버전 2.20.0 이상의 경우 다음 명령을 사용하여 JSON 파일을 빌드합니다.

```azurecli
az bicep build mainTemplate.bicep
```

Bicep CLI를 수동으로 설치한 경우 다음을 사용합니다.

```bash
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>템플릿 내보내기 및 변환

리소스 그룹의 템플릿을 내보낸 다음, 템플릿을 디컴파일 명령에 직접 전달할 수 있습니다. 다음 예시에서는 내보낸 템플릿을 디컴파일하는 방법을 보여 줍니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 통해 [템플릿 내보내기](export-template-portal.md).

다운로드한 파일에서 `bicep decompile <filename>`를 엽니다.

---

## <a name="side-by-side-view"></a>나란히 보기

[Bicep 플레이그라운드](https://aka.ms/bicepdemo)를 사용하면 동등한 JSON 파일 및 Bicep 파일을 나란히 볼 수 있습니다. 샘플 템플릿을 선택하여 두 버전을 모두 볼 수 있습니다. 또는 `Decompile`를 선택하여 자기만의 JSON 템플릿을 업로드하고 동등한 Bicep 파일을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Bicep에 대한 자세한 내용은 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 참조하세요.
