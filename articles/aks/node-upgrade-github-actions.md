---
title: GitHub 작업을 사용 하 여 AKS 노드 업그레이드 처리
titleSuffix: Azure Kubernetes Service
description: GitHub 작업을 사용 하 여 AKS 노드를 업데이트 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217960"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>GitHub 작업을 사용 하 여 자동으로 AKS (Azure Kubernetes Service) 노드에 보안 업데이트 적용

보안 업데이트는 AKS 클러스터의 보안을 유지 하 고 기본 OS에 대 한 최신 수정 사항을 준수 하는 데 중요 한 부분입니다. 이러한 업데이트는 OS 보안 수정 사항 또는 커널 업데이트를 포함합니다. 일부 업데이트는 프로세스를 완료 하기 위해 노드를 다시 부팅 해야 합니다.

를 실행 `az aks upgrade` 하면 업데이트를 적용 하는 데 0의 가동 중지 시간이 제공 됩니다. 명령은 모든 클러스터 노드에 최신 업데이트를 적용 하 고, 노드에 대 한 트래픽을 cordoning 및 드레이닝 하 고, 노드를 다시 시작한 다음 업데이트 된 노드에 대 한 트래픽을 허용 하는 기능을 처리 합니다. 다른 방법을 사용 하 여 노드를 업데이트 하는 경우 AKS는 노드를 자동으로 다시 시작 하지 않습니다.

> [!NOTE]
> 플래그와 함께 사용 하는 경우의 주요 차이점은 `az aks upgrade` `--node-image-only` 사용 시 노드 이미지만 업그레이드 된다는 것입니다. 생략 하면 노드 이미지와 Kubernetes 제어 평면 버전이 모두 업그레이드 됩니다. [노드에서 관리 되는 업그레이드에 대 한 문서][managed-node-upgrades-article] 와 [클러스터 업그레이드를][cluster-upgrades-article] 위한 문서에서 자세한 정보를 확인할 수 있습니다.

모든 Kubernetes의 노드는 표준 Azure VM (가상 머신)에서 실행 됩니다. 이러한 Vm은 Windows 또는 Linux를 기반으로 할 수 있습니다. Linux 기반 Vm은 매일 밤에 업데이트를 자동으로 확인 하도록 구성 된 OS에서 Ubuntu 이미지를 사용 합니다.

Azure CLI 명령을 사용 하는 경우 `az aks upgrade` 최신 보안 및 커널 업데이트를 사용 하 여 새 노드의 서 수를 만들 때 이러한 노드는 처음에 통제 업데이트가 완료 될 때까지 앱이 예약 되지 않도록 합니다. 완료 되 면 Azure cordons (노드를 새 작업 예약에 사용할 수 없도록 설정) 하 고 이전 노드를 드레이닝 (기존 워크 로드를 다른 노드로 이동) 하 고 새 노드를 중단 하 여 모든 예약 된 응용 프로그램을 새 노드로 효과적으로 전송할 수 있습니다.

이 프로세스는 linux 기반 커널을 수동으로 업데이트 하는 것 보다 좋습니다. 새 커널 업데이트를 설치 하면 Linux가 다시 부팅 되어야 하기 때문입니다. OS를 수동으로 업데이트 하는 경우에도 VM을 다시 부팅 하 고 모든 앱을 수동으로 cordoning 및 드레이닝 해야 합니다.

이 문서에서는 AKS 노드의 업데이트 프로세스를 자동화 하는 방법을 보여 줍니다. GitHub 작업 및 Azure CLI를 사용 하 여 자동으로 실행을 기반으로 업데이트 작업을 만듭니다 `cron` .

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

또한이 문서에서는 사용자의 작업을 만들 수 있는 [GitHub][github] 계정이 있다고 가정 합니다.

## <a name="create-a-timed-github-action"></a>시간이 지정 된 GitHub 작업 만들기

`cron` 는 자동화 된 일정에 따라 일련의 명령 또는 작업을 실행할 수 있는 유틸리티입니다. 자동화 된 일정에 따라 AKS 노드를 업데이트 하는 작업을 만들려면 작업을 호스트 하는 리포지토리가 필요 합니다. 일반적으로 GitHub 동작은 응용 프로그램과 동일한 리포지토리에 구성 되지만 모든 리포지토리를 사용할 수 있습니다. 이 문서에서는 [프로필 리포지토리][profile-repository]를 사용 합니다. 없는 경우 GitHub 사용자 이름과 같은 이름으로 새 리포지토리를 만듭니다.

1. GitHub에서 리포지토리로 이동 합니다.
1. 페이지 위쪽에서 **작업** 탭을 클릭 합니다.
1. 이 리포지토리에 이미 워크플로를 설정한 경우 완료 된 실행 목록으로 이동 됩니다 .이 경우에는 **새 워크플로** 단추를 클릭 합니다. 리포지토리의 첫 번째 워크플로 인 경우 GitHub에 일부 프로젝트 템플릿이 표시 됩니다. 설명 텍스트 아래에서 **직접 워크플로 설정** 링크를 클릭 합니다.
1. 워크플로와 태그를 다음과 같이 변경 합니다 `name` `on` . GitHub 작업은 Linux 기반 시스템과 동일한 [POSIX cron 구문을][cron-syntax] 사용 합니다. 이 일정에서는 30 일 마다 오전 3 시에 워크플로를 실행 하도록 지시 합니다.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. 아래를 사용 하 여 새 작업을 만듭니다. 이 작업의 이름은 `upgrade-node` 로 지정 되 고, Ubuntu 에이전트에서 실행 되며, Azure CLI 계정에 연결 하 여 노드를 업그레이드 하는 데 필요한 단계를 실행 합니다.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>워크플로에서 Azure CLI 설정

