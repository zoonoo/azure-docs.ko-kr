---
title: Azure Managed Application의 UI 정의 테스트 | Microsoft Docs
description: 포털을 통해 Azure Managed Application을 만들기 위해 사용자 환경을 테스트하는 방법을 설명합니다.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257639"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure Managed Applications에 대 한 포털 사용자 인터페이스 테스트

이후에 [createUiDefinition.json 파일을 만드는](create-uidefinition-overview.md) 관리 되는 응용 프로그램에 대 한 사용자 환경을 테스트 해야 합니다. 테스트를 단순화 하려면 포털에서 파일을 로드 하는 샌드박스 환경을 사용 합니다. 실제로 관리되는 애플리케이션을 배포할 필요는 없습니다. 샌드박스는 현재, 전체 화면 포털 환경에서 사용자 인터페이스를 제공합니다. 또는 레거시 포털 뷰를 사용 하지만 인터페이스를 테스트에 대 한 PowerShell 스크립트를 사용할 수 있습니다. 두 방법은 모두 이 문서에 나와 있습니다. 샌드박스는 인터페이스를 미리 보기에 대 한 권장된 방법입니다.

## <a name="prerequisites"></a>필수 조건

* **createUiDefinition.json** 파일. 이 파일에 없는 경우에 복사 합니다 [샘플 파일](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="use-sandbox"></a>샌드박스를 사용 합니다.

1. 엽니다는 [UI 정의 샌드박스 만들](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)합니다.

   ![샌드박스를 표시 합니다.](./media/test-createuidefinition/show-sandbox.png)

1. 빈 정의 createUiDefinition.json 파일의 내용을 바꿉니다. 선택 **미리 보기**합니다.

   ![미리 보기를 선택 합니다.](./media/test-createuidefinition/select-preview.png)

1. 만든 양식이 표시 됩니다. 사용자 환경을 단계별로 실행 하 고 값을 채울 수 있습니다.

   ![폼이 표시](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>문제 해결

폼을 선택한 후 표시 되지 않는 경우 **미리 보기**, 구문 오류를 할 수 있습니다. 오른쪽의 스크롤 막대에 빨간색 표시기 살펴보고 이동 합니다.

![구문 오류 표시](./media/test-createuidefinition/show-syntax-error.png)

양식에 표시 되지 않습니다. 해체 드롭다운을 사용 하 여 클라우드 아이콘 대신 표시를 폼에 속성이 누락 같은 오류가 발생 합니다. 브라우저에서 웹 개발자 도구를 엽니다. **콘솔**은 인터페이스에 관한 중요 메시지를 표시합니다.

![오류 표시](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>테스트 스크립트를 사용 합니다.

포털에서 인터페이스를 테스트하려면 다음 스크립트 중 하나를 로컬 머신에 복사합니다.

* [PowerShell 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

포털에서 인터페이스 파일을 보려면 다운로드한 스크립트를 실행합니다. 이 스크립트는 Azure 구독에 저장소 게정을 만들고 createUiDefinition.json 파일을 저장소 계정에 업로드합니다. 처음 스크립트를 실행하거나 저장소 계정이 삭제된 경우에만 저장소 계정이 생성됩니다. 저장소 계정이 Azure 구독에 이미 있는 경우 스크립트가 이를 다시 사용합니다. 스크립트에서 포털을 열고 저장소 계정에서 파일을 로드합니다.

저장소 계정의 위치를 제공하고 createUiDefinition.json 파일이 있는 폴더를 지정합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI의 경우 

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

createUiDefinition.json 파일이 스크립트와 동일한 폴더에 있고 저장소 계정을 이미 만든 경우 해당 매개 변수를 제공할 필요가 없습니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Azure CLI의 경우 

```azurecli
./sideload-createuidef.sh
```

이 스크립트는 브라우저에서 새 탭을 엽니다. 관리되는 애플리케이션을 만들기 위한 인터페이스가 있는 포털을 표시합니다.

![포털 보기](./media/test-createuidefinition/view-portal.png)

필드의 값을 제공합니다. 완료되면 값이 템플릿으로 전달됩니다.

![값 표시](./media/test-createuidefinition/show-json.png)

배포 템플릿을 테스트하기 위해 이러한 값을 매개 변수 파일로 사용할 수 있습니다.

포털 요약 화면에서 중단을 하는 경우에 출력 섹션의 버그가 나타날 수 있습니다. 예를 들어, 존재하지 않는 컨트롤을 참조했을 수 있습니다. 출력에서 매개 변수가 비어 있으면 매개 변수 존재 하지 않는 속성을 참조 될 수 있습니다. 예를 들어, 컨트롤에 대한 참조가 유효하지만 속성 참조는 유효하지 않습니다.

## <a name="test-your-solution-files"></a>솔루션 파일 테스트

이제 포털 인터페이스가 예상대로 작동하는 것을 확인했으므로 createUiDefinition 파일이 mainTemplate.json 파일과 제대로 통합되었는지 유효성을 검사할 시점입니다. 유효성 검사 스크립트 테스트를 실행하여 createUiDefinition 파일 등, 솔루션 파일의 콘텐츠를 테스트할 수 있습니다. 이 스크립트는 JSON 구문 유효성을 검사하고 텍스트 필드에서 Regex 식을 검사하며 포털 인터페이스의 출력 값이 템플릿 매개 변수와 ㅇ리치하는지 확인합니다. 이 스크립트 실행에 대한 자세한 내용은 [템플릿에 대해 정적 유효성 검사 실행](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)을 참조하세요.

## <a name="next-steps"></a>다음 단계

포털 인터페이스 유효성 검사 후 [Marketplace에서 Azure Managed Application](publish-marketplace-app.md)을 제공하는 방법을 알아보세요.
