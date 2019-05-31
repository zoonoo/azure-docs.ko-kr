---
title: 자습서 - Azure Red Hat OpenShift 클러스터 크기 조정 | Microsoft Docs
description: Azure CLI를 사용하여 Microsoft Azure Red Hat OpenShift 클러스터의 크기를 조정하는 방법 알아보기
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: b25e17e7064006a1421142dfcd32997cb4426e8e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305977"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>자습서: Azure Red Hat OpenShift 클러스터 크기 조정

이 자습서는 시리즈의 2부입니다. Azure CLI를 사용하여 Microsoft Azure Red Hat OpenShift 클러스터를 만들고, 크기를 조정한 다음, 삭제하여 리소스를 정리하는 방법을 알아봅니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Red Hat OpenShift 클러스터 크기 조정

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
> * Azure Red Hat OpenShift 클러스터 크기 조정
> * [Azure Red Hat OpenShift 클러스터 삭제](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서를 따라 클러스터를 만듭니다.

## <a name="step-1-sign-in-to-azure"></a>1단계: Azure에 로그인

Azure CLI를 로컬로 실행하는 경우 `az login`을 실행하여 Azure에 로그인합니다.

```bash
az login
```

여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꾼 후 실행합니다.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>2단계: 추가 노드를 사용하여 클러스터 크기 조정

Bash 터미널에서 변수 CLUSTER_NAME을 클러스터의 이름으로 설정합니다.

```bash
CLUSTER_NAME=yourclustername
```

이제 Azure CLI를 사용하여 클러스터 크기를 5개 노드로 조정해보겠습니다.

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

몇 분 후 `az openshift scale`이 성공적으로 완료되고 크기가 조정된 클러스터 세부 정보를 포함하는 JSON 문서를 반환합니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 크기 조정

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 삭제](tutorial-delete-cluster.md)