키에서는 `steps` 워크플로를 실행 하 여 노드를 업그레이드 하는 모든 작업을 정의 합니다.

Azure CLI을 다운로드 하 고 로그인 합니다.

1. GitHub 작업 화면의 오른쪽에서 *marketplace 검색 표시줄* 을 찾고 **"Azure 로그인"** 을 입력 합니다.
1. **Azure에서** 게시 한 **Azure 로그인** 이라는 작업을 결과로 얻을 수 있습니다.

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="두 줄을 표시 하는 검색 결과, 첫 번째 작업은 ' Azure 로그인 ' and second ' Azure Container Registry 로그인 '":::

1. **Azure 로그인** 을 클릭 합니다. 다음 화면에서 코드 샘플의 오른쪽 위에 있는 **복사 아이콘** 을 클릭 합니다.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Azure 로그인 작업 결과 창, 코드 샘플이 포함 된 복사 아이콘 주위의 빨간색 사각형은 클릭 지점을 강조 표시 합니다.":::

1. 키 아래에 다음을 붙여넣습니다 `steps` .

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Azure CLI에서 다음 명령을 실행 하 여 새 사용자 이름 및 암호를 생성 합니다.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    출력은 다음 json과 유사 해야 합니다.

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **새 브라우저 창에서** GitHub 리포지토리로 이동 하 여 리포지토리의 **설정** 탭을 엽니다. **비밀** 을 클릭 하 고 **새 리포지토리 암호** 를 클릭 합니다.
1. *이름* 에는 `AZURE_CREDENTIALS`를 사용합니다.
1.  *값* 에 대해 새 사용자 이름 및 암호를 만든 이전 단계의 출력에서 전체 콘텐츠를 추가 합니다.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="비밀 제목으로 AZURE_CREDENTIALS를 표시 하는 폼과 JSON으로 붙여넣은 실행 된 명령의 출력":::

1. **비밀 추가** 를 클릭 합니다.

작업에서 사용 하는 CLI는 Azure 계정에 기록 되 고 명령을 실행할 준비가 됩니다.

Azure CLI 명령을 실행 하는 단계를 만듭니다.

1. 화면 오른쪽에 있는 *GitHub marketplace* 의 **검색 페이지로** 이동 하 여 *Azure CLI 작업* 을 검색 합니다. *Azure에서 Azure CLI 작업을* 선택 합니다.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Azure에서 만든 대로 첫 번째 결과가 표시 된 ' Azure CLI 작업 '에 대 한 검색 결과":::

1. *GitHub marketplace 결과* 에서 복사 단추를 클릭 하 고 다음과 같이 *Azure 로그인* 단계 아래에 있는 주 편집기에서 작업 내용을 붙여넣습니다.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > `-g` `-n` 이전 단계와 비슷한 비밀에 및 매개 변수를 추가 하 여 명령에서 및 매개 변수를 분리할 수 있습니다. `{resourceGroupName}`및 `{aksClusterName}` 자리 표시자를 해당 비밀으로 바꿉니다 (예: `${{secrets.RESOURCE_GROUP_NAME}}` 및).`${{secrets.AKS_CLUSTER_NAME}}`

1. 파일의 이름을 `upgrade-node-images`로 변경합니다.
1. **커밋 시작** 을 클릭 하 고 메시지 제목을 추가 하 고 워크플로를 저장 합니다.

커밋을 만들면 워크플로가 저장 되 고 실행할 준비가 됩니다.

> [!NOTE]
> 클러스터의 모든 노드 풀 대신 단일 노드 풀을 업그레이드 하려면 `--name` 명령에 매개 변수를 추가 하 여 `az aks nodepool upgrade` 노드 풀 이름을 지정 합니다. 예를 들면 다음과 같습니다.
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>수동으로 GitHub 작업 실행

이라는 새 트리거를 추가 하 여 예약 된 실행 뿐만 아니라 워크플로를 수동으로 실행할 수 있습니다 `on` `workflow_dispatch` . 완성 된 파일은 아래와 같이 표시 됩니다.

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>다음 단계

- 최신 노드 이미지에 대 한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases) 를 참조 하세요.
- [AKS 클러스터 업그레이드][cluster-upgrades-article]를 사용 하 여 Kubernetes 버전을 업그레이드 하는 방법을 알아봅니다.
- 여러 노드 풀 및 [여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 사용 하 여 노드 풀을 업그레이드 하는 방법에 대해 자세히 알아보세요.
- [시스템 노드 풀][system-pools] 에 대해 자세히 알아보기
- 스폿 인스턴스를 사용 하 여 비용을 절감 하는 방법을 알아보려면 [AKS에 별색 노드 풀 추가][spot-pools] 를 참조 하세요.

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
