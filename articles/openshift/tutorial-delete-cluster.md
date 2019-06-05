---
title: 자습서 - Azure Red Hat OpenShift 클러스터 삭제 | Microsoft Docs
description: 이 자습서에서는 Azure CLI를 사용하여 Azure Red Hat OpenShift 클러스터를 삭제하는 방법 알아보기
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 627acbfc1f3a460cbb94e322c43445a55fce1ffa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306157"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>자습서: Azure Red Hat OpenShift 클러스터 삭제

이 자습서의 마지막 부분입니다. 샘플 클러스터의 테스트를 완료한 경우 사용하지 않는 항목에 대해 요금이 부과되지 않도록 해당 클러스터 및 연결된 리소스를 삭제하는 방법입니다.

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 삭제

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
> * [Azure Red Hat OpenShift 클러스터 크기 조정](tutorial-scale-cluster.md)
> * Azure Red Hat OpenShift 클러스터 삭제

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서를 따라 클러스터를 만듭니다.

## <a name="step-1-sign-in-to-azure"></a>1단계: Azure에 로그인

Azure CLI를 로컬로 실행하는 경우 `az login`을 실행하여 Azure에 로그인합니다.

```bash
az login
```

여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꾼 후 실행합니다.

## <a name="step-2-delete-the-cluster"></a>2단계: 클러스터 삭제

Bash 터미널을 열고 변수 CLUSTER_NAME을 클러스터의 이름으로 설정합니다.

```bash
CLUSTER_NAME=yourclustername
```

이제 클러스터를 삭제합니다.

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

클러스터를 삭제하려는지 묻는 메시지가 표시됩니다. `y`로 확인한 후에 클러스터를 삭제하는 데 몇 분 정도 걸립니다. 명령이 완료되면 전체 리소스 그룹 및 클러스터를 포함한 내부의 모든 리소스가 삭제됩니다.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Azure Portal을 사용하여 클러스터 삭제

또는 온라인 Azure Portal을 통해 클러스터의 연결된 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹의 이름은 사용자의 클러스터 이름과 동일합니다.

현재 클러스터를 만들 때 생성된 `Microsoft.ContainerService/openShiftManagedClusters` 리소스는 Azure Portal에서 숨겨져 있습니다. `Resource group` 보기에서 `Show hidden types`를 선택하여 리소스 그룹을 봅니다.

![숨겨진 형식 확인란의 스크린샷](./media/aro-portal-hidden-type.png)

리소스 그룹을 삭제하면 Azure Red Hat OpenShift 클러스터를 빌드할 때 생성되는 관련 리소스가 모두 삭제됩니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 삭제

공식 [Red Hat OpenShift 설명서](https://docs.openshift.com/aro/welcome/index.html)를 통해 OpenShift 사용에 관해 자세히 알아봅니다.