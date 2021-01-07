---
title: '자습서: GitHub를 사용하여 Azure Policy as Code 구현'
description: 이 자습서에서는 내보내기, GitHub 작업 및 GitHub 워크플로를 사용하여 Azure Policy as Code를 구현합니다.
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325902"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>자습서: GitHub를 사용하여 Azure Policy as Code 구현

Azure Policy as Code 워크플로를 사용하면 정책 정의 및 할당을 코드로 관리하고, 정의를 업데이트하는 수명 주기를 제어하고, 규정 준수 결과의 유효성 검사를 자동화할 수 있습니다. 이 자습서에서는 GitHub에서 Azure Policy 기능을 사용하여 수명 주기 프로세스를 빌드하는 방법에 대해 알아봅니다. 이러한 태스크는 다음과 같습니다.

> [!div class="checklist"]
> - 정책 정의 및 할당을 GitHub로 내보내기
> - GitHub에서 업데이트된 정책 개체를 Azure로 푸시
> - GitHub 작업에서 규정 준수 검사 트리거

기존 리소스의 현재 규정 준수 상태를 식별하는 정책을 할당하려는 경우 이 빠른 시작 문서를 통해 방법을 살펴보세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- [Azure Policy as Code 워크플로 디자인](../concepts/policy-as-code.md)을 검토하여 이 자습서에 사용되는 디자인 패턴을 이해합니다.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Azure Portal에서 Azure Policy 개체 내보내기

Azure Portal에서 정책 정의를 내보내려면 다음 단계를 수행합니다.

1. **모든 서비스** 를 클릭한 후 **정책** 을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. Azure Policy 페이지의 왼쪽에서 **정의** 를 선택합니다.

1. **정의 내보내기** 단추를 사용하거나 정책 정의 행에서 줄임표를 선택한 다음, **정의 내보내기** 를 선택합니다.

