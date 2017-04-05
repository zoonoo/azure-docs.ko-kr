---
title: "Azure CLI 스크립트 샘플 - Batch로 응용 프로그램 추가 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Batch로 응용 프로그램 추가"
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
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Azure CLI를 사용하여 응용 프로그램을 Azure Batch에 추가

이 스크립트는 Azure Batch 풀 또는 작업에 사용할 응용 프로그램을 설정하는 방법을 보여 줍니다. 응용 프로그램을 설정하려면 실행 파일을 해당 종속성과 함께 .zip 파일로 패키지로 만듭니다. 이 예제에서는 실행 가능한 zip 파일을 'my-application-exe.zip'이라고 합니다.
이 스크립트를 실행하기 전에 배치 계정이 이미 설정되어 있다고 가정합니다. 자세한 내용은 [배치 계정 만들기를 위한 샘플 스크립트](./batch-cli-sample-create-account.md)를 참조하세요.

필요한 경우 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)에 있는 지침을 사용하여 Azure CLI를 설치한 다음, `az login`을 실행하여 Azure에 로그인합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/batch/add-application/add-application.sh "응용 프로그램 추가")]

## <a name="clean-up-application"></a>응용 프로그램 정리

위의 샘플 스크립트를 실행한 후 다음 명령을 실행하여 응용 프로그램 및 업로드된 모든 응용 프로그램 패키지를 제거합니다.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 응용 프로그램을 만들고 응용 프로그램 패키지를 업로드합니다.
표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | 응용 프로그램을 만듭니다.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | 응용 프로그램의 속성을 업데이트합니다.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | 지정된 응용 프로그램에 응용 프로그램 패키지를 추가합니다.  |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Batch CLI 스크립트 샘플은 [Azure Batch CLI 설명서](../batch-cli-samples.md)에서 확인할 수 있습니다.

