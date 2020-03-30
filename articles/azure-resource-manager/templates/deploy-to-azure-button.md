---
title: Azure 단추에 배포
description: 단추를 사용하여 GitHub 리포지토리에서 Azure 리소스 관리자 템플릿을 배포합니다.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109054"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>배포 단추를 사용하여 GitHub 리포지토리에서 템플릿 배포

이 문서에서는 **Azure에 배포** 단추를 사용하여 GitHub 리포지토리에서 템플릿을 배포하는 방법을 설명합니다. GitHub 리포지토리의 README.md 파일또는 리포지토리를 참조하는 웹 페이지에 단추를 직접 추가할 수 있습니다.

## <a name="use-common-image"></a>공통 이미지 사용

웹 페이지 또는 리포지토리에 단추를 추가하려면 다음 이미지를 사용합니다.

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

이미지는 다음과 같이 나타납니다.

![Azure 단추에 배포](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>템플릿 배포를 위한 URL 만들기

템플릿의 URL을 만들려면 리포지토리의 템플릿에 대한 원시 URL로 시작합니다.

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

그런 다음 URL을 인코딩합니다. 온라인 인코더를 사용하거나 명령을 실행할 수 있습니다. 다음 PowerShell 예제에서는 URL값을 인코딩하는 방법을 보여 주며, 이 예제에서는 값을 인코딩하는 방법을 보여 주며 있습니다.

```powershell
[uri]::EscapeDataString($url)
```

예제 URL에는 URL이 인코딩될 때 다음 값이 있습니다.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

각 링크는 동일한 기본 URL로 시작합니다.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

기본 URL끝에 URL 인코딩된 템플릿 링크를 추가합니다.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

링크에 대한 전체 URL이 있습니다.

## <a name="create-deploy-to-azure-button"></a>Azure에 배포 만들기 단추

마지막으로 링크와 이미지를 함께 넣습니다.

GitHub 리포지토리 또는 웹 페이지의 README.md 파일에 마크다운이 있는 단추를 추가하려면 다음을 사용합니다.

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

HTML의 경우 다음을 사용합니다.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>템플릿 배포

전체 솔루션을 테스트하려면 다음 단추를 선택합니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

포털에는 매개 변수 값을 쉽게 제공할 수 있는 창이 표시됩니다. 매개 변수는 템플릿의 기본값으로 미리 채워져 있습니다.

![포털을 사용하여 배포](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>다음 단계

- 템플릿에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해하기를](template-syntax.md)참조하십시오.
