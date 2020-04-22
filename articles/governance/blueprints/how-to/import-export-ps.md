---
title: PowerShell을 사용 하 고 청사진 가져오기 및 내보내기
description: Blueprint 정의를 코드로 작업하는 방법을 알아봅니다. 내보내기 및 가져오기 명령을 사용하여 공유, 소스 제어 및 관리합니다.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686832"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>PowerShell을 사용 하 고 청사진 정의 가져오기 및 내보내기

Azure Blueprint는 Azure 포털을 통해 완전히 관리할 수 있습니다. 조직에서 Azure Blueprint사용을 앞가면서 블루프린트 정의를 관리 코드로 생각하기 시작해야 합니다. 이 개념을 IaC(코드)라고도 합니다. Blueprint 정의를 코드로 처리하면 Azure Portal에서 제공하는 것 이상의 추가 이점을 얻을 수 있습니다. 이러한 혜택은 다음과 같습니다.

- 청사진 정의 공유
- 청사진 정의 백업
- 다른 테넌터 또는 구독에서 청사진 정의 재사용
- 소스 제어에 Blueprint 정의 배치
  - 테스트 환경에서 청사진 정의 자동 테스트
  - 지속적인 통합 및 지속적인 배포(CI/CD) 파이프라인 지원

이유가 무엇이든, 청사진 정의를 코드로 관리하는 것은 이점이 있습니다. 이 문서에서는 [Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) 모듈에서 `Import-AzBlueprintWithArtifact` 및 `Export-AzBlueprintWithArtifact` 명령을 사용하는 방법을 보여 주십니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Blueprint에 대한 적당한 작업 지식을 가정합니다. 아직 수행하지 않은 경우 다음 문서를 통해 작업하십시오.

- [포털에서 청사진 만들기](../create-blueprint-portal.md)
- 배포 [단계](../concepts/deployment-stages.md) 및 [청사진 수명 주기에](../concepts/lifecycle.md) 대해 읽어보기
- PowerShell을 통해 청사진 정의 및 할당 [생성](../create-blueprint-powershell.md) 및 [관리](./manage-assignments-ps.md)

아직 설치되지 않은 경우 [Az.Blueprint 모듈 추가](./manage-assignments-ps.md#add-the-azblueprint-module)를 수행하여 PowerShell 갤러리에서 **Az.Blueprint** 모듈을 설치하고 유효성을 검사합니다.

## <a name="folder-structure-of-a-blueprint-definition"></a>청사진 정의의 폴더 구조

Blueprint내보내기 및 가져오기를 살펴보기 전에 Blueprint 정의를 구성하는 파일이 어떻게 구성되는지 살펴보겠습니다. 청사진 정의는 자체 폴더에 저장해야 합니다.

> [!IMPORTANT]
> cmdlet의 Name 매개 변수에 값이 전달되지 않으면 Blueprint 정의가 저장된 폴더의 이름이 사용됩니다. **Name** `Import-AzBlueprintWithArtifact`

명명되어야 `blueprint.json`하는 Blueprint 정의와 함께 Blueprint 정의가 구성된 아티팩트가 있습니다. 각 아티팩트는 .라는 `artifacts`하위 폴더에 있어야 합니다.
폴더에 있는 JSON 파일로 청사진 정의의 구조는 다음과 같이 표시됩니다.

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>청사진 정의 내보내기

Blueprint 정의를 내보내는 단계는 간단합니다. Blueprint 정의를 내보내는 것은 소스 제어를 공유, 백업 또는 배치하는 데 유용할 수 있습니다.

- **청사진** [필수]
  - 청사진 정의 지정
  - 참조 `Get-AzBlueprint` 개체를 얻는 데 사용
- **출력 경로** [필수]
  - 청사진 정의 JSON 파일을 저장할 경로를 지정합니다.
  - 출력 파일은 Blueprint 정의의 이름이 있는 하위 폴더에 있습니다.
- **버전(선택** 사항)
  - **Blueprint** 참조 개체에 두 개 이상의 버전에 대한 참조가 포함된 경우 출력할 버전을 지정합니다.

1. 청사진 정의에 대한 참조를 받아 다음으로 `{subId}`표시된 구독에서 내보낼 수 있습니다.

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. cmdlet을 `Export-AzBlueprintWithArtifact` 사용하여 지정된 청사진 정의를 내보냅니다.

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>청사진 정의 가져오기

[내보낸 Blueprint 정의가](#export-your-blueprint-definition) 있거나 [필수 폴더 구조에](#folder-structure-of-a-blueprint-definition)수동으로 생성된 Blueprint 정의가 있으면 해당 Blueprint 정의를 다른 관리 그룹 또는 구독으로 가져올 수 있습니다.

기본 제공 Blueprint 정의의 예는 [Azure Blueprint GitHub 리포지토리를](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)참조하십시오.

- **이름** [필수]
  - 새 Blueprint 정의의 이름을 지정합니다.
- **입력 경로** [필수]
  - 청사진 정의를 작성할 경로를 지정합니다.
  - [필요한 폴더 구조와](#folder-structure-of-a-blueprint-definition) 일치해야 합니다.
- **관리그룹Id(선택** 사항)
  - 현재 컨텍스트 기본값이 아닌 경우 Blueprint 정의를 저장하는 관리 그룹 ID
  - **관리 그룹 Id** 또는 **구독ID를** 지정해야 합니다.
- **구독ID(선택** 사항)
  - 현재 컨텍스트 기본값이 아닌 경우 Blueprint 정의를 저장하는 구독 ID
  - **관리 그룹 Id** 또는 **구독ID를** 지정해야 합니다.

1. cmdlet을 `Import-AzBlueprintWithArtifact` 사용하여 지정된 Blueprint 정의를 가져옵니다.

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Blueprint 정의를 가져오면 [PowerShell 에 할당합니다.](./manage-assignments-ps.md#create-blueprint-assignments)

고급 Blueprint 정의를 만드는 자세한 내용은 다음 문서를 참조하십시오.

- [정적 및 동적 매개 변수를](../concepts/parameters.md)사용합니다.
- [Blueprint 순서를](../concepts/sequencing-order.md)사용자 지정합니다.
- Blueprint 리소스 [잠금을 통해 배포를 보호합니다.](../concepts/resource-locking.md)
- [블루프린트를 코드로 관리합니다.](https://github.com/Azure/azure-blueprints/blob/master/README.md)

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결