---
title: 자습서 - Azure Red Hat OpenShift 클러스터 삭제
description: 이 자습서에서는 Azure CLI를 사용하여 Azure Red Hat OpenShift 클러스터를 삭제하는 방법 알아보기
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232141"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>자습서: Azure Red Hat OpenShift 4 클러스터 삭제

이 자습서의 3 부에서는 OpenShift 4를 실행 하는 Azure Red Hat OpenShift 클러스터가 삭제 됩니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 삭제


## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 Azure Red Hat OpenShift 클러스터가 만들어지고 OpenShift 웹 콘솔을 사용 하 여 연결 되었습니다. 이러한 단계를 수행 하지 않고 팔 로우를 수행 하려는 경우 [자습서 1-Azure Red Hat Openshift 4 클러스터 만들기](tutorial-create-cluster.md) 로 시작 합니다.

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 자습서에서는 Azure CLI 버전 2.0.75 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure CLI를 로컬로 실행하는 경우 `az login`을 실행하여 Azure에 로그인합니다.

```bash
az login
```

여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

이전 자습서에서는 다음 변수가 설정 되었습니다. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

이러한 값을 사용 하 여 클러스터를 삭제 합니다.

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

그런 다음 클러스터를 삭제할지 여부를 확인 하는 메시지가 표시 됩니다. `y`로 확인한 후에 클러스터를 삭제하는 데 몇 분 정도 걸립니다. 명령이 완료 되 면 전체 리소스 그룹과 그 안에 포함 된 모든 리소스 (클러스터 포함)가 삭제 됩니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Red Hat OpenShift 4 클러스터 삭제

공식 [Red Hat OpenShift 설명서](https://www.openshift.com/try)를 통해 OpenShift 사용에 관해 자세히 알아봅니다.
