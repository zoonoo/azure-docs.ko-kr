---
title: "Azure Container Registry 웹후크 | Microsoft Docs"
description: "Azure Container Registry 웹후크"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker, 컨테이너, ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d0190f5725671c320d92b897f0dcef7a526a86e3
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="using-azure-container-registry-webhooks---azure-portal"></a>Azure Container Registry 웹후크 사용 - Azure Portal

Azure Container Registry는 Docker 허브에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 개인 Docker 컨테이너 이미지를 저장하고 관리합니다. 레지스트리 리포지토리 중 하나에서 특정 작업이 수행되는 경우 웹후크를 사용하여 이벤트를 트리거합니다. 웹후크는 레지스트리 수준에서 이벤트에 응답하거나 특정 리포지토리 태그로 범위를 줄일 수 있습니다. 

자세한 배경 지식 및 개념은 [개요](./container-registry-intro.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건 

- Azure 컨테이너 관리 레지스트리 - Azure 구독에서 관리되는 컨테이너 레지스트리를 만듭니다. 예를 들어 Azure Portal 또는 Azure CLI 2.0을 사용합니다. 
- Docker CLI - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 Docker 엔진을 설치합니다. 

## <a name="create-webhook-azure-portal"></a>웹후크 Azure Portal 만들기

1. Azure Portal에 로그인하여 웹후크를 만들려는 레지스트리로 이동합니다. 

2. 컨테이너 블레이드에서 "웹후크" 탭을 선택합니다. 

3. 웹후크 블레이드 도구 모음에서 "추가"를 선택합니다. 

4. *웹후크 만들기* 양식을 다음 정보로 완성합니다.

| 값 | 설명 |
|---|---|
| 이름 | 웹후크에 지정하려는 이름입니다. 소문자와 숫자만 사용할 수 있으며 5-50자 사이입니다. |
| 서비스 URI | 웹후크가 POST 알림을 보내야 하는 URI입니다. |
| 사용자 지정 헤더 | POST 요청과 함께 전달하려는 헤더입니다. "키: 값" 형식이어야 합니다. |
| 트리거 동작 | 웹후크를 트리거하는 동작입니다. 현재 웹후크는 이미지에 대한 밀어넣기 및/또는 삭제 동작에 의해 트리거될 수 있습니다. |
| 가동 상태 | 웹후크가 만들어진 후의 상태입니다. 기본적으로 사용하도록 설정되어 있습니다. |
| 범위 | 웹후크가 작동하는 범위입니다. 기본적으로 이 범위는 레지스트리의 모든 이벤트입니다. "리포지토리: 태그" 형식을 사용하여 리포지토리 또는 태그에 대해 지정할 수 있습니다. |

웹후크 양식 예제:

![DCOS UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>웹후크 Azure CLI 만들기

Azure CLI를 사용하여 웹후크를 만들려면 [az acr webhook create](/cli/azure/acr/webhook#create) 명령을 사용합니다.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>웹후크 테스트

### <a name="azure-portal"></a>Azure 포털

컨테이너 이미지 밀어넣기 및 삭제 작업에서 웹후크를 사용하기 전에 **Ping** 단추를 사용하여 테스트할 수 있습니다. Ping을 사용하는 경우 지정된 끝점에 일반 post 요청을 보내고 응답을 기록합니다. 웹후크가 올바르게 설정되었는지 확인하는 데 도움이 됩니다.

1. 테스트하려는 웹후크를 선택합니다. 
2. 맨 위의 도구 모음에서 "Ping" 작업을 선택합니다. 
3. 요청 및 응답을 확인합니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 ACR 웹후크를 테스트하려면 [az acr webhook ping](/cli/azure/acr/webhook#ping) 명령을 사용합니다.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

결과를 보려면 [az acr webhook list-events](/cli/azure/acr/webhook#list-events) 명령을 사용합니다. 

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>웹후크 삭제

### <a name="azure-portal"></a>Azure 포털

각 웹후크는 Azure Portal에서 웹후크를 선택하고 삭제 단추를 선택하여 삭제할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
