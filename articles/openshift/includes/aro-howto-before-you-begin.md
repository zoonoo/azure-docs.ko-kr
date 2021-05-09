---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: d888d4df3f578bf88f8f38cb979ed70f681c64e8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108157643"
---
### <a name="create-the-cluster"></a>클러스터 만들기

자습서에 따라 [Azure Red Hat OpenShift 클러스터를 만듭니다](../tutorial-create-cluster.md). CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="connect-to-the-cluster"></a>클러스터에 연결

Azure Red Hat OpenShift 클러스터를 관리하려면 OpenShift 명령줄 클라이언트인 [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)를 사용합니다.

> [!NOTE]
> [Azure Cloud Shell](https://shell.azure.com/)에 [OpenShift 명령줄을 설치](../tutorial-connect-cluster.md)하고 아래의 모든 명령줄 작업에 사용하는 것이 좋습니다. shell.azure.com에 접속하거나 링크를 클릭하여 셸을 시작합니다.
>
> [![Embed 시작](/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Azure Cloud Shell 시작")](https://shell.azure.com/bash)

자습서에 따라 CLI를 설치하고, 클러스터 자격 증명을 검색하고, 웹 콘솔 및 OpenShift CLI를 사용하여 [클러스터에 연결](../tutorial-connect-cluster.md)합니다.

로그인하면 `default` 프로젝트를 사용 중이라는 메시지가 표시됩니다.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```