---
title: Azure Red Hat 오픈시프트 클러스터 관리자 역할 | 마이크로 소프트 문서
description: Azure Red Hat OpenShift 클러스터 관리자 역할할당 및 사용
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139099"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat 오픈시프트 고객 관리자 역할

Azure Red Hat OpenShift 클러스터의 클러스터 관리자입니다. 계정에서 모든 사용자가 만든 프로젝트에 대한 사용 권한과 액세스 권한을 늘렸습니다.

계정에 고객-관리자-클러스터 권한 부여 역할이 바인딩된 경우 프로젝트를 자동으로 관리할 수 있습니다.

> [!Note] 
> 고객-관리자 클러스터 클러스터 역할은 클러스터-관리자 클러스터 역할과 다 다 다 다.

예를 들어 동사 집합()과`create`연결된 작업을 실행하여 리소스 이름 집합()에서`templates`작동할 수 있습니다. 이러한 역할 및 동사 및 리소스 집합의 세부 정보를 보려면 다음 명령을 실행합니다.

`$ oc get clusterroles customer-admin-cluster -o yaml`

동사 이름이 반드시 모든 맵이 `oc` 명령에 직접 매핑되는 것은 아닙니다. 일반적으로 수행할 수 있는 CLI 작업의 형식과 동일합니다. 

예를 들어 `list` 동사를 사용하면 리소스 이름()의`oc get`모든 개체 목록을 표시할 수 있습니다. `get` 동사는 이름()을`oc describe`알고 있는 경우 특정 개체의 세부 정보를 표시할 수 있음을 의미합니다.

## <a name="configure-the-customer-administrator-role"></a>고객 관리자 역할 구성

플래그를 `--customer-admin-group-id`제공하여 클러스터를 만드는 동안에만 고객-관리자 클러스터 역할을 구성할 수 있습니다. 이 필드는 Azure 포털에서 현재 구성할 수 없습니다. Azure Active Directory 및 관리자 그룹을 구성하는 방법에 대해 알아보려면 [Azure Red Hat OpenShift에 대한 Azure Active Directory 통합을](howto-aad-app-configuration.md)참조하십시오.

## <a name="confirm-membership-in-the-customer-administrator-role"></a>고객 관리자 역할의 구성원 확인

고객 관리자 그룹의 구성원을 확인하려면 OpenShift CLI `oc get nodes` 명령 `oc projects`또는 을 사용해 보십시오. `oc get nodes`고객-관리자-클러스터 역할이 있는 경우 노드 목록이 표시되고 고객-관리자-프로젝트 역할만 있는 경우 권한 오류가 표시됩니다. `oc projects`을 사용하면 작업 중인 프로젝트만 이 와 반대로 클러스터의 모든 프로젝트가 표시됩니다.

클러스터의 역할 및 사용 권한을 자세히 살펴보려면 [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) 명령을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

고객-관리자 클러스터 클러스터 역할 구성:
> [!div class="nextstepaction"]
> [Azure Red Hat 오픈시프트용 Azure Active Directory 통합](howto-aad-app-configuration.md)
