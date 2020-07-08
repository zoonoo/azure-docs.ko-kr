---
title: UI 정의 파일 테스트
description: 포털을 통해 Azure Managed Application을 만들기 위해 사용자 환경을 테스트하는 방법을 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78250186"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure Managed Applications에 대 한 포털 인터페이스 테스트

관리 되는 응용 프로그램에 대 한 [파일에 createUiDefinition.js를 만든](create-uidefinition-overview.md) 후 사용자 환경을 테스트 해야 합니다. 테스트를 간소화 하려면 포털에서 파일을 로드 하는 샌드박스 환경을 사용 합니다. 실제로 관리되는 애플리케이션을 배포할 필요는 없습니다. 샌드박스는 현재 전체 화면 포털 환경에서 사용자 인터페이스를 제공 합니다. 또는 인터페이스를 테스트 하는 데 스크립트를 사용할 수 있습니다. 두 방법은 모두 이 문서에 나와 있습니다. 샌드박스는 인터페이스를 미리 보는 데 권장 되는 방법입니다.

## <a name="prerequisites"></a>사전 요구 사항

* **createUiDefinition.json** 파일. 이 파일이 없으면 [샘플 파일](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)을 복사 합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="use-sandbox"></a>샌드박스 사용

1. [UI 정의 만들기 샌드박스](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)를 엽니다.

   ![샌드박스 표시](./media/test-createuidefinition/show-sandbox.png)

1. 빈 정의를 파일에 createUiDefinition.js의 콘텐츠로 바꿉니다. **미리 보기**를 선택합니다.

   ![미리 보기 선택](./media/test-createuidefinition/select-preview.png)

1. 만든 양식이 표시 됩니다. 사용자 환경을 단계별로 실행 하 고 값을 입력할 수 있습니다.

   ![폼 표시](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>문제 해결

**미리 보기**를 선택한 후 양식이 표시 되지 않으면 구문 오류가 발생할 수 있습니다. 오른쪽 스크롤 막대에서 빨간색 표시기를 찾아 이동 합니다.

![구문 오류 표시](./media/test-createuidefinition/show-syntax-error.png)

양식이 표시 되지 않고, 대신 삭제를 사용 하는 클라우드의 아이콘이 표시 되는 경우 누락 된 속성과 같은 오류가 폼에 표시 됩니다. 브라우저에서 웹 개발자 도구를 엽니다. **콘솔**은 인터페이스에 관한 중요 메시지를 표시합니다.

![오류 표시](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>테스트 스크립트 사용

포털에서 인터페이스를 테스트하려면 다음 스크립트 중 하나를 로컬 머신에 복사합니다.

* [PowerShell side-by-side 로드 스크립트-Az Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell side-by-side 로드 스크립트-Azure 모듈](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

포털에서 인터페이스 파일을 보려면 다운로드한 스크립트를 실행합니다. 이 스크립트는 Azure 구독에 스토리지 게정을 만들고 createUiDefinition.json 파일을 스토리지 계정에 업로드합니다. 처음 스크립트를 실행하거나 스토리지 계정이 삭제된 경우에만 스토리지 계정이 생성됩니다. 스토리지 계정이 Azure 구독에 이미 있는 경우 스크립트가 이를 다시 사용합니다. 스크립트에서 포털을 열고 스토리지 계정에서 파일을 로드합니다.

스토리지 계정의 위치를 제공하고 createUiDefinition.json 파일이 있는 폴더를 지정합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI의 경우

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

createUiDefinition.json 파일이 스크립트와 동일한 폴더에 있고 스토리지 계정을 이미 만든 경우 해당 매개 변수를 제공할 필요가 없습니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI의 경우

```bash
./sideload-createuidef.sh
```

이 스크립트는 브라우저에서 새 탭을 엽니다. 관리되는 애플리케이션을 만들기 위한 인터페이스가 있는 포털을 표시합니다.

필드의 값을 제공합니다. 완료 되 면 브라우저의 개발자 도구 콘솔에서 찾을 수 있는 템플릿에 전달 되는 값이 표시 됩니다.

![값 표시](./media/test-createuidefinition/show-json.png)

배포 템플릿을 테스트하기 위해 이러한 값을 매개 변수 파일로 사용할 수 있습니다.

포털이 요약 화면에서 멈추면 출력 섹션에 버그가 있을 수 있습니다. 예를 들어, 존재하지 않는 컨트롤을 참조했을 수 있습니다. 출력의 매개 변수가 비어 있는 경우 매개 변수는 존재 하지 않는 속성을 참조 하는 것일 수 있습니다. 예를 들어, 컨트롤에 대한 참조가 유효하지만 속성 참조는 유효하지 않습니다.

## <a name="test-your-solution-files"></a>솔루션 파일 테스트

이제 포털 인터페이스가 예상대로 작동하는 것을 확인했으므로 createUiDefinition 파일이 mainTemplate.json 파일과 제대로 통합되었는지 유효성을 검사할 시점입니다. 유효성 검사 스크립트 테스트를 실행하여 createUiDefinition 파일 등, 솔루션 파일의 콘텐츠를 테스트할 수 있습니다. 이 스크립트는 JSON 구문 유효성을 검사하고 텍스트 필드에서 Regex 식을 검사하며 포털 인터페이스의 출력 값이 템플릿 매개 변수와 ㅇ리치하는지 확인합니다. 이 스크립트 실행에 대한 자세한 내용은 [템플릿에 대해 정적 유효성 검사 실행](https://github.com/Azure/azure-quickstart-templates/tree/master/test)을 참조하세요.

## <a name="next-steps"></a>다음 단계

포털 인터페이스 유효성 검사 후 [Marketplace에서 Azure Managed Application](publish-marketplace-app.md)을 제공하는 방법을 알아보세요.
