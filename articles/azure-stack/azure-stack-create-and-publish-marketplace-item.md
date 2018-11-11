---
title: 만들기 및 Azure Stack의 마켓플레이스 항목 게시 | Microsoft Docs
description: 만들기 및 Azure Stack의 마켓플레이스 항목을 게시 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: c07e1282a755962b6fe6bc980207a510bd3287a4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253774"
---
# <a name="create-and-publish-a-marketplace-item"></a>Marketplace 항목 만들기 및 게시

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

## <a name="create-a-marketplace-item"></a>Marketplace 항목 만들기
1. [다운로드](https://www.aka.ms/azurestackmarketplaceitem) Azure 갤러리 Packager 도구 및 샘플 Azure Stack Marketplace 항목.
2. 샘플 마켓플레이스 항목을 열고 이름을 변경 합니다 **SimpleVMTemplate** 폴더입니다. (Marketplace 항목과-예를 들어, 동일한 이름을 사용 **Contoso.TodoList**.) 이 폴더에는 다음이 포함 됩니다.
   
   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Azure 리소스 관리자 템플릿을 만들거나](../azure-resource-manager/resource-group-authoring-templates.md) GitHub에서 템플릿을 선택합니다. 마켓플레이스 항목이 서식이 파일을 사용 하 여 리소스를 만듭니다.

    > [!Note]  
    > 하드 코드 하지 마십시오 제품 키, 암호 또는 Azure Resource Manager 템플릿에서 고객 식별이 가능한 정보와 같은 모든 비밀입니다. 템플릿 json 파일을 갤러리에 게시 되 면 인증 없이 액세스할 수 있습니다.  모든 비밀을 저장할 [Key Vault](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-keyvault-parameter) 템플릿 내에서 호출 합니다.

