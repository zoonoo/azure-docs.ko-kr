---
title: Azure Red Hat OpenShift 클러스터 업그레이드
description: OpenShift 4를 실행 하는 Azure Red Hat OpenShift 클러스터를 업그레이드 하는 방법을 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 98ab8ff1e321929a9007c28f487d5861f6ac9930
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063960"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift (ARO) 클러스터 업그레이드

ARO 클러스터 수명 주기 중 일부에는 최신 OpenShift 버전으로 정기적으로 업그레이드 하는 작업이 포함 됩니다. 최신 기능을 얻으려면 최신 보안 릴리스를 적용 하거나 업그레이드 하는 것이 중요 합니다. 이 문서에서는 OpenShift 웹 콘솔을 사용 하 여 OpenShift 클러스터의 모든 구성 요소를 업그레이드 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전의 2.0.65를 실행 해야 합니다. `az --version`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

이 문서에서는 권한이 있는 사용자로 기존 Azure Red Hat OpenShift 클러스터에 대 한 액세스 권한이 있다고 가정 `admin` 합니다.

## <a name="check-for-available-aro-cluster-upgrades"></a>사용 가능한 ARO 클러스터 업그레이드 확인

Openshift 웹 콘솔에서 **관리**  >  **클러스터 설정** 을 선택 하 고 **세부 정보** 탭을 엽니다.

클러스터에 대 한 **업데이트 상태가** **사용 가능한 업데이트** 를 반영 하는 경우 클러스터를 업데이트할 수 있습니다.

## <a name="upgrade-your-aro-cluster"></a>ARO 클러스터 업그레이드

이전 단계의 웹 콘솔에서와 같이 업데이트 하려는 버전에 대 한 올바른 채널로 **채널** 을 설정 `stable-4.5` 합니다.

업데이트할 버전을 선택 하 고 **업데이트** 를 선택 합니다. 업데이트 상태가로 변경 되는 것을 볼 수 있습니다 `Update to <product-version> in progress` . 운영자 및 노드의 진행률 표시줄을 확인 하 여 클러스터 업데이트의 진행률을 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [OC CLI를 사용 하 여 ARO 클러스터를 업그레이드 하는 방법을 알아봅니다.](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- 사용할 수 있는 OpenShift Container Platform 권고 및 업데이트에 대 한 정보는 고객 포털의 [정오표 섹션](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) 에서 찾을 수 있습니다.
  
