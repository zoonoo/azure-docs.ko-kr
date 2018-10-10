---
title: Azure Service Fabric Mesh CLI 설정 | Microsoft Docs
description: Azure Service Fabric Mesh CLI를 설정하는 방법에 대해 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5e8eaca7f759842a8097184dafc1f3ea183b898c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993190"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정
Service Fabric Mesh CLI는 Service Fabric Mesh에서 리소스를 배포하고 관리하는 데 필요합니다. 

미리 보기를 위해 Azure CLI에 대한 확장으로 Azure Service Fabric Mesh CLI가 작성되었습니다. Azure Cloud Shell에서 설치하거나 Azure CLI의 로컬 설치에서 설치할 수 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>로컬에 Service Fabric Mesh CLI 설치
CLI를 로컬에서 설치하고 사용하려는 경우 Azure CLI 버전 2.0.43 이상을 설치해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 최신 버전의 CLI를 설치하거나 업그레이드하려면 [Azure CLI 설치][azure-cli-install]를 참조하세요.

다음 명령을 사용하여 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다. 

```azurecli-interactive
az extension add --name mesh
```

기존 Azure Service Fabric Mesh CLI 모듈을 업데이트하려면 다음 명령을 실행합니다.

```azurecli-interactive
az extension update --name mesh
```

[Windows 개발 환경](service-fabric-mesh-howto-setup-developer-environment-sdk.md)을 설정할 수도 있습니다.

[azure-cli-install]: /cli/azure/install-azure-cli
