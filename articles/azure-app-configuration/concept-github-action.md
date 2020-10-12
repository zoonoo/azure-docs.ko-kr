---
title: App Configuration에 GitHub 리포지토리 동기화
description: GitHub 리포지토리를 업데이트할 때 GitHub Actions를 사용하여 App Configuration 인스턴스를 자동으로 업데이트합니다.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 66d0e32e7dfdd5ab2abee5108ac8ce54c5222747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371824"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>App Configuration에 GitHub 리포지토리 동기화

기존 원본 제어 사례를 계속 사용하려는 팀은 GitHub Actions를 사용하여 GitHub 리포지토리를 App Configuration 저장소와 자동으로 동기화할 수 있습니다. 이렇게 하면 일반적인 방식으로 구성 파일을 변경할 수 있으며 다음과 같은 App Configuration 혜택을 얻을 수 있습니다. <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   코드 외부의 중앙 집중식 구성 <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   전체 앱을 다시 배포하지 않고 구성 업데이트 <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   Azure App Service 및 Functions 같은 서비스와 통합. 

GitHub Actions [워크플로](https://help.github.com/articles/about-github-actions#workflow)는 GitHub 리포지토리에서 자동화된 프로세스를 정의합니다. ‘Azure App Configuration 동기화’ 작업은 원본 리포지토리가 변경될 때 App Configuration 인스턴스에 대한 업데이트를 트리거합니다. 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일을 사용하여 단계 및 매개 변수를 정의합니다. 앱 코드로 수행하는 것처럼 앱 구성 파일을 푸시, 검토 또는 분기하는 경우 구성 업데이트를 트리거할 수 있습니다.

GitHub [문서](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow)에서는 GitHub 워크플로 및 작업의 자세한 보기를 제공합니다. 

## <a name="enable-github-actions-in-your-repository"></a>리포지토리에서 GitHub Actions 사용
이 GitHub 동작 사용을 시작 하려면 리포지토리로 이동 하 여 **작업** 탭을 선택 합니다. **새 워크플로**를 선택한 다음 **직접 워크플로를 설정**합니다. 마지막으로 마켓플레이스에서 “Azure App Configuration 동기화”를 검색합니다.
> [!div class="mx-imgBorder"]
> ![작업 탭 선택](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![앱 구성 동기화 작업 선택](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>푸시 후 구성 파일 동기화
이 작업은 변경 내용이 `appsettings.json`에 푸시될 때 Azure App Configuration 파일을 동기화합니다. 개발자가 `appsettings.json`의 변경 내용을 푸시하면 App Configuration 동기화 작업은 App Configuration 인스턴스를 새 값으로 업데이트합니다.

이 워크플로의 첫 번째 섹션에서는 ‘마스터’ 분기에 대한 `appsettings.json`을 포함하는 ‘푸시’를 ‘실행’하도록 지정합니다.   두 번째 섹션에는 작업이 트리거된 후 실행되는 작업이 나열됩니다. 이 작업은 관련 파일을 체크 아웃하고 리포지토리에 비밀로 저장된 연결 문자열을 사용하여 App Configuration 인스턴스를 업데이트합니다.  GitHub에서 비밀을 사용하는 방법에 관한 자세한 내용은 암호화된 비밀 만들기 및 사용에 관한 [GitHub 문서](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)를 참조하세요.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>strict 동기화 사용
기본적으로 GitHub 작업은 strict 모드를 사용하도록 설정하지 않습니다. 즉, 동기화는 구성 파일의 키-값을 App Configuration 인스턴스에 추가하기만 합니다(키-값 쌍이 삭제되지 않음). Strict 모드를 사용하면 구성 파일에 없는 키-값 쌍이 구성 파일과 일치하도록 App Configuration 인스턴스에서 삭제됩니다. 여러 원본에서 동기화하거나 App Configuration과 함께 Azure Key Vault를 사용하는 경우 다른 파일에서 구성 설정을 초기화하지 않도록 strict 동기화에서 다른 접두사 또는 레이블을 사용할 수 있습니다(아래 샘플 참조). 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>하나의 작업에서 여러 파일 동기화 

구성이 여러 파일에 있는 경우 아래 패턴을 사용하여 파일 중 하나를 수정할 때 동기화를 트리거할 수 있습니다. 이 패턴은 glob 라이브러리를 사용 합니다 https://www.npmjs.com/package/glob . 구성 파일 이름에 쉼표가 포함 되어 있으면 백슬래시를 사용 하 여 쉼표를 이스케이프할 수 있습니다. 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>접두사 또는 레이블로 동기화
동기화 작업에서 접두사 또는 레이블을 지정하면 특정 세트만 동기화됩니다. 이 동작은 여러 파일에서 strict 동기화를 사용하는 경우 중요합니다. 구성을 설정하는 방법에 따라 접두사 또는 레이블을 각 파일에 연결한 후에 각 접두사 또는 레이블을 개별적으로 동기화할 수 있으므로 아무것도 덮어쓰지 않습니다. 일반적으로 접두사는 다양한 애플리케이션 또는 서비스에 사용되며 레이블은 다양한 환경에 사용됩니다. 

접두사로 동기화: 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

레이블로 동기화: 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>동기화 시 동적 레이블 사용
다음 작업은 동기화될 때마다 동적 레이블을 삽입하므로 각 동기화를 고유하게 식별할 수 있으며 코드 변경 내용을 구성 변경 내용에 매핑할 수 있습니다.

이 워크플로의 첫 번째 섹션에서는 ‘마스터’ 분기에 대한 `appsettings.json`을 포함하는 ‘푸시’를 ‘실행’하도록 지정합니다.   두 번째 섹션에서는 커밋 해시에 따라 구성 업데이트의 고유 레이블을 만드는 작업을 실행합니다. 그런 다음, 이 작업은 이 업데이트의 새 값과 고유 레이블로 App Configuration 인스턴스를 업데이트합니다.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>GitHub 작업에서 Azure Key Vault 사용
AppConfiguration과 함께 Azure Key Vault를 사용하는 개발자는 두 개의 개별 파일(일반적으로 appsettings.json 및 a secretreferences.json)을 사용해야 합니다. secretreferences.json에는 키 자격 증명 모음 비밀의 URL이 포함됩니다.

{ "mySecret": "{\"uri\":\"https://myKeyVault.vault.azure.net/secrets/mySecret "}" }

그런 다음, GitHub 작업은 appsettings.json에서 strict 동기화를 수행한 후 secretreferences.json에서 strict가 아닌 동기화를 수행하도록 구성될 수 있습니다. 다음 샘플에서는 두 파일 중 하나가 업데이트될 때 동기화를 트리거합니다.

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>최대 깊이를 사용하여 GitHub 작업 제한
중첩된 JSON 특성의 기본 동작은 전체 개체를 평면화하는 것입니다.  아래 JSON은 다음 키-값 쌍을 정의합니다.

| 키 | 값 |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

중첩된 개체를 구성 인스턴스로 푸시되는 값으로 사용하려면 *depth* 값을 사용하여 적절한 깊이에서 평면화를 중지할 수 있습니다. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

깊이가 2인 경우 이제 위의 예제는 다음 키-값 쌍을 반환합니다.

| 키 | 값 |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>작업 입력 이해
입력 매개 변수는 런타임에 작업에서 사용하는 데이터를 지정합니다.  다음 표에는 App Configuration 동기화에서 허용하는 입력 매개 변수와 각 매개 변수의 예상 값이 포함되어 있습니다.  GitHub Actions의 작업 입력에 관한 자세한 내용은 GitHub [문서](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)를 참조하세요.

> [!Note]
> 입력 ID는 대/소문자를 구분하지 않습니다.


| 입력 이름 | 필수 여부 | 값 |
|----|----|----|
| configurationFile | 예 | 리포지토리에서 구성 파일의 상대 경로입니다.  Glob 패턴이 지원되며 여러 파일을 포함할 수 있습니다. |
| format | 예 | 구성 파일의 파일 형식입니다.  유효한 형식은 다음과 같습니다. JSON, YAML, 속성. |
| connectionString | 예 | App Configuration 인스턴스의 연결 문자열입니다. GitHub 리포지토리에서는 연결 문자열을 비밀로 저장해야 하며 워크플로에서는 비밀 이름만 사용해야 합니다. |
| 구분 기호 | 예 | 구성 파일을 키-값 쌍으로 평면화할 때 사용되는 구분 기호입니다.  유효한 값은 다음과 같습니다. , ; : - _ __ / |
| 접두사 | 예 | 키의 시작 부분에 추가할 접두사입니다. |
| label | 예 | 키-값 쌍을 설정할 때 사용되는 레이블입니다. 지정하지 않으면 null 레이블이 사용됩니다. |
| strict | 예 | strict 모드를 사용하는지 여부를 결정하는 부울 값입니다. 기본값은 False입니다. |
| depth | 예 | 구성 파일을 평면화할 최대 깊이입니다.  깊이는 양수여야 합니다.  기본값에는 최대 깊이가 없습니다. |
| tags | 예 | 키-값 쌍에 설정되는 태그를 지정합니다.  필요한 형식은 다음과 같은 JSON 개체의 문자열화된 형식입니다. { [propertyName: string]: string; } 각 속성 이름-값이 태그가 됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 App Configuration 동기화 GitHub 작업 및 해당 작업을 사용하여 App Configuration 인스턴스에 대한 업데이트를 자동화하는 방법을 알아보았습니다. Azure App Configuration이 키-값 쌍의 변경에 반응하는 방법을 알아보려면 다음 [문서](./concept-app-configuration-event.md)을 계속 진행합니다.
