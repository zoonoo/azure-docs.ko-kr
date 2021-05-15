---
title: Helm을 사용하여 수신 컨트롤러 업그레이드
description: 이 문서에서는 Helm을 사용하여 Application Gateway 수신을 업그레이드하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84806773"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Helm을 사용하여 Application Gateway 수신 컨트롤러를 업그레이드하는 방법 

Azure Storage에서 호스트되는 Helm 리포지토리를 사용하여 Kubernetes용 Azure AGIC(Application Gateway 수신 컨트롤러)를 업그레이드할 수 있습니다.

업그레이드 절차를 시작하기 전에 필요한 리포지토리를 추가했는지 확인합니다.

- 현재 추가된 Helm 리포지토리 보기:

    ```bash
    helm repo list
    ```

- 다음을 통해 AGIC 리포지토리를 추가합니다.

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>업그레이드

1. 최신 릴리스를 얻으려면 AGIC Helm 리포지토리를 새로 고칩니다.

    ```bash
    helm repo update
    ```

1. 사용 가능한 `application-gateway-kubernetes-ingress` 차트 버전 보기:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    샘플 응답:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    위의 목록에서 사용할 수 있는 최신 버전은 `0.7.0-rc1`입니다.

1. 현재 설치되어 있는 Helm 차트 보기:

    ```bash
    helm list
    ```

    샘플 응답:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    위의 샘플 응답에서의 Helm 차트 설치는 `odd-billygoat`(으)로 이름이 지정됩니다. 나머지 명령에 이 이름을 사용합니다. 실제 배포 이름은 대부분 다를 수 있습니다.

1. 다음과 같이 Helm 배포를 새 버전으로 업그레이드합니다.

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>롤백

Helm 배포에 실패하는 경우 이전 릴리스로 롤백할 수 있습니다.

1. 마지막으로 알려진 정상 릴리스 번호를 가져옵니다.

    ```bash
    helm history odd-billygoat
    ```

    샘플 출력:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    `helm history` 명령의 샘플 출력에서 `odd-billygoat`의 마지막 성공적 배포는 Revision `1`인 것으로 보입니다.

1. 마지막으로 성공한 Revision으로 롤백합니다.

    ```bash
    helm rollback odd-billygoat 1
    ```
