---
title: "Azure 컨테이너 레지스트리로 인증 | Microsoft 문서"
description: "Azure Active Directory 서비스 주체 또는 관리자 계정을 사용하여 Azure 컨테이너 레지스트리에 로그인하는 방법에 대해 설명합니다."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 2372d9d4dc190e7583bcc513042a85e970325e14
ms.contentlocale: ko-kr
ms.lasthandoff: 06/21/2017

---
# <a name="authenticate-with-a-private-docker-container-registry"></a>개인 Docker 컨테이너 레지스트리로 인증
Azure 컨테이너 레지스트리에서 컨테이너 이미지를 사용하려면 `docker login` 명령을 사용하여 로그인합니다. **[Azure Active Directory 서비스 주체](../active-directory/active-directory-application-objects.md)** 또는 레지스트리 특정 **관리자 계정**을 사용하여 로그인할 수 있습니다. 이 문서에서는 이러한 ID에 대해 자세히 설명합니다.



## <a name="service-principal"></a>서비스 주체

레지스트리에 [서비스 주체를 할당](container-registry-get-started-azure-cli.md#assign-a-service-principal)하고 기본 Docker 인증에 사용할 수 있습니다. 대부분의 시나리오에서는 서비스 주체를 사용하도록 권장됩니다. 다음 예제와 같이 서비스 주체의 앱 ID와 암호를 `docker login` 명령에 제공합니다.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

로그인하면 Docker에서 자격 증명을 캐시하므로 앱 ID를 기억할 필요가 없습니다.

> [!TIP]
> 원하는 경우 `az ad sp reset-credentials` 명령을 실행하여 서비스 주체의 암호를 다시 생성할 수 있습니다.
>


서비스 주체는 [역할 기반 액세스](../active-directory/role-based-access-control-configure.md)를 레지스트리에 허용합니다. 사용 가능한 역할은 다음과 같습니다.
  * 독자(풀 전용 액세스).
  * 참가자(풀 및 푸시).
  * 소유자(풀, 푸시, 다른 사용자에게 역할 할당).

Azure Container Registry에서는 익명 액세스를 사용할 수 없습니다. 공용 이미지에는 [Docker 허브](https://docs.docker.com/docker-hub/)를 사용할 수 있습니다.

여러 서비스 주체를 레지스트리에 할당할 수 있으므로 여러 사용자 또는 응용 프로그램에 대한 액세스를 정의할 수 있습니다. 또한 서비스 주체는 다음 예제와 같은 개발자 또는 DevOps 시나리오에서 레지스트리에 대한 "헤드리스" 연결을 사용하도록 설정할 수 있습니다.

  * 레지스트리에서 DC/OS, Docker Swarm 및 Kubernetes를 포함한 오케스트레이션 시스템으로 컨테이너 배포 - 컨테이너 레지스트리를 관련 Azure 서비스(예: [Container Service](../container-service/index.yml), [App Service](../app-service/index.md), [Batch](../batch/index.md), [Service Fabric](../service-fabric/index.md) 등)로 가져올 수도 있습니다.

  * 컨테이너 이미지를 작성하고 레지스트리로 푸시하는 지속적인 통합 및 배포 솔루션(예: Visual Studio Team Services 또는 Jenkins)





## <a name="admin-account"></a>관리자 계정
만드는 각 레지스트리에는 관리자 계정이 자동으로 만들어집니다. 기본적으로 계정은 사용할 수 없지만 [포털](container-registry-get-started-portal.md#manage-registry-settings) 또는 [Azure CLI 2.0 명령](container-registry-get-started-azure-cli.md#manage-admin-credentials)을 통해 계정을 사용하도록 설정하고 자격 증명을 관리할 수 있습니다. 각 관리자 계정은 두 개의 암호가 제공되며, 둘 다 다시 생성할 수 있습니다. 두 개의 암호를 사용하면 다른 암호를 다시 생성하는 동안에 하나의 암호를 사용하여 레지스트리에 대한 연결을 유지할 수 있습니다. 계정을 사용할 수 있으면 레지스트리에 대한 기본 인증을 위해 사용자 이름과 둘 중 한 가지 암호를 `docker login` 명령에 전달할 수 있습니다. 예:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> 관리자 계정은 주로 테스트 용도로 단일 사용자가 레지스트리에 액세스하도록 설계되었습니다. 관리자 계정 자격 증명을 다른 사용자와 공유하지 않는 것이 좋습니다. 모든 사용자는 레지스트리에서 단일 사용자로 나타납니다. 이 계정을 변경하거나 사용하지 않도록 설정하면 자격 증명을 사용하는 모든 사용자의 레지스트리 액세스는 허용되지 않습니다.
>


### <a name="next-steps"></a>다음 단계
* [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)
* 컨테이너 레지스트리 미리 보기의 인증에 대한 자세한 내용은 [블로그 게시물](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/)을 참조하세요.

