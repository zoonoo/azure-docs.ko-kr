---
title: 포털 템플릿을 템플릿 사양으로 변환
description: Azure Portal 갤러리에서 기존 템플릿을 템플릿 사양으로 변환 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555946"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>포털의 템플릿 갤러리를 템플릿 사양으로 변환

Azure Portal은 계정에 Azure Resource Manager 템플릿 (ARM 템플릿)을 저장 하는 방법을 제공 합니다. 그러나 [템플릿 사양을](template-specs.md) 사용 하면 조직의 사용자와 템플릿을 공유 하 고 다른 템플릿과 연결할 수 있습니다. 이 문서에서는 템플릿 갤러리의 기존 템플릿을 템플릿 사양으로 변환 하는 방법을 보여 줍니다.

변환할 템플릿이 있는지 확인 하려면 [포털에서 템플릿 갤러리](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)를 확인 합니다. 이러한 템플릿에는 리소스 형식이 `Microsoft.Gallery/myareas/galleryitems` 있습니다.

## <a name="convert-with-powershell-script"></a>PowerShell 스크립트를 사용 하 여 변환

템플릿 갤러리에서 템플릿을 쉽게 변환 하려면 Azure 빠른 시작 템플릿 리포지토리의 PowerShell 스크립트를 사용 합니다. 스크립트를 실행할 때 각 템플릿에 대 한 새 템플릿 사양을 만들거나 템플릿 사양을 만드는 템플릿을 다운로드할 수 있습니다. 스크립트는 템플릿 갤러리에서 템플릿을 삭제 하지 않습니다.

1. [마이그레이션 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1)를 복사 합니다. *Migrate-GalleryItems.ps1* 이름으로 로컬 복사본을 저장 합니다.
1. 새 템플릿 사양을 만들려면 및 매개 변수에 대 한 값을 제공 `-ResourceGroupName` `-Location` 합니다. 

   `ItemsToExport` `MyGalleryItems` 템플릿을 내보내려면로 설정 합니다. `AllGalleryItems`액세스 권한이 있는 모든 템플릿을 내보내려면로 설정 합니다.

   다음 예제에서는 **migratedRG** 이라는 리소스 그룹의 각 템플릿에 대 한 새 템플릿 사양을 만듭니다. 이 스크립트는 리소스 그룹을 만듭니다 (없는 경우).

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. 템플릿 사양을 만드는 데 사용할 수 있는 템플릿을 다운로드 하려면 리소스 그룹 또는 위치에 대 한 값을 제공 하지 마세요. 대신를 지정 `-ExportToFile` 합니다. 템플릿은 갤러리의 템플릿과 동일 하지 않습니다. 대신 템플릿에 대 한 템플릿 사양을 만드는 템플릿 사양 리소스가 포함 되어 있습니다.

   다음 예제에서는 템플릿 사양을 만들지 않고 템플릿을 다운로드 합니다.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   템플릿 사양을 만드는 템플릿을 배포 하는 방법에 대 한 자세한 내용은 [빠른 시작: 템플릿 사양 만들기 및 배포 (미리 보기)](quickstart-create-template-specs.md)를 참조 하세요.

스크립트 및 해당 매개 변수에 대 한 자세한 내용은 [템플릿 갤러리 템플릿에서 TemplateSpecs 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)를 참조 하세요.

## <a name="manually-convert-through-portal"></a>포털을 통해 수동으로 변환

갤러리에서 새 템플릿 사양으로 템플릿을 수동으로 복사할 수 있습니다.

1. 포털에서 [템플릿 (미리 보기)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) 을 엽니다.
1. 마이그레이션할 템플릿을 선택 합니다.
1. **템플릿 보기** 를 선택 합니다.
1. 템플릿 콘텐츠를 복사 합니다.
1. 포털 검색 창에서 **템플릿 사양을** 검색 합니다. 해당 옵션을 선택 합니다.
1. **템플릿 사양 만들기** 를 선택합니다.
1. 이름, 구독, 리소스 그룹, 위치 및 버전에 대 한 값을 제공 합니다.
1. **다음: 템플릿 편집** 을 선택 합니다.
1. 템플릿의 내용에 대해 템플릿 갤러리에서 복사한 템플릿을 붙여넣습니다.
1. **검토 + 만들기** 를 선택합니다.
1. 유효성 검사가 성공적으로 완료 되 면 **만들기** 를 선택 합니다.

조직에서 템플릿 사양을 다른 사용자와 공유 해야 하는 경우 액세스 권한이 필요한 그룹이 나 사용자에 게 [역할 기반 액세스 제어를 설정](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) 합니다.

## <a name="next-steps"></a>다음 단계

템플릿 사양에 대해 자세히 알아보려면 [템플릿 사양 만들기 및 배포](template-specs.md)를 참조 하세요.
