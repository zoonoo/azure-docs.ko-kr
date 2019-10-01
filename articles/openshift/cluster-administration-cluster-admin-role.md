---
title: Azure Red Hat OpenShift 클러스터 관리자 역할 | Microsoft Docs
description: Azure Red Hat OpenShift 클러스터 관리자 역할 할당 및 사용
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709948"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 고객 관리자 역할

OpenShift 클러스터의 ARO (Azure Red Hat OpenShift) 클러스터 관리자는 계정에 사용 권한이 늘어나고 사용자가 만든 모든 프로젝트에 대 한 액세스 권한이 있습니다.

계정에 바인딩된 osa-고객-관리자 권한 부여 역할이 있으면 자동으로 프로젝트를 관리할 수 있습니다.

> [!Note] 
> osa-customer-admin clusterrole은 cluster-admin clusterrole와 다릅니다.


예를 들어 일련의 (`create`)와 연결 된 작업을 실행 하 여 일련의 리소스 이름 (`templates`)에 대해 작업을 수행할 수 있습니다. 이러한 역할 및 해당 동사 및 리소스 집합에 대 한 세부 정보를 보려면 다음 명령을 실행 합니다.

`$ oc describe clusterrole/osa-customer-admin`

동사 이름은 모두 oc 명령에 직접 매핑할 필요는 없지만 일반적으로 수행할 수 있는 CLI 작업 유형에 더 일반적으로 해당 합니다. 예를 들어 `list` 동사는 지정 된 리소스 이름 (`oc get`)의 모든 개체 목록을 표시할 수 있음을 의미 하 고, `get` 동사는 해당 이름을 알고 있는 경우 (`oc describe`) 특정 개체의 세부 정보를 표시할 수 있음을 의미 합니다.

## <a name="how-to-configure-customer-administrator-role"></a>고객 관리자 역할을 구성 하는 방법

고객 관리자 역할은 `--customer-admin-group-id`에 플래그를 제공 하 여 클러스터를 만드는 동안에만 구성할 수 있습니다. Azure Active Directory 및 Administrators 그룹을 구성 하는 방법 가이드를 참조 하세요. [Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합](howto-aad-app-configuration.md)

## <a name="next-steps"></a>다음 단계

Osa를 구성 하는 방법-고객-관리자 역할:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합](howto-aad-app-configuration.md)
