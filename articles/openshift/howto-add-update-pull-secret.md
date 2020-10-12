---
title: Azure Red Hat OpenShift 4 클러스터에서 Red Hat pull 비밀 추가 또는 업데이트
description: 기존 4.x ARO 클러스터에서 Red Hat pull 비밀 추가 또는 업데이트
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull 비밀, aro, openshift, red hat
ms.openlocfilehash: 769b7589fb6496fc2f4123665ad1f6fe61d0cce2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294750"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에서 Red Hat pull 비밀 추가 또는 업데이트

이 가이드에서는 기존 Azure Red Hat OpenShift (ARO) 4.x 클러스터에 대 한 Red Hat pull 비밀을 추가 하거나 업데이트 하는 방법을 설명 합니다.

처음으로 클러스터를 만드는 경우 클러스터를 만들 때 끌어오기 암호를 추가할 수 있습니다. Red Hat pull 암호를 사용 하 여 ARO 클러스터를 만드는 방법에 대 한 자세한 내용은 [Azure Red Hat OpenShift 4 클러스터 만들기](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 가이드에서는 기존 Azure Red Hat OpenShift 4 클러스터가 있다고 가정 합니다. 클러스터에 대 한 관리자 액세스 권한이 있는지 확인 합니다.

## <a name="prepare-your-pull-secret"></a>끌어오기 비밀 준비
Red Hat pull 비밀을 추가 하지 않고 ARO 클러스터를 만드는 경우에도 끌어오기 암호는 클러스터에 자동으로 만들어집니다. 그러나이 끌어오기 암호는 완전히 채워지지 않습니다.

이 섹션에서는 Red Hat pull 암호의 추가 값을 사용 하 여 해당 끌어오기 암호를 업데이트 하는 과정을 안내 합니다.

1. `pull-secret` `openshift-config` 다음 명령을 실행 하 여 네임 스페이스에서 라는 암호를 인출 하 고 별도의 파일에 저장 합니다. 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    출력은 다음과 유사 합니다. 실제 비밀 값은 제거 되었습니다.

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. [Red Hat OpenShift 클러스터 관리자 포털로](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) 이동 하 여 **pull 비밀 다운로드**를 선택 합니다. Red Hat pull 비밀은 다음과 같습니다. 실제 비밀 값은 제거 되었습니다.

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Red Hat pull 비밀에 있는 항목을 추가 하 여 클러스터에서 가져온 끌어오기 비밀 파일을 편집 합니다. 

    > [!IMPORTANT]
    > `cloud.openshift.com`Red hat pull 비밀에서 항목을 포함 하면 클러스터에서 원격 분석 데이터를 Red hat로 보내기 시작 합니다. 원격 분석 데이터를 전송 하려는 경우에만이 섹션을 포함 합니다. 그렇지 않으면 다음 섹션을 종료 합니다.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > 사용자를 제거 하거나 변경 하지 마십시오 `arosvc.azurecr.io` . 이 섹션은 클러스터가 제대로 작동 하는 데 필요 합니다.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    최종 파일은 다음과 같습니다. 실제 비밀 값은 제거 되었습니다.

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. 파일이 유효한 JSON 인지 확인 합니다. JSON의 유효성을 검사 하는 방법에는 여러 가지가 있습니다. 다음 예제에서는 jq를 사용 합니다.

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > 파일에 오류가 있는 경우로 표시 됩니다 `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>클러스터에 끌어오기 비밀 추가

다음 명령을 실행 하 여 끌어오기 암호를 업데이트 합니다.

> [!NOTE]
> 이 명령을 실행 하면 클러스터 노드가 업데이트 될 때 하나씩 다시 시작 됩니다. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

비밀을 설정한 후에는 Red Hat 인증 연산자를 사용할 준비가 된 것입니다.

### <a name="modify-the-configuration-files"></a>구성 파일 수정

Red Hat 연산자를 사용 하려면 다음 개체를 수정 합니다.

먼저 Samples Operator 구성 파일을 수정 합니다. 그런 다음, 다음 명령을 실행 하 여 구성 파일을 편집할 수 있습니다.

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

`spec.architectures.managementState`및 값을 `status.architecture.managementState` 에서 `Removed` 로 변경 `Managed` 합니다. 

다음 YAML 코드 조각은 편집 된 YAML 파일의 관련 섹션만 표시 합니다.

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

다음 명령을 실행 하 여 운영자 허브 구성 파일을 편집 합니다.  

```console
oc edit operatorhub cluster -o yaml
```

`Spec.Sources.Disabled` `Status.Sources.Disabled` `true` `false` 사용 하도록 설정 하려는 모든 원본에 대해 및 값을에서로 변경 합니다.

다음 YAML 코드 조각은 편집 된 YAML 파일의 관련 섹션만 표시 합니다.

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

편집 내용을 적용 하려면 파일을 저장 합니다.

## <a name="validate-that-your-secret-is-working"></a>비밀이 작동 하는지 확인

끌어오기 암호를 추가 하 고 올바른 구성 파일을 수정한 후에는 클러스터를 업데이트 하는 데 몇 분 정도 걸릴 수 있습니다. 클러스터가 업데이트 되었는지 확인 하려면 다음 명령을 실행 하 여 사용 가능한 인증 된 운영자 및 Red Hat Operators 원본을 표시 합니다.

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

인증 된 연산자와 Red Hat 연산자가 표시 되지 않으면 몇 분 정도 기다린 후 다시 시도 합니다.

끌어오기 암호를 업데이트 하 고 올바르게 작동 하는지 확인 하려면 OperatorHub를 열고 Red Hat 확인 된 운영자를 확인 합니다. 예를 들어 OpenShift Container Storage 연산자를 사용할 수 있는지 확인 하 고, 설치할 권한이 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
Red Hat pull 비밀에 대해 자세히 알아보려면 [이미지 끌어오기 비밀 사용](https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html)을 참조 하세요.

Red Hat OpenShift 4에 대해 자세히 알아보려면 [Azure Red Hat openshift 4](https://docs.openshift.com/aro/4/welcome/index.html)를 참조 하세요.
