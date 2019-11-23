---
title: Azure Red Hat OpenShift 클러스터 관리자 역할 | Microsoft Docs
description: Azure Red Hat OpenShift 클러스터 관리자 역할의 할당 및 사용
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936922"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 고객 관리자 역할

Azure Red Hat OpenShift 클러스터의 클러스터 관리자입니다. 사용자 계정의 사용 권한 및 사용자가 만든 모든 프로젝트에 대 한 액세스 권한이 증가 했습니다.

계정에 바인딩된 osa-고객 관리 권한 부여 역할이 있는 경우 프로젝트를 자동으로 관리할 수 있습니다.

> [!Note] 
> Osa-고객 관리 클러스터 역할은 클러스터 관리자 클러스터 역할과 동일 하지 않습니다.


예를 들어`create`(동사 집합)와 연결 된 작업을 실행 하 여 리소스 이름 집합에 대해 작업을 수행할 수 있습니다 (`templates`). 이러한 역할 및 해당 동사 및 리소스 집합에 대 한 세부 정보를 보려면 다음 명령을 실행 합니다.

`$ oc describe clusterrole/osa-customer-admin`

동사 이름이 반드시 `oc` 명령에 직접 매핑할 필요는 없습니다. 이러한 작업은 일반적으로 수행할 수 있는 CLI 작업 유형에 더 일반적입니다. 

예를 들어 `list` 동사가 있으면 리소스 이름 (`oc get`)의 모든 개체 목록을 표시할 수 있습니다. `get` 동사는 이름을 알고 있는 경우 (`oc describe`) 특정 개체의 세부 정보를 표시할 수 있음을 의미 합니다.

## <a name="configure-the-customer-administrator-role"></a>고객 관리자 역할 구성

`--customer-admin-group-id`플래그를 제공 하 여 클러스터를 만드는 동안에만 고객 관리자 역할을 구성할 수 있습니다. Azure Active Directory 및 관리자 그룹을 구성 하는 방법에 [대 한 자세한 내용은 Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합](howto-aad-app-configuration.md)(영문)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Osa-고객-관리자 역할을 구성 합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합](howto-aad-app-configuration.md)
