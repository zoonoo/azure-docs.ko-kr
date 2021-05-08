---
title: Azure Red Hat OpenShift 클러스터 업그레이드
description: OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터를 업그레이드하는 방법 알아보기
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720888"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>ARO(Azure Red Hat OpenShift) 클러스터 업그레이드

ARO 클러스터 수명 주기 중 일부에는 최신 OpenShift 버전으로 정기적으로 업그레이드하는 작업이 포함됩니다. 최신 Kubernetes 보안 릴리스를 적용하거나 업그레이드하여 최신 기능을 받는 것은 중요합니다. 이 문서에서는 OpenShift 웹 콘솔을 사용하여 OpenShift 클러스터의 모든 구성 요소를 업그레이드하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하려면 Azure CLI 버전 2.0.65 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

이 문서에서는 여러분이 `admin` 권한이 있는 사용자로서 기존 Azure Red Hat OpenShift 클러스터에 대한 액세스 권한이 있다고 가정합니다.

## <a name="check-for-available-aro-cluster-upgrades"></a>사용 가능한 ARO 클러스터 업그레이드 확인

OpenShift 웹 콘솔에서 **관리** > **클러스터 설정** 을 선택하고 **세부 정보** 탭을 엽니다.

클러스터의 **업데이트 상태** 가 **업데이트 가능** 이라고 나타나는 경우 클러스터를 업데이트할 수 있습니다.

## <a name="upgrade-your-aro-cluster"></a>ARO 클러스터 업그레이드

이전 단계의 웹 콘솔에서 `stable-4.5`와 같이 업데이트하려는 버전에 대한 올바른 채널로 **채널** 을 설정합니다.

업데이트할 버전을 선택하고 **업데이트** 를 선택합니다. 업데이트 상태가 `Update to <product-version> in progress`로 변경되는 것을 볼 수 있습니다. 운영자 및 노드의 진행률 표시줄을 확인하여 클러스터 업데이트의 진행률을 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [OC CLI를 사용하여 ARO 클러스터를 업그레이드하는 방법 알아보기](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- OpenShift 컨테이너 플랫폼과 관련하여 제공되는 권고 및 업데이트에 대한 정보는 고객 포털의 [정오표 섹션](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata)에서 찾을 수 있습니다.
