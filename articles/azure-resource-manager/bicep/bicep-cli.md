---
title: Bicep CLI 명령 및 개요
description: Bicep CLI에서 사용할 수 있는 명령에 대해 설명합니다. 이러한 명령에는 Bicep에서 Azure Resource Manager 템플릿을 빌드하는 명령이 포함됩니다.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: dd1f292d4ce60353d2f8cecaaa83e38b26bdfaa3
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111540807"
---
# <a name="bicep-cli-commands"></a>Bicep CLI 명령

이 문서에서는 Bicep CLI에서 사용할 수 있는 명령에 대해 설명합니다. 이러한 명령을 실행하려면 [Bicep CLI가 설치](./install.md)되어 있어야 합니다.

이 문서에서는 Azure CLI에서 명령을 실행하는 방법을 보여 줍니다. Azure CLI 사용하지 않는 경우 명령을 시작할 때 `az`를 사용하지 않고 명령을 실행합니다. 예를 들어 `az bicep version`는 ``bicep version``가 됩니다.

## <a name="build"></a>빌드

`build` 명령은 Bicep 파일을 ARM 템플릿(Azure Resource Manager 템플릿)으로 변환합니다. 일반적으로 이 명령은 Bicep 파일을 배포할 때 자동으로 실행되므로 실행할 필요가 없습니다. Bicep 파일에서 만든 ARM 템플릿 JSON을 보려면 수동으로 실행합니다.

다음 예제에서는 _main.bicep_ 이라는 Bicep 파일을 _main.json_ 이라는 ARM 템플릿으로 변환합니다. 새 파일은 Bicep 파일이 있는 동일한 디렉터리에 만들어집니다.

```azurecli
az bicep build --file main.bicep
```

다음 예제는 _main.json_ 을 다른 디렉터리에 저장합니다.

```azurecli
 az bicep build --file main.bicep --outdir c:\jsontemplates
```

다음 예제는 만들 파일의 이름과 위치를 지정합니다.

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

`stdout`에 파일을 인쇄하려면 다음을 사용합니다.

```azurecli
az bicep build --file main.bicep --stdout
```

## <a name="decompile"></a>디컴파일

`decompile` 명령은 ARM 템플릿 JSON을 Bicep 파일로 변환합니다.

```azurecli
az bicep decompile --file main.json
```

이 명령 사용에 대한 자세한 내용은 [ARM 템플릿 JSON을 Bicep으로 디컴파일](decompile.md)을 참조하세요.

## <a name="install"></a>설치

`install` 명령은 로컬 환경에 Bicep CLI를 추가합니다. 자세한 내용은 [Bicep 도구 설치](install.md)를 참조하세요.

최신 버전을 설치하려면 다음을 사용하세요.

```azurecli
az bicep install
```

특정 버전을 설치하려면

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

`list-versions` 명령은 사용 가능한 모든 버전의 Bicep CLI를 반환합니다. 이 명령을 사용하여 새 버전을 [업그레이드](#upgrade)하거나 [설치](#install)할 것인지 확인합니다.

```azurecli
az bicep list-versions
```

이 명령은 사용 가능한 버전 배열을 반환합니다.

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="upgrade"></a>업그레이드

`upgrade` 명령은 설치된 버전을 최신 버전으로 업데이트합니다.

```azurecli
az bicep upgrade
```

## <a name="version"></a>버전

`version` 명령은 설치된 버전을 반환합니다.

```azurecli
az bicep version
```

이 명령은 버전 번호를 표시합니다.

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

Bicep CLI를 설치하지 않은 경우 Bicep CLI를 찾을 수 없다는 오류가 표시됩니다.

## <a name="next-steps"></a>다음 단계

Bicep 파일 배포에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CLI](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
