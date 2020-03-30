---
title: Azure 앱 구성 동기화를 사용하여 GitHub 작업 사용
description: GitHub 작업을 사용하여 GitHub 리포지토리를 업데이트할 때 앱 구성 인스턴스에 대한 업데이트를 트리거합니다.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46d4aa4d4d37e9cac928e8d1a9e5e77ca0f30f18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384061"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>GitHub Actions를 사용하여 App Configuration 인스턴스 동기화

기존 소스 제어 방법을 계속 사용하려는 팀은 GitHub 작업을 사용하여 GitHub 리포지토리를 앱 구성 저장소와 자동으로 동기화할 수 있습니다. 이렇게 하면 다음과 같은 앱 구성 이점을 얻는 동시에 평소와 같이 구성 파일을 변경할 수 있습니다. <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 코드 외부의 중앙 집중식 구성 <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 전체 앱을 재배포하지 않고 구성 업데이트 <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Azure 앱 서비스 및 기능과 같은 서비스와의 통합. 

GitHub 작업 [워크플로는](https://help.github.com/articles/about-github-actions#workflow) GitHub 리포지토리에서 자동화된 프로세스를 정의합니다. *Azure 앱 구성 동기화* 작업은 원본 리포지토리를 변경할 때 앱 구성 인스턴스에 대한 업데이트를 트리거합니다. 리포지토리 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일을 사용하여 단계 및 매개 변수를 정의합니다. 앱 코드와 마찬가지로 앱 구성 파일을 푸시, 검토 또는 분기할 때 구성 업데이트를 트리거할 수 있습니다.

GitHub [설명서는](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHub 워크플로 및 작업에 대한 심층적인 보기를 제공합니다. 

## <a name="enable-github-actions-in-your-repository"></a>리포지토리에서 GitHub 작업 사용
이 GitHub 작업을 사용 하려면 리포지토리로 이동 하 고 **작업** 탭을 선택 합니다. 새 **워크플로를**클릭 한 다음 **워크플로를 직접 설정**합니다. 마지막으로 마켓플레이스에서 "Azure 앱 구성 동기화"를 검색합니다.
> [!div class="mx-imgBorder"]
> ![작업 탭 선택](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![앱 구성 동기화 작업 선택](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>푸시 후 구성 파일 동기화
이 작업은 변경 사항이 로 푸시될 때 `appsettings.json`Azure 앱 구성 파일을 동기화합니다. 개발자가 변경 을 `appsettings.json`푸시하면 앱 구성 동기화 작업이 앱 구성 인스턴스를 새 값으로 업데이트합니다.

이 워크플로의 첫 번째 섹션에서는 *작업이 마스터* 분기에 `appsettings.json` 포함된 *푸시에* *대해* 트리거되도록 지정합니다. 두 번째 섹션에서는 작업이 트리거되면 실행되는 작업이 나열됩니다. 이 작업은 관련 파일을 체크 아웃하고 리포지토리에 비밀로 저장된 연결 문자열을 사용하여 앱 구성 인스턴스를 업데이트합니다.  GitHub에서 비밀을 사용하는 것에 대한 자세한 내용은 암호화된 비밀 만들기 및 사용에 대한 [GitHub의 문서를](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) 참조하세요.

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

## <a name="use-a-dynamic-label-on-sync"></a>동기화 시 동적 레이블 사용
이전 작업은 업데이트될 때마다 `appsettings.json` 앱 구성 인스턴스를 업데이트합니다. 이 작업은 각 동기화에 동적 레이블을 삽입하여 각 동기화를 고유하게 식별하고 코드 변경 내용을 구성 변경 사항에 매핑할 수 있도록 합니다.

이 워크플로의 첫 번째 섹션에서는 *작업이 마스터* 분기에 `appsettings.json` 포함된 *푸시에* *대해* 트리거되도록 지정합니다. 두 번째 섹션에서는 커밋 해시를 기반으로 구성 업데이트에 대한 고유한 레이블을 만드는 작업을 실행합니다. 그런 다음 작업은 이 업데이트에 대한 새 값과 고유 레이블로 앱 구성 인스턴스를 업데이트합니다.

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

## <a name="use-strict-sync"></a>엄격한 동기화 사용
엄격 모드를 사용하도록 설정하면 동기화를 통해 앱 구성 인스턴스가 지정된 접두사에 대한 구성 파일과 일치하고 레이블을 정확하게 지정합니다. 구성 파일에 없는 동일한 접두사 및 레이블이 있는 키-값 쌍이 삭제됩니다. 
 
엄격 모드를 사용할 수 없는 경우 동기화는 구성 파일에서 키-값만 설정합니다. 키-값 쌍은 삭제되지 않습니다. 

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

## <a name="use-max-depth-to-limit-github-action"></a>최대 깊이를 사용하여 GitHub 작업을 제한합니다.
중첩된 JSON 특성의 기본 동작은 전체 개체를 병합하는 것입니다.  아래 JSON은 이 키-값 쌍을 정의합니다.

| Key | 값 |
| --- | --- |
| 개체:내부:내부 키 | 내부 가치 |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

중첩된 개체가 Configuration 인스턴스에 푸시된 값인 경우 *깊이* 값을 사용하여 적절한 깊이에서 병합을 중지할 수 있습니다. 

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

깊이가 2인 경우 위의 예제에서는 이제 다음 키-값 쌍을 반환합니다.

| Key | 값 |
| --- | --- |
| 오브젝트:내부 | {"내부키":"내부 가치"} |

## <a name="understand-action-inputs"></a>작업 입력 이해
입력 매개 변수는 런타임 중에 작업에서 사용되는 데이터를 지정합니다.  다음 표에는 앱 구성 동기화에서 허용하는 입력 매개 변수와 각 테이블에 대한 예상 값이 포함되어 있습니다.  GitHub 작업에 대한 작업 입력에 대한 자세한 내용은 GitHub의 [설명서를](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)참조하십시오.

> [!Note]
> 입력 된 아이디는 대/소문자를 구분하지 않습니다.


| 입력 이름 | 필수 여부 | 값 |
|----|----|----|
| 구성파일 | yes | 리포지토리의 구성 파일에 대한 상대 경로입니다.  Glob 패턴은 지원되며 여러 파일을 포함할 수 있습니다. |
| format | yes | 구성 파일의 파일 형식입니다.  유효한 형식은 다음과 같습니다: JSON, YAML, 속성. |
| connectionString | yes | 앱 구성 인스턴스에 대한 연결 문자열입니다. 연결 문자열은 GitHub 리포지토리에 비밀로 저장되어야 하며 보안 이름만 워크플로에 사용해야 합니다. |
| 구분 기호 | yes | 구성 파일을 키-값 쌍으로 병합할 때 사용됩니다.  유효한 값은 다음과 같습니다. , ; : - _ __ / |
| 접두사 | 예 | 키의 시작 부분에 추가할 접두사입니다. |
| label | 예 | 키-값 쌍을 설정할 때 사용되는 레이블입니다. 지정되지 않은 경우 null 레이블이 사용됩니다. |
| strict | 예 | 엄격 모드가 활성화되어 있는지 여부를 결정하는 부울 값입니다. 기본값은 False입니다. |
| 깊이 | 예 | 구성 파일을 병합하기 위한 최대 깊이입니다.  깊이는 양수여야 합니다.  기본값에는 최대 깊이가 없습니다. |
| tags | 예 | 키-값 쌍에 설정된 태그를 지정합니다.  예상 된 형식은 다음 모양의 JSON 개체의 문자열 형식입니다: { [propertyName: 문자열]: 문자열; } 각 속성 이름-값태그가 됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 앱 구성 동기화 GitHub 작업에 대해 배웠으며 앱 구성 인스턴스에 대한 업데이트를 자동화하는 데 사용할 수 있는 방법을 배웠습니다. Azure 앱 구성이 키-값 쌍의 변경 사항에 어떻게 반응하는지 알아보려면 다음 [문서를](./concept-app-configuration-event.md)계속 루십시오.
