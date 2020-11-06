---
title: Kubernetes tools를 사용 하 여 데이터 컨트롤러 만들기
description: Kubernetes tools를 사용 하 여 데이터 컨트롤러 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 051a7f506d351a17764e38c760ffba06d224cc38
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422572"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Kubernetes 도구를 사용 하 여 Azure Arc 데이터 컨트롤러 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

개요 정보는 [Azure Arc data Controller 만들기](create-data-controller.md) 항목을 검토 하세요.

Kubernetes 도구를 사용 하 여 Azure Arc 데이터 컨트롤러를 만들려면 Kubernetes 도구가 설치 되어 있어야 합니다.  이 문서의 예제에서는 `kubectl` 를 사용 하지만, 유사한 접근 방식은 Kubernetes 대시보드, 등의 다른 Kubernetes 도구와 함께 사용 `oc` 하거나 `helm` 이러한 도구와 Kubernetes yaml/json에 익숙한 경우에도 사용할 수 있습니다.

[Kubectl 도구 설치](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 아래에 표시 된 Azure Arc 데이터 컨트롤러를 만드는 몇 가지 단계에는 Kubernetes cluster administrator 권한이 필요 합니다.  Kubernetes 클러스터 관리자가 아닌 경우 Kubernetes 클러스터 관리자가 사용자를 대신 하 여 이러한 단계를 수행 해야 합니다.

### <a name="cleanup-from-past-installations"></a>이전 설치에서 정리

이전에 Azure Arc data controller를 동일한 클러스터에 설치 하 고 명령을 사용 하 여 Azure Arc data controller를 삭제 한 경우에 `azdata arc dc delete` 도 삭제 해야 하는 클러스터 수준 개체가 있을 수 있습니다. 다음 명령을 실행 하 여 Azure Arc 데이터 컨트롤러 클러스터 수준 개체를 삭제 합니다.

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

## <a name="overview"></a>개요

Azure Arc 데이터 컨트롤러를 만들 때는 다음과 같은 개략적인 단계를 수행 합니다.
1. Arc 데이터 컨트롤러, Azure SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale에 대 한 사용자 지정 리소스 정의를 만듭니다. **[Kubernetes 클러스터 관리자 권한 필요]**
2. 데이터 컨트롤러가 생성 되는 네임 스페이스를 만듭니다. **[Kubernetes 클러스터 관리자 권한 필요]**
3. 복제본 집합, 서비스 계정, 역할 및 역할 바인딩을 포함 하는 부트스트래퍼 서비스를 만듭니다.
4. 데이터 컨트롤러 관리자의 사용자 이름 및 암호에 대 한 암호를 만듭니다.
5. 데이터 컨트롤러를 만듭니다.
   
## <a name="create-the-custom-resource-definitions"></a>사용자 지정 리소스 정의 만들기

다음 명령을 실행 하 여 사용자 지정 리소스 정의를 만듭니다.  **[Kubernetes 클러스터 관리자 권한 필요]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>데이터 컨트롤러가 생성 되는 네임 스페이스 만들기

다음과 유사한 명령을 실행 하 여 데이터 컨트롤러가 생성 될 새 전용 네임 스페이스를 만듭니다.  이 예제 및이 문서의 나머지 예제에서는 네임 스페이스 이름이 `arc` 사용 됩니다. 다른 이름을 사용 하도록 선택 하는 경우에는 전체에서 같은 이름을 사용 합니다.

```console
kubectl create namespace arc
```

다른 사용자가 클러스터 관리자가 아닌이 네임 스페이스를 사용 하는 경우 네임 스페이스 관리자 역할을 만들고 역할 바인딩을 통해 해당 역할을 해당 사용자에 게 부여 하는 것이 좋습니다.  네임 스페이스 관리자는 네임 스페이스에 대 한 모든 권한을 보유 해야 합니다.  추가 정보는 나중에 사용자에 게 보다 세분화 된 역할 기반 액세스를 제공 하는 방법에 제공 됩니다.

## <a name="create-the-bootstrapper-service"></a>부트스트래퍼 서비스 만들기

부트스트래퍼 서비스는 데이터 컨트롤러, SQL 관리 되는 인스턴스 또는 PostgreSQL Hyperscale 서버 그룹과 같은 사용자 지정 리소스를 만들고, 편집 하 고, 삭제 하기 위해 들어오는 요청을 처리 합니다.

다음 명령을 실행 하 여 부트스트래퍼 서비스, 부트스트래퍼 서비스용 서비스 계정 및 부트스트래퍼 서비스 계정에 대 한 역할 및 역할 바인딩을 만듭니다.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

다음 명령을 사용 하 여 부트스트래퍼 pod가 실행 중인지 확인 합니다.  상태가로 변경 될 때까지 몇 번 실행 해야 할 수 있습니다 `Running` .

```console
kubectl get pod --namespace arc
```

부트스트래퍼. yaml 템플릿 파일은 기본적으로 MCR (Microsoft Container Registry)에서 부트스트래퍼 컨테이너 이미지를 끌어옵니다.  사용자 환경에 Microsoft Container Registry에 대 한 직접 액세스 권한이 없는 경우 다음을 수행할 수 있습니다.
- [Microsoft Container Registry에서 컨테이너 이미지를 끌어와 개인 컨테이너 레지스트리에 푸시하](offline-deployment.md)는 단계를 수행 합니다.
- 개인 컨테이너 레지스트리에 대 한 [이미지 끌어오기 암호를 만듭니다](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) .
- 부트스트래퍼 컨테이너에 이미지 풀 비밀을 추가 합니다. 아래 예제를 참조하세요.
- 부트스트래퍼 이미지에 대 한 이미지 위치를 변경 합니다. 아래 예제를 참조하세요.

아래 예제에서는 `regcred` Kubernetes 설명서에 나와 있는 것 처럼 이미지 풀 비밀 이름을 만들었다고 가정 합니다.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-oct-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>데이터 컨트롤러 관리자에 대 한 암호 만들기

데이터 컨트롤러 관리자 사용자 이름 및 암호는 관리 기능을 수행 하기 위해 데이터 컨트롤러 API에 인증 하는 데 사용 됩니다.  보안 암호를 선택 하 고 클러스터 관리자 권한이 필요한 암호를 사용 하 여 공유 합니다.

Kubernetes 암호는 b a s e 64로 인코딩된 문자열로 저장 되며, 사용자 이름 및 암호에 대해 하나입니다.

온라인 도구를 사용 하 여 원하는 사용자 이름과 암호를 base64로 인코드 하거나, 플랫폼에 따라 기본 제공 CLI 도구를 사용할 수 있습니다.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

사용자 이름 및 암호를 인코딩한 후 [템플릿 파일](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) 을 기반으로 파일을 만들고 사용자 이름 및 암호 값을 고유한 값으로 바꿀 수 있습니다.

그런 후 다음 명령을 실행 하 여 비밀을 만듭니다.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>데이터 컨트롤러 만들기

이제 데이터 컨트롤러 자체를 만들 준비가 되었습니다.

먼저 일부 설정을 수정할 수 있도록 컴퓨터에 로컬로 [템플릿 파일](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) 의 복사본을 만듭니다.

필요에 따라 다음을 편집 합니다.

**필수**
- **위치** : 데이터 컨트롤러에 대 한 _메타 데이터가_ 저장 될 Azure 위치로 변경 합니다.  [데이터 컨트롤러 만들기 개요](create-data-controller.md) 문서에서 사용 가능한 Azure 위치 목록을 볼 수 있습니다.
- **resourceGroup** : Azure Resource Manager에서 데이터 컨트롤러 azure 리소스를 만들려는 azure 리소스 그룹입니다.  일반적으로이 리소스 그룹은 이미 존재 해야 하지만 데이터를 Azure에 업로드할 때 까지는 필요 하지 않습니다.
- **구독** : azure 리소스를 만들려는 구독에 대 한 AZURE 구독 GUID입니다.

**기본값을 검토 하 고 변경 하는 것이 좋습니다.**
- **저장소. className** : 데이터 컨트롤러 데이터 및 로그 파일에 사용할 저장소 클래스입니다.  Kubernetes 클러스터에서 사용 가능한 저장소 클래스를 모를 경우 다음 명령을 실행할 수 있습니다 `kubectl get storageclass` .  기본값은 저장소 클래스가 있는 것으로 `default` 가정 하 고 이름이 인 저장소 `default` 클래스가 기본값 _인_ 것으로 가정 합니다.  참고: 원하는 저장소 클래스에 설정 해야 하는 두 개의 className 설정이 있습니다. 하나는 데이터이 고 하나는 로그입니다.
- **serviceType** : `NodePort` LoadBalancer를 사용 하지 않는 경우 서비스 유형을로 변경 합니다.  참고: 두 개의 serviceType 설정을 변경 해야 합니다.

**필드**
- **이름** : 데이터 컨트롤러의 기본 이름은 이지만 `arc` 원하는 경우 변경할 수 있습니다.
- **displayName** :이 값을 파일 위쪽의 이름 특성과 동일한 값으로 설정 합니다.
- **레지스트리** : Microsoft Container Registry 기본값입니다.  Microsoft Container Registry에서 이미지를 끌어오거나 [개인 컨테이너 레지스트리에 푸시하](offline-deployment.md)는 경우 여기에 레지스트리의 IP 주소 또는 DNS 이름을 입력 합니다.
- **Dockerregistry** : 필요한 경우 개인 컨테이너 레지스트리에서 이미지를 끌어오는 데 사용할 이미지 풀 비밀입니다.
- **리포지토리** : Microsoft Container Registry의 기본 리포지토리는 `arcdata` 입니다.  개인 컨테이너 레지스트리를 사용 하는 경우 Azure Arr을 사용 하는 데이터 서비스 컨테이너 이미지를 포함 하는 폴더/리포지토리의 경로를 입력 합니다.
- **Imagetag** : 현재 최신 버전 태그는 템플릿에서 기본값으로 사용 되지만 이전 버전을 사용 하려는 경우 변경할 수 있습니다.

완료 된 데이터 컨트롤러 yaml 파일의 예:
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-oct-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

편집한 파일을 로컬 컴퓨터에 저장 하 고 다음 명령을 실행 하 여 데이터 컨트롤러를 만듭니다.

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>만들기 상태 모니터링

컨트롤러를 만드는 과정을 완료 하는 데 몇 분 정도 걸립니다. 다음 명령을 사용 하 여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령은 이름이 인 데이터 컨트롤러 및 Kubernetes 네임 스페이스를 만들었다고 가정 합니다 `arc` .  다른 네임 스페이스/데이터 컨트롤러 이름을 사용한 경우를 사용자의 이름으로 바꿀 수 있습니다 `arc` .

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행 하 여 특정 pod의 생성 상태를 확인할 수도 있습니다.  이 기능은 문제를 해결 하는 데 특히 유용 합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Azure Data Studio 용 azure Arc 확장은 Azure Arc 사용 Kubernetes를 설정 하 고 샘플 SQL Managed Instance yaml 파일을 포함 하는 git 리포지토리를 모니터링 하도록 구성 하는 방법을 안내 하는 노트북을 제공 합니다. 모든 항목이 연결 되 면 새 SQL Managed Instance Kubernetes 클러스터에 배포 됩니다.

Azure Data Studio Azure Arc extension에서 **Azure arc Enabled Kubernetes 및 Flux 노트북을 사용 하 여 SQL Managed Instance 배포** 를 참조 하세요.

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

만든 문제가 발생 하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Kubernetes를 사용 하 여 SQL 관리 되는 인스턴스 만들기-기본 도구](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Kubernetes를 사용 하 여 PostgreSQL Hyperscale server 그룹 만들기-기본 도구](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
