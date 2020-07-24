---
title: Azure HPC 캐시의 필수 구성 요소 Azure CLI
description: Azure CLI를 사용 하 여 Azure HPC 캐시를 만들거나 수정 하기 전의 설정 단계
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099248"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC 캐시에 대 한 Azure CLI 설정

Azure CLI를 사용 하 여 Azure HPC 캐시를 만들거나 관리 하기 전에 다음 단계를 수행 하 여 환경을 준비 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Azure CLI 설치

Azure HPC 캐시에는 Azure CLI 버전 2.7 이상이 필요 합니다. `az --version`을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 설치하거나 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Azure HPC 캐시 확장을 설치 합니다.

Azure HPC Cache 함수는 주 코드 베이스의 일부가 아니므로 확장 참조도 설치 해야 합니다. 아래의 지침을 따르세요.

1. 로그인

   로컬에 설치 된 CLI 버전을 사용 하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용 하 여 로그인 합니다.

    ```azurecli
    az login
    ```

    터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

   > [!TIP]
   > 구독이 여러 개인 경우 하나를 선택 해야 합니다. Azure Portal에서 Cloud Shell 세션을 시작할 때 선택 하거나 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli#sign-in) 의 지침에 따라 명령줄에서 구독을 설정 합니다.

2. Azure CLI 확장 설치

   Azure HPC Cache 함수는 Azure CLI 확장으로 제공 되며 아직 핵심 CLI 패키지의 일부가 아닙니다. 확장 참조를 사용 하려면 먼저 확장 참조를 설치 해야 합니다.

   Azure CLI 확장은 실험적 및 시험판 명령에 대 한 액세스를 제공 합니다. 업데이트 및 제거를 포함 하 여 확장에 대 한 자세한 내용은 [Azure CLI에 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조 하세요.

   다음 명령을 실행 하 여 Azure HPC 캐시용 확장을 설치 합니다.

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>기본 리소스 그룹 설정 (선택 사항)

대부분의 hpc 캐시 명령은 캐시의 리소스 그룹을 전달 해야 합니다. [Az configure](/cli/azure/reference-index#az-configure)를 사용 하 여 기본 리소스 그룹을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure CLI 확장을 설치 하 고 로그인 한 후 Azure CLI를 사용 하 여 Azure HPC 캐시 시스템을 만들고 관리할 수 있습니다.

* [Azure HPC Cache 만들기](hpc-cache-create.md)
* [Azure CLI hpc 캐시 설명서](/cli/azure/ext/hpc-cache/hpc-cache)
