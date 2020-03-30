---
title: Azure Red Hat 오픈시프트의 리소스 관리 | 마이크로 소프트 문서
description: Azure Red Hat OpenShift 클러스터에서 프로젝트, 템플릿, 이미지 스트림 관리
services: openshift
keywords: 레드 햇 오픈 시프트 프로젝트 요청 자체 프로비저너
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139116"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 프로젝트, 템플릿, 이미지 스트림 관리 

OpenShift 컨테이너 플랫폼에서 프로젝트는 관련 개체를 그룹화하고 격리하는 데 사용됩니다. 관리자는 개발자에게 특정 프로젝트에 대한 액세스 권한을 부여하고, 자체 프로젝트를 만들 수 있도록 허용하며, 개별 프로젝트에 대한 관리 권한을 부여할 수 있습니다.

## <a name="self-provisioning-projects"></a>자체 프로비저닝 프로젝트

개발자가 자체 프로젝트를 만들 수 있도록 설정할 수 있습니다. API 끝점은 프로젝트 요청이라는 템플릿에 따라 프로젝트를 프로비전합니다. 웹 콘솔과 `oc new-project` 명령은 개발자가 새 프로젝트를 만들 때 이 끝점을 사용합니다.

프로젝트 요청이 제출되면 API는 템플릿에서 다음 매개 변수를 대체합니다.

| 매개 변수               | 설명                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 프로젝트의 이름입니다. 필수 사항입니다.             |
| PROJECT_DISPLAYNAME     | 프로젝트의 표시 이름입니다. 비어 있을 수 있습니다. |
| PROJECT_DESCRIPTION     | 프로젝트에 대한 설명입니다. 비어 있을 수 있습니다.  |
| PROJECT_ADMIN_USER      | 관리 사용자의 사용자 이름입니다.       |
| PROJECT_REQUESTING_USER | 요청하는 사용자의 사용자 이름입니다.           |

API에 대한 액세스는 자체 프로비저러 클러스터 역할 바인딩이 있는 개발자에게 부여됩니다. 이 기능은 기본적으로 모든 인증된 개발자가 사용할 수 있습니다.

## <a name="modify-the-template-for-a-new-project"></a>새 프로젝트의 템플릿 수정 

1. 권한이 있는 `customer-admin` 사용자로 로그인합니다.

2. 기본 프로젝트 요청 템플릿을 편집합니다.

   ```
   oc edit template project-request -n openshift
   ```

3. 다음과 같은 추가 를 추가하여 Azure Red Hat OpenShift(ARO) 업데이트 프로세스에서 기본 프로젝트 템플릿을 제거합니다.`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   ARO 업데이트 프로세스에 의해 프로젝트 요청 템플릿이 업데이트되지 않습니다. 이를 통해 고객은 템플릿을 사용자 지정하고 클러스터가 업데이트될 때 이러한 사용자 지정을 유지할 수 있습니다.

## <a name="disable-the-self-provisioning-role"></a>자체 프로비저닝 역할 사용 안 함

인증된 사용자 그룹이 새 프로젝트를 자체 프로비전하지 못하도록 할 수 있습니다.

1. 권한이 있는 `customer-admin` 사용자로 로그인합니다.

2. 자체 프로비저닝 클러스터 역할 바인딩을 편집합니다.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. ARO 업데이트 프로세스에서 다음 주석을 추가하여 역할을 `openshift.io/reconcile-protect: "true"`제거합니다.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 클러스터 역할 바인딩을 `system:authenticated:oauth` 변경하여 프로젝트를 만들지 못하게 합니다.

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>기본 템플릿 및 이미지 스트림 관리

Azure Red Hat OpenShift에서 네임스페이스 내의 `openshift` 기본 템플릿 및 이미지 스트림에 대한 업데이트를 비활성화할 수 있습니다.
ALL `Templates` 및 `ImageStreams` 네임스페이스에 대한 업데이트를 사용하지 않으려면 다음을 수행합니다. `openshift`

1. 권한이 있는 `customer-admin` 사용자로 로그인합니다.

2. 네임스페이스 편집: `openshift`

   ```
   oc edit namespace openshift
   ```

3. ARO `openshift` 업데이트 프로세스에서 다음 추가 된 추가 를 통해 네임 스페이스를 제거합니다.`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   네임스페이스의 `openshift` 모든 개별 개체는 추가된 추가를 `openshift.io/reconcile-protect: "true"` 추가하여 업데이트 프로세스에서 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

튜토리얼을 보십시오 :
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