1. **GitHub로 로그인** 단추를 선택합니다. 리소스를 내보내도록 Azure Policy에 권한을 부여하기 위해 아직 GitHub에 인증하지 않은 경우 새로 열리는 창에서 [GitHub 작업](https://github.com/features/actions)에 액세스를 검토하고 **AzureGitHubActions 권한 부여** 를 선택하여 내보내기 프로세스를 계속합니다. 완료되면 새 창이 자동으로 닫힙니다.

1. **기본 사항** 탭에서 다음 옵션을 설정하고, 페이지 아래쪽에서 **정책** 탭 또는 **다음: 정책** 단추를 선택합니다.

   - **리포지토리 필터** : 내 소유의 리포지토리만 보려면 _내 리포지토리_ 로 설정하고, GitHub 작업 액세스 권한을 부여한 모든 리포지토리를 보려면 _모든 리포지토리_ 로 설정합니다.
   - **리포지토리** : Azure Policy 리소스를 내보낼 리포지토리로 설정합니다.
   - **분기** : 리포지토리의 분기를 설정합니다. 기본 분기가 아닌 다른 분기를 사용하면 소스 코드에 추가로 병합하기 전에 업데이트의 유효성을 검사할 수 있습니다.
   - **디렉터리** : Azure Policy 리소스를 내보낼 _루트 수준 폴더_ 입니다. 이 디렉터리의 하위 폴더는 내보낸 리소스에 따라 만들어집니다.

1. **정책** 탭에서 줄임표를 선택하고 관리 그룹, 구독 또는 리소스 그룹의 조합을 선택하여 검색 범위를 설정합니다.
   
1. **정책 정의 추가** 단추를 사용하여 내보낼 개체 범위를 검색합니다. 열리는 측면 창에서 내보낼 각 개체를 선택합니다. 검색 상자 또는 유형을 사용하여 선택 항목을 필터링합니다. 모든 개체를 내보내기로 선택한 경우 페이지 맨 아래에 있는 **추가** 단추를 사용합니다.

1. 선택한 각 개체의 정책 정의에 대해 원하는 내보내기 옵션을 선택합니다(예: _정의만_ 또는 _정의 및 할당_ ). 그런 다음, **검토 + 내보내기** 탭을 선택하거나 페이지 맨 아래에서 **다음: 검토 + 내보내기** 단추를 선택합니다.

   > [!NOTE]
   > _정의 및 할당_ 옵션을 선택하면 정책 정의를 추가할 때 필터에 설정된 범위 내의 정책 할당만 내보내집니다.

1. **검토 + 내보내기** 탭에서 세부 정보가 일치하는지 확인한 다음, 페이지 맨 아래에 있는 **내보내기** 단추를 사용합니다.

1. GitHub 리포지토리, 분기 및 _루트 수준 폴더_ 를 확인하여 선택한 리소스가 소스 제어로 내보내지고 있는지 살펴봅니다.

Azure Policy 리소스는 선택한 GitHub 리포지토리 및 _루트 수준 폴더_ 내부의 다음 구조로 내보내집니다.

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>GitHub에서 업데이트된 정책 개체를 Azure로 푸시

1. 정책 개체를 내보낼 때 작업을 시작하기 위해 `.github/workflows/manage-azure-policy-<randomLetters>.yml`이라는 [GitHub 워크플로](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) 파일도 만들어집니다.

   > [!NOTE]
   > GitHub 워크플로 파일은 내보내기가 사용될 때마다 생성됩니다. 파일의 각 인스턴스는 해당 내보내기 작업 중에 옵션에만 적용됩니다.

1. 이 워크플로 파일은 [Azure Policy 관리](https://github.com/marketplace/actions/manage-azure-policy) 작업을 사용하여 GitHub 리포지토리의 내보낸 정책 개체에 수행된 변경 내용을 Azure Policy로 다시 푸시합니다. 기본적으로 이 작업은 Azure의 기존 파일과 다른 파일만 감안하고 동기화합니다. `assignments` 매개 변수를 작업에 사용하여 특정 할당 파일의 변경 내용만 동기화할 수도 있습니다. 이 매개 변수는 특정 환경에만 정책 할당을 적용하는 데 사용할 수 있습니다. 자세한 내용은 [Azure Policy 리포지토리 관리 추가 정보](https://github.com/Azure/manage-azure-policy)를 참조하세요.

1. 기본적으로 워크플로는 수동으로 트리거해야 합니다. 이렇게 하려면 GitHub에서 **작업** 을 사용하고, `manage-azure-policy-<randomLetters>` 워크플로를 선택하고, **워크플로 실행** 을 선택한 다음, **워크플로 실행** 을 다시 선택합니다.

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="GitHub 웹 인터페이스의 [작업] 탭, 워크플로 및 [워크플로 실행] 단추의 스크린샷":::

   > [!NOTE]
   > 워크플로 파일을 탐지하고 수동으로 실행하려면 워크플로 파일이 기본 분기에 있어야 합니다.

1. 이 워크플로는 정책 개체의 변경 내용을 Azure와 동기화하고 로그에 상태를 제공합니다.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="GitHub 웹 인터페이스의 [작업] 탭, 워크플로 및 [워크플로 실행] 단추의 스크린샷":::

1. 또한 이 워크플로는 추적할 Azure Policy 개체 `properties.metadata`의 세부 정보를 추가합니다.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="GitHub 웹 인터페이스의 [작업] 탭, 워크플로 및 [워크플로 실행] 단추의 스크린샷":::

### <a name="trigger-compliance-scans-using-github-action"></a>GitHub 작업을 사용하여 규정 준수 검사 트리거

[Azure Policy 규정 준수 검사 작업](https://github.com/marketplace/actions/azure-policy-compliance-scan)을 사용하면 하나 또는 여러 리소스, 리소스 그룹 또는 구독의 [GitHub 워크플로](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)에서 주문형 규정 준수 평가 검사를 트리거하고, 해당 리소스의 규정 준수 상태에 따라 워크플로 경로를 변경할 수 있습니다. 또한 예정된 시간에 실행되어 편리한 시간에 최신 규정 준수 상태를 가져오도록 워크플로를 구성할 수 있습니다. 필요에 따라 이 GitHub 작업은 추가 분석 또는 보관을 위해 검사한 리소스의 규정 준수 상태에 대한 보고서를 생성할 수도 있습니다.

다음 예제에서는 구독의 규정 준수 검사를 실행합니다. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>검토

이 자습서에서는 다음 작업을 성공적으로 완료했습니다.

> [!div class="checklist"]
> - GitHub로 정책 정의 및 할당을 내보냈습니다.
> - GitHub에서 업데이트된 정책 개체를 Azure로 푸시했습니다.
> - GitHub 작업에서 규정 준수 검사를 트리거했습니다.

## <a name="next-steps"></a>다음 단계

정책 정의의 구조에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Policy 정의 구조](../concepts/definition-structure.md)