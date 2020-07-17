---
title: 투구로 수신 컨트롤러 업그레이드
description: 이 문서에서는 투구를 사용 하 여 Application Gateway 수신을 업그레이드 하는 방법에 대 한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84806773"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Helm을 사용하여 Application Gateway 수신 컨트롤러를 업그레이드하는 방법 

Azure Storage에서 호스트 되는 투구 리포지토리를 사용 하 여 Kubernetes 용 Azure 애플리케이션 게이트웨이 수신 컨트롤러 (AGIC)를 업그레이드할 수 있습니다.

업그레이드 절차를 시작 하기 전에 필요한 리포지토리를 추가 했는지 확인 합니다.

- 현재 추가 된 투구 리포지토리 보기:

    ```bash
    helm repo list
    ```

- 다음을 사용 하 여 AGIC 리포지토리를 추가 합니다.

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Upgrade

1. 최신 릴리스를 얻으려면 AGIC 투구 리포지토리를 새로 고칩니다.

    ```bash
    helm repo update
    ```

1. 사용 가능한 차트 버전 보기 `application-gateway-kubernetes-ingress` :

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    샘플 응답:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    위의 목록에서 사용할 수 있는 최신 버전은 다음과 같습니다.`0.7.0-rc1`

1. 현재 설치 되어 있는 투구 차트를 봅니다.

    ```bash
    helm list
    ```

    샘플 응답:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    위의 샘플 응답에서의 투구 차트 설치는로 지정 됩니다 `odd-billygoat` . 나머지 명령에는이 이름을 사용 합니다. 실제 배포 이름은 대부분 다를 수 있습니다.

1. 투구 배포를 새 버전으로 업그레이드:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>롤백

투구 배포에 실패 하는 경우 이전 릴리스로 롤백할 수 있습니다.

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

    명령의 샘플 출력에서 `helm history` 마지막으로 성공한 배포를 성공한 것 처럼 보입니다. `odd-billygoat``1`

1. 마지막으로 성공한 수정 버전으로 롤백:

    ```bash
    helm rollback odd-billygoat 1
    ```
