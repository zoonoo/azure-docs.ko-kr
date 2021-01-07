---
title: Azure Red Hat OpenShift에서 보안 컨텍스트 제약 조건 관리 Microsoft Docs
description: Azure Red Hat OpenShift 클러스터 관리자에 대 한 보안 컨텍스트 제약 조건
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: eb5c568f056a99187a0e7a78a6f89b206f2d8dec
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220247"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에서 보안 컨텍스트 제약 조건 관리

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11는 30 월 2022에 사용 중지 됩니다. 새 Azure Red Hat OpenShift 3.11 클러스터 만들기에 대 한 지원은 30 년 11 2020 월 30 일까 지 계속 됩니다. 사용 중지 후에는 나머지 Azure Red Hat OpenShift 3.11 클러스터가 종료 되어 보안 취약점을 방지 합니다.
> 
> 이 가이드에 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 특정 질문이 있는 경우 문의해 주시기 [바랍니다](mailto:arofeedback@microsoft.com).

SCCs (보안 컨텍스트 제약 조건)를 사용 하면 클러스터 관리자가 pod에 대 한 사용 권한을 제어할 수 있습니다. 이 API 형식에 대해 자세히 알아보려면 [SCCs에 대 한 아키텍처 설명서](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html)를 참조 하세요. CLI를 사용 하 여 인스턴스에서 SCCs를 일반 API 개체로 관리할 수 있습니다.

## <a name="list-security-context-constraints"></a>보안 컨텍스트 제약 조건 나열

SCCs의 현재 목록을 가져오려면 다음 명령을 사용 합니다. 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>개체에서 보안 컨텍스트 제약 조건을 검사 합니다.

특정 SCC를 검사 하려면 `oc get` , 또는를 사용 `oc describe` `oc edit` 합니다.  예를 들어 **제한** 된 SCC를 검사 하려면 다음 명령을 사용 합니다.
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 
