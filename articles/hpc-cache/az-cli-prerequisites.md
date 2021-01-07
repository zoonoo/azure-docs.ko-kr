---
title: Azure HPC 캐시의 필수 구성 요소 Azure CLI
description: Azure CLI를 사용 하 여 Azure HPC 캐시를 만들거나 수정 하기 전의 설정 단계
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654459"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC Cache용 Azure CLI 설치

Azure CLI를 사용 하 여 Azure HPC 캐시를 만들거나 관리 하기 전에 다음 단계를 수행 하 여 환경을 준비 합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC 캐시에는 Azure CLI 버전 2.7 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="set-default-resource-group-optional"></a>기본 리소스 그룹 설정 (선택 사항)

대부분의 hpc 캐시 명령은 캐시의 리소스 그룹을 전달 해야 합니다. [Az configure](/cli/azure/reference-index#az-configure)를 사용 하 여 기본 리소스 그룹을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure CLI 확장을 설치 하 고 로그인 한 후 Azure CLI를 사용 하 여 Azure HPC 캐시 시스템을 만들고 관리할 수 있습니다.

* [Azure HPC Cache 만들기](hpc-cache-create.md)
* [Azure CLI hpc 캐시 설명서](/cli/azure/ext/hpc-cache/hpc-cache)
