---
title: Azure Red Hat OpenShift 클러스터 관리자 역할 | Microsoft Docs
description: Azure Red Hat OpenShift 클러스터 관리자 역할 할당 및 사용
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636246"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 고객 관리자 역할

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월 30일부터 사용이 중지됩니다. 새로운 Azure Red Hat OpenShift 3.11 클러스터 만들기 지원은 2020년 11월 30일까지 계속됩니다. 사용 중지 후에는 보안 취약성을 방지하기 위해 남아 있는 Azure Red Hat OpenShift 3.11 클러스터가 종료됩니다.
> 
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 궁금한 점은 [Microsoft에 문의](mailto:arofeedback@microsoft.com)하세요.

사용자는 Azure Red Hat OpenShift 클러스터의 클러스터 관리자입니다. 사용자 계정은 사용 권한이 증가했고 사용자가 만든 모든 프로젝트에 액세스할 수 있습니다.

계정에 customer-admin-cluster 권한 부여 역할이 바인딩되어 있는 경우 프로젝트를 자동으로 관리할 수 있습니다.

> [!Note] 
> customer-admin-cluster 클러스터 역할은 cluster-admin 클러스터 역할과 동일하지 않습니다.

예를 들어, 동사 세트(`create`)에 연결된 작업을 실행하여 리소스 이름 세트(`templates`)에 대해 작업을 수행할 수 있습니다. 이러한 역할 및 해당 동사 및 리소스 세트에 대한 세부 정보를 보려면 다음 명령을 실행합니다.

`$ oc get clusterroles customer-admin-cluster -o yaml`

동사 이름이 반드시 `oc` 명령에 직접 매핑될 필요는 없습니다. 이러한 이름은 사용자가 수행할 수 있는 CLI 작업 유형에 더 일반적으로 매핑됩니다. 

예를 들어 `list` 동사가 있으면 특정 리소스 이름(`oc get`)의 모든 개체 목록을 표시할 수 있음을 의미합니다. `get` 동사는 개체 이름(`oc describe`)을 알고 있는 경우 해당 개체의 세부 정보를 표시할 수 있다는 의미입니다.

## <a name="configure-the-customer-administrator-role"></a>고객 관리자 역할 구성

`--customer-admin-group-id` 플래그를 제공하여 클러스터를 만드는 동안에만 customer-admin-cluster 클러스터 역할을 구성할 수 있습니다. 이 필드는 현재 Azure Portal에서 구성할 수 없습니다. Azure Active Directory 및 관리자 그룹을 구성하는 방법에 대한 자세한 내용은 [Azure Red Hat OpenShift를 위한 Azure Active Directory 통합](howto-aad-app-configuration.md)을 참조하세요.

## <a name="confirm-membership-in-the-customer-administrator-role"></a>고객 관리자 역할의 멤버 자격 확인

고객 관리자 그룹의 멤버 자격을 확인하려면 OpenShift CLI 명령 `oc get nodes` 또는 `oc projects`를 실행해봅니다. `oc get nodes`를 실행하면 customer-admin-cluster 역할이 있는 경우 노드 목록이 표시되고, customer-admin-project 역할만 있는 경우에는 사용 권한 오류가 표시됩니다. `oc projects`를 실행하면 작업 중인 프로젝트뿐 아니라 클러스터의 모든 프로젝트가 표시됩니다.

클러스터에서 역할 및 권한을 자세히 살펴보려면 [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) 명령을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

customer-admin-cluster 클러스터 역할을 구성합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift를 위한 Azure Active Directory 통합](howto-aad-app-configuration.md)
