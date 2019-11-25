---
title: Webhooks to respond to registry actions
description: Learn how to use webhooks to trigger events when push or pull actions occur in your registry repositories.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454367"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry 웹후크 사용

Azure Container Registry는 Docker Hub에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 프라이빗 Docker 컨테이너 이미지를 저장하고 관리합니다. It can also host repositories for [Helm charts](container-registry-helm-repos.md) (preview), a packaging format to deploy applications to Kubernetes. 레지스트리 리포지토리 중 하나에서 특정 작업이 수행되는 경우 웹후크를 사용하여 이벤트를 트리거할 수 있습니다. 웹후크는 레지스트리 수준에서 이벤트에 응답하거나 특정 리포지토리 태그로 범위를 줄일 수 있습니다. With a  [geo-replicated](container-registry-geo-replication.md) registry, you configure each webhook to respond to events in a specific regional replica.

Webhook 요청에 대한 세부 정보는 [Azure Container Registry 웹후크 스키마 참조](container-registry-webhook-reference.md)를 참조하세요.

## <a name="prerequisites"></a>전제 조건

* Azure Container Registry - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다. The [Azure Container Registry SKUs](container-registry-skus.md) have different webhooks quotas.
* Docker CLI - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 [Docker 엔진](https://docs.docker.com/engine/installation/)을 설치합니다.

## <a name="create-webhook---azure-portal"></a>Create webhook - Azure portal

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
1. 웹후크를 만들려는 컨테이너 레지스트리로 이동합니다.
1. Under **Services**, select **Webhooks**.
1. 웹후크 도구 모음에서 **추가**를 선택합니다.
1. 다음 정보로 *웹후크 만들기* 양식을 완성합니다.

| Value | 설명 |
|---|---|
| Webhook name | 웹후크에 지정하려는 이름입니다. It may contain only letters and numbers, and must be 5-50 characters in length. |
| 위치 | For a [geo-replicated](container-registry-geo-replication.md) registry, specify the Azure region of the registry replica. 
| 서비스 URI | 웹후크가 POST 알림을 보내야 하는 URI입니다. |
| 사용자 지정 헤더 | POST 요청과 함께 전달하려는 헤더입니다. "키: 값" 형식이어야 합니다. |
| 트리거 동작 | 웹후크를 트리거하는 동작입니다. Actions include image push, image delete, Helm chart push, Helm chart delete, and image quarantine. You can choose one or more actions to trigger the webhook. |
| 상태 | 웹후크가 만들어진 후의 상태입니다. 기본적으로 사용하도록 설정되어 있습니다. |
| 범위 | 웹후크가 작동하는 범위입니다. If not specified, the scope is for all events in the registry. It can be specified for a repository or a tag by using the format "repository:tag", or "repository:*" for all tags under a repository. |

웹후크 양식 예제:

![Azure Portal의 ACR 웹후크 만들기 UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Create webhook - Azure CLI

Azure CLI를 사용하여 웹후크를 만들려면 [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) 명령을 사용합니다. The following command creates a webhook for all image delete events in the registry *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>웹후크 테스트

### <a name="azure-portal"></a>Azure Portal

Prior to using the webhook, you can test it with the **Ping** button. Ping은 지정된 엔드포인트에 일반 POST 요청을 보내고 응답을 기록합니다. ping 기능을 사용하면 웹후크를 올바르게 구성했는지 확인하는 데 도움이 될 수 있습니다.

1. 테스트하려는 웹후크를 선택합니다.
2. 맨 위의 도구 모음에서 **Ping**을 선택합니다.
3. **HTTP 상태** 열에서 엔드포인트의 응답을 확인합니다.

![Azure Portal의 ACR 웹후크 만들기 UI](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 ACR 웹후크를 테스트하려면 [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) 명령을 사용합니다.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

결과를 보려면 [az acr webhook list-events](/cli/azure/acr/webhook) 명령을 사용합니다.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>웹후크 삭제

### <a name="azure-portal"></a>Azure Portal

각 웹후크는 Azure Portal에서 웹후크를 선택하고 **삭제** 단추를 선택하여 삭제할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>다음 단계

### <a name="webhook-schema-reference"></a>Webhook 스키마 참조

Azure Container Registry에서 내보낸 JSON 이벤트 페이로드의 형식 및 속성에 대한 내용은 webhook 스키마 참조를 참조하세요.

[Azure Container Registry 웹후크 스키마 참조](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid 이벤트

이 문서에서 설명한 기본 레지스트리 webhook 이벤트 외에도 Azure Container Registry는 Event Grid로 이벤트를 내보낼 수 있습니다.

[빠른 시작: Event Grid로 컨테이너 레지스트리 이벤트 전송](container-registry-event-grid-quickstart.md)