4. 리소스를 성공적으로 배포 될 수 있음을 확인 하려면 Microsoft Azure Stack Api를 사용 하 여 템플릿을 테스트 합니다.
5. 템플릿에 가상 머신 이미지를 사용 하는 경우 지침에 따라 [가상 머신 이미지를 Azure Stack에 추가](azure-stack-add-vm-image.md)합니다.
6. Azure Resource Manager 템플릿을 저장 합니다 **/Contoso.TodoList/DeploymentTemplates/** 폴더입니다.
7. Marketplace 항목에 대 한 텍스트와 아이콘을 선택 합니다. 아이콘을 추가 합니다 **아이콘** 폴더를 텍스트를 추가 하 고는 **리소스** 파일를 **문자열** 폴더입니다. 아이콘에 대해 작게, 보통, 크게 및 넓게의 명명 규칙을 사용합니다. 참조 [Marketplace 항목 UI 참조](#reference-marketplace-item-ui) 대 한 자세한 설명은 합니다.
   
   > [!NOTE]
   > 모든 4 개의 아이콘 크기 (소형, 중간, 대규모, 넓은)는 마켓플레이스 항목을 올바르게 빌드하기 위한 필요 합니다.
   > 
   > 
8. 에 **은 manifest.json** 파일에서 변경 **이름** Marketplace 항목의 이름입니다. 변경할 수도 **게시자** 이름 또는 회사입니다.
9. 아래 **아티팩트**, 변경 **이름** 및 **경로** 포함 된 Azure Resource Manager 템플릿에 대 한 정보가 수정 되었습니다.
   
   ```json
   "artifacts": [
      {
          "name": "Type your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\Type your path",
          "isDefault": true
      }
   ```

10. 바꿉니다 **My Marketplace Items** 마켓플레이스 항목이 표시 됩니다는 범주의 목록을 사용 하 여 합니다.
    
   ```json
   "categories":[
   "My Marketplace Items"
   ],
   ```

11. manifest.json에 추가 편집 내용을 참조 하세요 [참조: Marketplace 항목은 manifest.json](#reference-marketplace-item-manifestjson)합니다.
12. .Azpkg 파일로 폴더를 패키지, 명령 프롬프트를 열고 하 고 다음 명령을 실행:
    
   ```shell
   AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
   ```
    
    > [!NOTE]
    > 전체 경로를 출력 패키지가 있어야 합니다. 예를 들어, 출력 경로가 C:\MarketPlaceItem\yourpackage.azpkg C:\MarketPlaceItem 폴더 존재 해야 합니다.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Marketplace 항목 게시
1. Marketplace 항목 (.azpkg) Azure Blob storage에 업로드 하려면 PowerShell 또는 Azure Storage 탐색기를 사용 합니다. 로컬 Azure Stack 저장소에 업로드할 수도 있고 Azure Storage에 업로드할 수 있습니다. (이 경우 패키지에 대 한 임시 위치) Blob이 공개적으로 액세스할 수 있는지 확인 합니다.
2. Microsoft Azure Stack 환경에서 클라이언트 가상 컴퓨터에서 PowerShell 세션이 서비스 관리자 자격 증명으로 설정 되어 있는지를 확인 합니다. Azure stack에서 PowerShell을 인증 하는 방법에 대 한 지침을 찾을 수 있습니다 [PowerShell 사용 하 여 템플릿 배포](user/azure-stack-deploy-template-powershell.md)합니다.
3. 사용 하는 경우 [PowerShell 1.3.0]( azure-stack-powershell-install.md) 나중에 사용할 수 있습니다 합니다 **추가-AzsGalleryItem** Azure Stack에 마켓플레이스 항목 게시에 대 한 PowerShell cmdlet. PowerShell 1.3.0 사용 하기 전에 cmdlet을 사용 하 여 **Add-azurermgalleryitem** 대신 **추가-AzsGalleryItem**합니다.  예를 들어, 사용 하는 경우 PowerShell 1.3.0 이상:
   
   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   ```
   
   | 매개 변수 | 설명 |
   | --- | --- |
   | 구독 ID |관리자 구독 id입니다. PowerShell을 사용 하 여 검색할 수 있습니다. 포털에서 다운로드 하려는 경우 공급자 구독으로 이동 하 고 구독 ID 복사 |
   | GalleryItemUri |Blob 저장소에 이미 업로드 된 갤러리 패키지에 대 한 URI입니다. |
   | Apiversion |으로 설정할 **2015-04-01**합니다. |
4. 포털로 이동 합니다. 이제는 연산자 또는 사용자 포털에서 마켓플레이스 항목을 볼 수 있습니다.
   
   > [!NOTE]
   > 패키지에는 나타나는 데 몇 분 정도 걸릴 수 있습니다.
   > 
   > 
5. 이제 Azure Stack Marketplace를 마켓플레이스 항목이 저장 되었습니다. Blob 저장소 위치에서 삭제할 수 있습니다.
    > [!Caution]  
    > 모든 기본 갤러리 아티팩트 및 사용자 지정 갤러리 아티팩트 다음 Url에서 인증 없이 액세스할 수 됩니다.  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`  
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`  
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. 사용 하 여 마켓플레이스 항목을 제거할 수 있습니다 합니다 **Remove-azurermgalleryitem** cmdlet. 예제:
   
   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   ```
   
   > [!NOTE]
   > Marketplace UI 항목을 제거한 후 오류가 표시 될 수 있습니다. 오류를 해결 하려면 **설정을** 포털에서 합니다. 그런 다음 선택 **수정 취소** 아래에서 **포털 사용자 지정**합니다.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Marketplace 항목은 manifest.json 참조:
### <a name="identity-information"></a>ID 정보
| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| 이름 |X |문자열 |[A-Za-z0-9]+ | |
| 게시자 |X |문자열 |[A-Za-z0-9]+ | |
| 버전 |X |문자열 |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| DisplayName |X |문자열 |권장 사항 80 자 |80 자 보다 긴 경우 포털 항목 이름을 정상적으로 표시 하지 않을 수 있습니다. |
| PublisherDisplayName |X |문자열 |권장 사항 30 자 |30 자 보다 긴 경우 포털 게시자 이름을 정상적으로 표시 하지 않을 수 있습니다. |
| PublisherLegalName |X |문자열 |최대 256 자 | |
| 요약 |X |문자열 |60-100자 | |
| LongSummary |X |문자열 |140-256자 |적용 되지 않습니다 Azure Stack에서. |
| 설명 |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |5,000 대 500 자 | |

### <a name="images"></a>이미지
Marketplace에서는 다음 아이콘을 사용합니다.

| 이름 | 너비 | 높이 | 메모 |
| --- | --- | --- | --- |
| 와이드 |255 픽셀 |115 픽셀 |항상 필요 |
| 큰 |115 픽셀 |115 픽셀 |항상 필요 |
| 중간 |90 픽셀 |90 픽셀 |항상 필요 |
| 작음 |40 픽셀 |40 픽셀 |항상 필요 |
| 스크린샷 |533 px |32 픽셀 |옵션 |

### <a name="categories"></a>범주
각 마켓플레이스 항목은 포털 UI에 표시 되는 항목을 식별 하는 범주를 사용 하 여 태그를 지정 해야 합니다. Azure Stack에서 기존 범주 중 하나를 선택할 수 있습니다 (계산, 데이터 + 저장소 등) 또는 새 암호를 선택 합니다.

### <a name="links"></a>링크
각 마켓플레이스 항목에는 다양 한 추가 콘텐츠 링크를 포함할 수 있습니다. 링크는 이름 및 URI 목록으로 지정됩니다.

| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| DisplayName |X |문자열 |최대 64 자 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>추가 속성
이전 메타 데이터 외에도 마켓플레이스 작성자는 다음과 같은 형태로 사용자 지정 키/값 쌍 데이터를 제공할 수 있습니다.

| 이름 | 필수 | type | 제약 조건 | 설명 |
| --- | --- | --- | --- | --- |
| DisplayName |X |문자열 |최대 25 자 | |
| 값 |X |문자열 |최대 30 자 | |

### <a name="html-sanitization"></a>HTML 삭제
HTML을 허용 하는 모든 필드에 대해 다음 요소와 특성이 허용 됩니다.

h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i

## <a name="reference-marketplace-item-ui"></a>Marketplace 항목 UI 참조:
아이콘 및 Azure Stack 포털에 표시 된 대로 Marketplace 항목에 대 한 텍스트는 다음과 같습니다.

### <a name="create-blade"></a>블레이드 만들기
![블레이드 만들기](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace 항목 세부 정보 블레이드
![Marketplace 항목 세부 정보 블레이드](media/azure-stack-marketplace-item-ui-reference/image3.png)

