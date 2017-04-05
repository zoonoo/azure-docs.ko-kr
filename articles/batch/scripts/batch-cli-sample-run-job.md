---
title: "Azure CLI 스크립트 샘플 - Batch로 작업 실행 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Batch로 작업 실행"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Azure CLI를 사용하여 Azure Batch에서 실행 중인 작업

이 스크립트는 Batch 작업을 만들고 일련의 태스크를 작업에 추가합니다. 또한 작업 및 태스크를 모니터링하는 방법도 보여 줍니다.
이 스크립트를 실행하기 전에 배치 계정이 이미 설정되어 있고 풀과 응용 프로그램이 모두 구성되어 있다고 가정합니다. 자세한 내용은 이러한 각 항목을 다루는 [샘플 스크립트](../batch-cli-samples.md)를 참조하세요.

필요한 경우 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)에 있는 지침을 사용하여 Azure CLI를 설치한 다음, `az login`을 실행하여 Azure에 로그인합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[메인](../../../cli_scripts/batch/run-job/run-job.sh "작업 실행")]

## <a name="clean-up-job"></a>작업 정리

위의 샘플 스크립트를 실행한 후 다음 명령을 실행하여 작업 및 작업의 모든 태스크를 제거합니다. 풀을 별도로 삭제해야 합니다. [풀 관리에 대한 자습서](./batch-cli-sample-manage-pool.md)를 참조하세요.

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Batch 작업 및 태스크를 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 배치 계정에 대해 인증합니다.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | 배치 작업을 만듭니다.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Batch 작업의 속성을 업데이트합니다.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | 지정된 Batch 작업의 세부 정보를 검색합니다.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | 지정된 Batch 작업에 태스크를 추가합니다.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | 지정된 Batch 작업에서 태스크의 세부 정보를 검색합니다.  |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Batch CLI 스크립트 샘플은 [Azure Batch CLI 설명서](../batch-cli-samples.md)에서 확인할 수 있습니다.

