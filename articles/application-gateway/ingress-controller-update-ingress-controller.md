---
title: 헬름으로 어그레 컨트롤러 업그레이드
description: 이 문서에서는 Helm을 사용하여 응용 프로그램 게이트웨이 수집을 업그레이드하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795894"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Helm을 사용하여 Application Gateway 수신 컨트롤러를 업그레이드하는 방법 

Kubernetes(AGIC)에 대한 Azure 응용 프로그램 게이트웨이 침투 컨트롤러는 Azure 저장소에서 호스팅되는 Helm 리포지토리를 사용하여 업그레이드할 수 있습니다.

업그레이드 절차를 시작하기 전에 필요한 리포지토리를 추가해야 합니다.

- 다음과 함께 현재 추가된 Helm 리포지토리보기:

    ```bash
    helm repo list
    ```

- AGIC 리포지토리를 다음과 같은 다음으로 추가합니다.

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>업그레이드

1. AGIC 헬름 리포지토리를 새로 고쳐 최신 릴리스를 가져옵니다.

    ```bash
    helm repo update
    ```

1. `application-gateway-kubernetes-ingress` 차트의 사용 가능한 버전 보기:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    샘플 응답:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    위의 목록에서 사용할 수 있는 최신 버전은 다음과 같이 표시됩니다.`0.7.0-rc1`

1. 현재 설치된 투구 차트 보기:

    ```bash
    helm list
    ```

    샘플 응답:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    위의 샘플 응답에서 헬름 차트 `odd-billygoat`설치의 이름이 지정됩니다. 이 이름은 나머지 명령에 사용합니다. 실제 배포 이름은 다를 가능성이 높습니다.

1. Helm 배포를 새 버전으로 업그레이드합니다.

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>롤백

Helm 배포가 실패하면 이전 릴리스로 롤백할 수 있습니다.

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

    `helm history` 명령의 샘플 출력에서 우리의 `odd-billygoat` 마지막 성공적인 배포는 개정했다처럼 보인다`1`

1. 마지막으로 성공한 개정으로 롤백:

    ```bash
    helm rollback odd-billygoat 1
    ```
