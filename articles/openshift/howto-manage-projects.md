---
title: Azure Red Hat OpenShift에서 리소스 관리 | Microsoft Docs
description: Azure Red Hat OpenShift 클러스터에서 프로젝트, 템플릿, 이미지-스트림 관리
services: openshift
keywords: Red Hat OpenShift 프로젝트는 자체 프로비저닝 프로그램을 요청합니다.
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.openlocfilehash: bf2cf5a0d41af15821035c615fe071c8580e125f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633107"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 프로젝트, 템플릿, 이미지-스트림 관리

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월 30일부터 사용이 중지됩니다. 새로운 Azure Red Hat OpenShift 3.11 클러스터 만들기 지원은 2020년 11월 30일까지 계속됩니다. 사용 중지 후에는 보안 취약성을 방지하기 위해 남아 있는 Azure Red Hat OpenShift 3.11 클러스터가 종료됩니다.
> 
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 궁금한 점은 [문의하세요](mailto:arofeedback@microsoft.com).

OpenShift 컨테이너 플랫폼에서 프로젝트는 관련 개체를 그룹화하고 격리하는 데 사용됩니다. 관리자는 개발자에게 특정 프로젝트에 대한 액세스 권한을 부여하고 본인의 프로젝트를 만들 수 있도록 하며 개별 프로젝트에 대한 관리 권한을 부여할 수 있습니다.

## <a name="self-provisioning-projects"></a>자체 프로비저닝 프로젝트

개발자가 본인의 프로젝트를 만드는 데 사용할 수 있습니다. API 엔드포인트는 project-request라는 템플릿에 따라 프로젝트를 프로비저닝합니다. 웹 콘솔과 `oc new-project` 명령은 개발자가 새 프로젝트를 만들 때 이 엔드포인트를 사용합니다.

프로젝트 요청이 제출되면 API는 템플릿에서 다음 매개 변수를 대체합니다.

| 매개 변수               | 설명                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 프로젝트의 이름입니다. 필수 요소.             |
| PROJECT_DISPLAYNAME     | 프로젝트의 표시 이름입니다. 비어 있을 수 있습니다. |
| PROJECT_DESCRIPTION     | 프로젝트에 대한 설명입니다. 비어 있을 수 있습니다.  |
| PROJECT_ADMIN_USER      | 관리 사용자의 사용자 이름입니다.       |
| PROJECT_REQUESTING_USER | 요청 사용자의 사용자 이름입니다.           |

API에 대한 액세스는 자체 프로비저닝 프로그램 클러스터 역할 바인딩을 통해 개발자에게 부여됩니다. 이 기능은 기본적으로 모든 인증된 개발자가 사용할 수 있습니다.

## <a name="modify-the-template-for-a-new-project"></a>새 프로젝트의 템플릿 수정 

1. `customer-admin` 권한이 있는 사용자로 로그인합니다.

2. 기본 project-request 템플릿을 편집합니다.

   ```
   oc edit template project-request -n openshift
   ```

3. `openshift.io/reconcile-protect: "true"` 주석을 추가하여 ARO(Azure Red Hat OpenShift) 업데이트 프로세스에서 기본 프로젝트 템플릿을 제거합니다.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   project-request 템플릿은 ARO 업데이트 프로세스에 의해 업데이트되지 않습니다. 이를 통해 고객이 템플릿을 사용자 지정하고 클러스터를 업데이트할 때 이러한 사용자 지정을 유지할 수 있습니다.

## <a name="disable-the-self-provisioning-role"></a>자체 프로비저닝 역할 사용하지 않음

인증된 사용자 그룹이 새 프로젝트를 자체 프로비저닝하지 못하도록 방지할 수 있습니다.

1. `customer-admin` 권한이 있는 사용자로 로그인합니다.

2. 자체 프로비저닝 프로그램 클러스터 역할 바인딩을 편집합니다.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. `openshift.io/reconcile-protect: "true"` 주석을 추가하여 ARO 업데이트 프로세스에서 역할을 제거합니다.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. `system:authenticated:oauth`이 프로젝트를 만들지 못하도록 클러스터 역할 바인딩을 변경합니다.

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

## <a name="manage-default-templates-and-imagestreams"></a>기본 템플릿 및 imageStreams 관리

Azure Red Hat OpenShift에서 `openshift` 네임스페이스 내의 기본 템플릿 및 이미지-스트림의 업데이트를 사용하지 않도록 설정할 수 있습니다.
`openshift` 네임스페이스에 있는 모든 `Templates`과 `ImageStreams`의 업데이트를 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1. `customer-admin` 권한이 있는 사용자로 로그인합니다.

2. `openshift` 네임 스페이스 편집

   ```
   oc edit namespace openshift
   ```

3. `openshift.io/reconcile-protect: "true"` 주석을 추가하여 ARO 업데이트 프로세스에서 `openshift` 네임스페이스를 제거합니다.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   `openshift` 네임스페이스에 `openshift.io/reconcile-protect: "true"` 주석을 추가하여 해당 네임스페이스의 개별 개체를 업데이트 프로세스에서 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 자습서를 실행해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
