---
title: Azure Managed Application의 UI 정의 테스트 | Microsoft Docs
description: 포털을 통해 Azure Managed Application을 만들기 위해 사용자 환경을 테스트하는 방법을 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043547"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>관리되는 응용 프로그램에 대해 Azure Portal 인터페이스 테스트
Azure Managed Application에 대해 [createUiDefinition.json 파일을 만든 후](create-uidefinition-overview.md) 사용자 환경을 테스트해야 합니다. 간단한 테스트를 위해 파일을 포털에 로드하는 스크립트를 사용합니다. 실제로 관리되는 응용 프로그램을 배포할 필요는 없습니다.

## <a name="prerequisites"></a>필수 조건

* **createUiDefinition.json** 파일. 이 파일이 없으면 [샘플 파일](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json)을 복사하여 로컬에 저장합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="download-test-script"></a>테스트 스크립트 다운로드

포털에서 인터페이스를 테스트하려면 다음 스크립트 중 하나를 로컬 머신에 복사합니다.

* [PowerShell 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>스크립트 실행 

포털에서 인터페이스 파일을 보려면 다운로드한 스크립트를 실행합니다. 이 스크립트는 Azure 구독에 저장소 게정을 만들고 createUiDefinition.json 파일을 저장소 계정에 업로드합니다. 그런 다음, 포털을 열고 저장소 계정에서 파일을 로드합니다.

저장소 계정의 위치를 제공하고 createUiDefinition.json 파일이 있는 폴더를 지정합니다. 처음 스크립트를 실행하거나 저장소 계정이 삭제된 경우에만 저장소 계정 위치를 제공합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

Azure CLI의 경우 

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
```

## <a name="test-your-interface"></a>인터페이스 테스트

이 스크립트는 브라우저에서 새 탭을 엽니다. 관리되는 응용 프로그램을 만들기 위한 인터페이스가 있는 포털을 표시합니다.

![포털 보기](./media/test-createuidefinition/view-portal.png)

필드를 작성하기 전에 브라우저에서 Web Developer Tools를 엽니다. **콘솔**은 인터페이스에 관한 중요 메시지를 표시합니다.

![콘솔 선택](./media/test-createuidefinition/select-console.png)

인터페이스 정의에 오류가 있으면 콘솔에 설명이 표시됩니다.

![오류 표시](./media/test-createuidefinition/show-error.png)

필드의 값을 제공합니다. 완료되면 값이 템플릿으로 전달됩니다.

![값 표시](./media/test-createuidefinition/show-json.png)

## <a name="test-your-solution-files"></a>솔루션 파일 테스트

이제 포털 인터페이스가 예상대로 작동하는 것을 확인했으므로 createUiDefinition 파일이 mainTemplate.json 파일과 제대로 통합되었는지 유효성을 검사할 시점입니다. 유효성 검사 스크립트 테스트를 실행하여 createUiDefinition 파일 등, 솔루션 파일의 콘텐츠를 테스트할 수 있습니다. 이 스크립트는 JSON 구문 유효성을 검사하고 텍스트 필드에서 Regex 식을 검사하며 포털 인터페이스의 출력 값이 템플릿 매개 변수와 ㅇ리치하는지 확인합니다. 이 스크립트 실행에 대한 자세한 내용은 [템플릿에 대해 정적 유효성 검사 실행](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)을 참조하세요.

## <a name="next-steps"></a>다음 단계

포털 인터페이스 유효성 검사 후 [Marketplace에서 Azure Managed Application](publish-marketplace-app.md)을 제공하는 방법을 알아보세요.
