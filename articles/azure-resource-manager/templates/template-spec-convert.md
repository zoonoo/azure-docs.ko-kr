---
title: 포털 템플릿을 템플릿 사양으로 변환
description: Azure Portal 갤러리의 기존 템플릿을 템플릿 사양으로 변환하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: ad0391b91def898d252c429040380eab40522cd8
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378469"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>포털의 템플릿 갤러리를 템플릿 사양으로 변환

Azure Portal 계정에 ARM 템플릿(Azure Resource Manager 템플릿)을 저장하는 방법을 제공합니다. 그러나 [템플릿 사양](template-specs.md)은 조직의 사용자와 템플릿을 공유하고 다른 템플릿과 연결할 수 있는 더 쉬운 방법을 제공합니다. 이 문서에서는 템플릿 갤러리의 기존 템플릿을 템플릿 사양으로 변환하는 방법을 보여 줍니다.

변환할 템플릿이 있는지 확인하려면 [포털의 템플릿 갤러리](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)를 확인하세요. 이러한 템플릿의 리소스 유형은 `Microsoft.Gallery/myareas/galleryitems`입니다.

## <a name="convert-with-powershell-script"></a>PowerShell 스크립트를 사용하여 변환

템플릿 갤러리에서 템플릿 변환을 간소화하려면 Azure 빠른 시작 템플릿 리포지터의 PowerShell 스크립트를 사용합니다. 스크립트를 실행할 때 각 템플릿에 대한 새 템플릿 사양을 만들거나 템플릿 사양을 만드는 템플릿을 다운로드할 수 있습니다. 스크립트는 템플릿 갤러리에서 템플릿을 삭제하지 않습니다.

1. [마이그레이션 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.resources/templatespec-migrate-create/Migrate-GalleryItems.ps1)를 복사합니다. *Migrate-GalleryItems.ps1* 이라는 이름으로 로컬 사본을 저장합니다.
1. 새 템플릿 사양을 만들려면 `-ResourceGroupName` 및 `-Location` 매개 변수에 대한 값을 제공합니다.

   템플릿을 내보내려면 `ItemsToExport`을 `MyGalleryItems`로 설정합니다. 액세스할 수 있는 모든 템플릿을 내보내려면 `AllGalleryItems`로 설정합니다.

   다음 예에서는 **migratedRG** 라는 리소스 그룹의 각 템플릿에 대한 새 템플릿 사양을 만듭니다. 스크립트는 리소스 그룹이 없으면 생성합니다.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. 템플릿 사양을 만드는 데 사용할 수 있는 템플릿을 다운로드하려면 리소스 그룹 또는 위치에 대한 값을 제공하지 마세요. 대신 `-ExportToFile`을 지정합니다. 템플릿은 갤러리의 템플릿과 동일하지 않습니다. 대신 템플릿에 대한 템플릿 사양을 만드는 템플릿 사양 리소스가 포함되어 있습니다.

   다음 예제에서는 템플릿 사양을 만들지 않고 템플릿을 다운로드합니다.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   템플릿 사양을 생성하는 템플릿을 배포하는 방법을 알아보려면 [빠른 시작: 템플릿 사양 생성 및 배포](quickstart-create-template-specs.md)를 참조하세요.

스크립트 및 해당 매개 변수에 대한 자세한 내용은 [템플릿 갤러리 템플릿에서 TemplateSpecs 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.resources/templatespec-migrate-create)를 참조하세요.

## <a name="manually-convert-through-portal"></a>포털을 통해 수동으로 변환

갤러리에서 새 템플릿 사양으로 템플릿을 수동으로 복사할 수 있습니다.

1. 포털에서 [템플릿(미리 보기)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)을 엽니다.
1. 마이그레이션할 템플릿을 선택합니다.
1. **템플릿보기** 를 선택합니다.
1. 템플릿 콘텐츠를 복사합니다.
1. 포털 검색 창에서 **템플릿 사양** 을 검색합니다. 해당 옵션을 선택합니다.
1. **템플릿 사양 만들기** 를 선택합니다.
1. 이름, 구독, 리소스 그룹, 위치 및 버전에 대한 값을 제공합니다.
1. **다음: 템플릿 편집** 을 선택합니다.
1. 템플릿의 내용에 대해 템플릿 갤러리에서 복사한 템플릿을 붙여넣습니다.
1. **검토 + 만들기** 를 선택합니다.
1. 유효성 검사가 성공적으로 완료되면 **만들기** 를 선택합니다.

조직의 다른 사용자와 템플릿 사양을 공유해야 하는 경우 액세스가 필요한 그룹 또는 사용자에게 [역할 기반 액세스 제어를 설정](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)합니다.

## <a name="next-steps"></a>다음 단계

템플릿 사양에 대한 자세한 내용은 [템플릿 사양 생성 및 배포](template-specs.md)를 참조하세요.
