---
title: Velero를 사용 하 여 Azure Red Hat OpenShift 4 클러스터 응용 프로그램 복원 만들기
description: Velero를 사용 하 여 Azure Red Hat OpenShift 클러스터 응용 프로그램의 복원을 만드는 방법을 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 9eac34d643ba0df4be79a064858c580c884de727
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078564"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Azure Red Hat OpenShift 4 클러스터 응용 프로그램 복원 만들기

이 문서에서는 Azure Red Hat OpenShift 4 클러스터 응용 프로그램 복원을 만들기 위한 환경을 준비 합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 필수 구성 요소를 설정 하 고 필요한 도구를 설치 합니다.
> * Azure Red Hat OpenShift 4 응용 프로그램 복원 만들기

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Azure Red Hat OpenShift 4 응용 프로그램 백업 만들기

Azure Red Hat OpenShift 4 응용 프로그램 백업을 만들려면 [Azure Red Hat openshift 4 백업 만들기](./howto-create-a-backup.md) 를 참조 하세요.

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Azure Red Hat OpenShift 4 응용 프로그램 복원

이러한 단계를 통해 이전에 Velero로 백업한 응용 프로그램을 복원할 수 있습니다.
현재 클러스터에서 인식 되는 백업 목록을 확인 하 여 복원에 사용할 수 있는 백업을 확인할 수 있습니다.  이 단계를 수행 하려면 다음 명령을 실행 해야 합니다.

_이 단계에서는 "Velero" 라는 프로젝트에 Velero를 설치 했다고 가정 합니다._

```bash
oc get backups -n velero
```

복원 하려는 백업이 있으면 다음 명령을 사용 하 여 복원을 수행 해야 합니다.

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

이 단계에서는 백업을 만들 때 이전 단계에서 백업한 Kubernetes 개체를 만듭니다.

복원 상태를 확인 하려면 다음 단계를 실행 합니다.

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
단계가 표시 되 면 `Completed` Azure Red Hat 4 응용 프로그램을 복원 해야 합니다.

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>포함 된 스냅숏을 사용 하 여 Azure Red Hat OpenShift 4 응용 프로그램 복원


Velero를 사용 하 여 영구 볼륨을 사용 하는 Azure Red Hat OpenShift 4 응용 프로그램의 복원을 만들려면 다음 명령을 사용 하 여 복원을 수행 해야 합니다.

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
이 단계에서는 백업을 만들 때 이전 단계에서 백업한 Kubernetes 개체를 만듭니다.

복원 상태를 확인 하려면 다음 단계를 실행 합니다.

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
단계가 표시 되 면 `Completed` Azure Red Hat 4 응용 프로그램을 복원 해야 합니다.

Velero를 사용 하 여 백업을 만들고 복원 하는 방법에 대 한 자세한 내용은 [OpenShift 리소스 백업 기본 방법을](https://www.openshift.com/blog/backup-openshift-resources-the-native-way) 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Red Hat OpenShift 4 클러스터 응용 프로그램이 복원 되었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Velero를 사용 하 여 OpenShift v4 클러스터 응용 프로그램 복원 만들기
> * Velero를 사용 하 여 스냅숏을 사용 하 여 OpenShift v4 클러스터 응용 프로그램 복원 만들기


다음 문서로 이동 하 여 Azure Red Hat OpenShift 4 지원 되는 리소스에 대해 알아보세요.

* [Azure Red Hat OpenShift v4 지원 되는 리소스](supported-resources.md)


