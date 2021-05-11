---
title: Azure Red Hat OpenShift 4 클러스터에서 Red Hat 끌어오기 암호 추가 또는 업데이트
description: 기존 4.x ARO 클러스터에서 Red Hat 끌어오기 암호 추가 또는 업데이트
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/21/2020
keywords: 끌어오기 암호, ARO, OpenShift, Red Hat
ms.openlocfilehash: 58c0eb2be3423783a69d005277ffe75aaf59415f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633736"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에서 Red Hat 끌어오기 암호 추가 또는 업데이트

이 가이드에서는 기존 ARO(Azure Red Hat OpenShift) 4.x 클러스터에 Red Hat 끌어오기 암호를 추가하거나 업데이트하는 방법을 설명합니다.

처음으로 클러스터를 만드는 경우 클러스터를 만들 때 끌어오기 암호를 추가할 수 있습니다. Red Hat 끌어오기 암호를 사용하여 ARO 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Red Hat OpenShift 4 클러스터 만들기](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 가이드에서는 기존 Azure Red Hat OpenShift 4 클러스터가 있다고 가정합니다. 클러스터에 대한 관리자 액세스 권한이 있는지 확인합니다.

## <a name="prepare-your-pull-secret"></a>끌어오기 암호 준비
Red Hat 끌어오기 암호를 추가하지 않고 ARO 클러스터를 만드는 경우에도 끌어오기 암호는 클러스터에 자동으로 만들어집니다. 그러나 이 끌어오기 암호는 완전히 채워지지 않은 상태입니다.

이 섹션에서는 Red Hat 끌어오기 암호의 추가 값을 사용하여 해당 끌어오기 암호를 업데이트하는 과정을 안내합니다.

1. 다음 명령을 실행하여 `openshift-config` 네임스페이스에서 `pull-secret`이라는 암호를 가져와서 별도의 파일에 저장합니다. 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    출력은 다음과 유사합니다. (실제 암호 값은 제거되었습니다.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. [Red Hat OpenShift 클러스터 관리자 포털](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)로 이동하여 **끌어오기 암호 다운로드** 를 선택합니다. Red Hat 끌어오기 암호는 다음과 같습니다. (실제 암호 값은 제거되었습니다.)

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

3. Red Hat 끌어오기 암호에 있는 항목에 추가하여 클러스터에서 가져온 끌어오기 암호 파일을 편집합니다. 

    > [!IMPORTANT]
    > Red Hat 끌어오기 암호에서 `cloud.openshift.com` 항목을 포함하면 클러스터에서 원격 분석 데이터를 Red Hat으로 보내기 시작합니다. 원격 분석 데이터를 전송하려는 경우에만 이 섹션이 포함됩니다. 그러지 않은 경우 다음 섹션을 종료하세요.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > 끌어오기 암호에서 `arosvc.azurecr.io` 항목을 제거하거나 변경하지 마세요. 이 섹션은 클러스터가 제대로 작동하는 데 필요합니다.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    최종 파일은 다음과 같아야 합니다. (실제 암호 값은 제거되었습니다.)

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

4. 파일이 유효한 JSON인지 확인하세요. JSON은 여러 방법으로 유효성을 검사할 수 있습니다. 다음 예제에서는 jq를 사용합니다.

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > 파일에 오류가 있는 경우 `parse error`로 표시됩니다.

## <a name="add-your-pull-secret-to-your-cluster"></a>클러스터에 끌어오기 암호 추가

다음 명령을 실행하여 끌어오기 암호를 업데이트합니다.

> [!NOTE]
> 이 명령을 실행하면 클러스터 노드가 업데이트될 때 하나씩 다시 시작됩니다. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

암호를 설정한 후에는 Red Hat 인증 연산자를 사용할 준비가 되었습니다.

### <a name="modify-the-configuration-files"></a>구성 파일 수정

Red Hat 연산자를 사용하려면 다음 개체를 수정합니다.

먼저 샘플 연산자 구성 파일을 수정합니다. 그다음에 다음 명령을 실행하여 구성 파일을 편집할 수 있습니다.

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

`spec.architectures.managementState`과 `status.architecture.managementState` 값을 `Removed`에서 `Managed`로 변경합니다. 

다음 YAML 코드 조각은 편집된 YAML 파일의 관련 섹션만 표시합니다.

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

두 번째로는 다음 명령을 실행하여 운영자 허브 구성 파일을 편집합니다.  

```console
oc edit operatorhub cluster -o yaml
```

사용하도록 설정하려는 모든 원본에 대해 `Spec.Sources.Disabled`과 `Status.Sources.Disabled` 값을 `true`에서 `false`로 변경합니다.

다음 YAML 코드 조각은 편집된 YAML 파일의 관련 섹션만 표시합니다.

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

편집 내용을 적용하려면 파일을 저장합니다.

## <a name="validate-that-your-secret-is-working"></a>암호가 작동하는지 확인

끌어오기 암호를 추가하고 올바른 구성 파일을 수정한 후에는 클러스터를 업데이트하는 데 몇 분 정도 걸릴 수 있습니다. 클러스터가 업데이트되었는지 확인하려면 다음 명령을 실행하여 사용 가능한 인증된 운영자 및 Red Hat 연산자 원본을 표시합니다.

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

인증 연산자와 Red Hat 연산자가 표시되지 않으면 몇 분 정도 기다린 후 다시 시도합니다.

끌어오기 암호를 업데이트하고 올바르게 작동하는지 확인하려면 OperatorHub를 열고 Red Hat의 확인된 운영자를 확인합니다. 예를 들면 OpenShift 컨테이너 스토리지 연산자를 사용할 수 있는지 확인하고, 설치할 권한이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계
Red Hat 끌어오기 암호에 대해 자세히 알아보려면 [이미지 끌어오기 암호 사용](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html)을 참조하세요.

Red Hat OpenShift 4에 대한 자세한 내용은 [Red Hat Openshift 컨테이너 플랫폼 설명서](https://docs.openshift.com/container-platform/4.6/welcome/index.html)를 참조하세요.
