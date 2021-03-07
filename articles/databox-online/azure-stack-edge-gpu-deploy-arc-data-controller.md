---
title: Azure Stack Edge Pro GPU 장치에 Azure Arc 데이터 컨트롤러 배포 | Microsoft Docs
description: Azure Stack Edge Pro GPU 장치에 Azure Arc Data Controller 및 Azure Data Services를 배포 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 3ff07c773a2976a296d13510a3ddd7b41217aaa2
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437624"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에 Azure Data Services 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Arc 데이터 컨트롤러를 만든 다음 Azure Stack Edge Pro GPU 장치에 Azure Data Services를 배포 하는 과정을 설명 합니다. 

Azure Arc Data Controller는 고객이 관리 하는 환경에서 Azure Data Services를 사용 하도록 설정 하는 로컬 제어 평면입니다. Azure Stack Edge Pro 장치에서 실행 되는 Kubernetes 클러스터에 Azure Arc 데이터 컨트롤러를 만든 후 해당 데이터 컨트롤러에 SQL Managed Instance (미리 보기)와 같은 Azure Data Services을 배포할 수 있습니다.

데이터 컨트롤러를 만든 다음 SQL Managed Instance를 배포 하는 절차는 `kubectl` 장치에서 Kubernetes 클러스터에 대 한 명령줄 액세스를 제공 하는 PowerShell 및-네이티브 도구를 사용 하는 절차를 포함 합니다.


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Edge Pro 장치 Azure Stack에 액세스할 수 있으며, [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명 된 대로 장치를 활성화 했습니다.

1. 장치에서 계산 역할을 사용 하도록 설정 했습니다. [Azure Stack Edge Pro 장치에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 지침에 따라 장치에서 compute를 구성 하는 경우에도 장치에 Kubernetes 클러스터가 만들어집니다.

1. 로컬 웹 UI의 **장치** 페이지에서 Kubernetes API 끝점이 있습니다. 자세한 내용은 [KUBERNETES API 끝점 가져오기](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)의 지침을 참조 하세요.

1. 장치에 연결 하는 클라이언트에 액세스할 수 있습니다. 
    1. 이 문서에서는 PowerShell 5.0 이상을 실행 하는 Windows 클라이언트 시스템을 사용 하 여 장치에 액세스 합니다. [지원 되는 운영 체제가](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)있는 다른 클라이언트를 사용할 수 있습니다. 
    1. `kubectl`클라이언트에를 설치 합니다. 클라이언트 버전:
        1. 장치에 설치 된 Kubernetes 서버 버전을 확인 합니다. 장치의 로컬 UI에서 **소프트웨어 업데이트** 페이지로 이동 합니다. 이 페이지에서 **Kubernetes 서버 버전** 을 확인 합니다.
        1. 마스터에서 부 버전이 두 개 이상 왜곡된 클라이언트를 다운로드합니다. 클라이언트 버전은 마스터를 최대 1 부 버전까지 만들 수 있습니다. 예를 들어, v 1.3 마스터는 v 1.1, v 1.2 및 v 1.3 노드와 함께 사용 해야 하며, v 1.2, v 1.3 및 v 1.4 클라이언트에서 작동 해야 합니다. Kubernetes client 버전에 대 한 자세한 내용은 [Kubernetes 버전 및 버전 기울기 지원 정책](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)을 참조 하세요.
    
1. 필요에 따라 [Azure Arc 사용 데이터 서비스를 배포 하 고 관리 하기 위한 클라이언트 도구를 설치](../azure-arc/data/install-client-tools.md)합니다. 이러한 도구는 필수는 아니지만 권장 됩니다.  
1. 데이터 컨트롤러와 SQL Managed Instance를 프로 비전 할 수 있는 충분 한 리소스가 장치에 있는지 확인 합니다. Data controller와 SQL Managed Instance의 경우 최소 16gb의 RAM과 4 개의 CPU 코어가 필요 합니다. 자세한 지침은 [Azure Arc 사용 데이터 서비스 배포에 대 한 최소 요구 사항](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)을 참조 하세요.


## <a name="configure-kubernetes-external-service-ips"></a>Kubernetes 외부 서비스 Ip 구성

1. 장치의 로컬 웹 UI로 이동한 다음 **Compute** 로 이동 합니다.
1. 계산에 사용 되는 네트워크를 선택 합니다. 

    ![로컬 UI의 컴퓨팅 페이지 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. 다른 외부 서비스 또는 컨테이너에 대해 이미 구성 된 Ip 외에도 세 개의 추가 Kubernetes 외부 서비스 Ip를 제공 해야 합니다. 데이터 컨트롤러는 두 개의 서비스 Ip를 사용 하 고, 세 번째 IP는 SQL Managed Instance를 만들 때 사용 됩니다. 배포 하는 각 추가 데이터 서비스에 대해 하나의 IP가 필요 합니다. 

    ![로컬 UI의 컴퓨팅 페이지 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. 설정을 적용 하면 이러한 새 Ip가 이미 기존 Kubernetes 클러스터에 적용 됩니다. 


## <a name="deploy-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 배포

데이터 컨트롤러를 배포 하려면 먼저 네임 스페이스를 만들어야 합니다.

### <a name="create-namespace"></a>네임스페이스 만들기 

데이터 컨트롤러를 배포할 새 전용 네임 스페이스를 만듭니다. 사용자를 만든 다음 사용자에 게 만든 네임 스페이스에 대 한 액세스 권한을 부여 합니다. 

> [!NOTE]
> 네임 스페이스 및 사용자 이름 둘 다에 대해 [DNS 하위 도메인 명명 규칙이](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) 적용 됩니다.

1. [PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.
1. 네임스페이스 만들기 유형:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. 사용자를 만듭니다. 유형: 

    `New-HcsKubernetesUser -UserName <User name>`

1. 구성 파일은 일반 텍스트로 표시 됩니다. 이 파일을 복사 하 여 *구성* 파일로 저장 합니다. 

    > [!IMPORTANT]
    > 구성 파일을 *.txt* 파일로 저장 하지 말고 파일 확장명 없이 파일을 저장 합니다.

1. 구성 파일은 `.kube` 로컬 컴퓨터에 있는 사용자 프로필의 폴더에 있어야 합니다. 사용자 프로필의 해당 폴더에 파일을 복사 합니다.

    ![클라이언트에 있는 구성 파일의 위치](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)
1. 사용자가 만든 네임 스페이스에 대 한 액세스 권한을 부여 합니다. 유형: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    이전 명령의 샘플 출력은 다음과 같습니다. 이 예제에서는 네임 스페이스를 만들고 사용자에 `myadstest` `myadsuser` 게 네임 스페이스에 대 한 액세스 권한을 부여 합니다.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. 시스템의 호스트 파일에 DNS 항목을 추가 합니다. 

    1. 관리자 권한으로 메모장을 실행 하 고 `hosts` 에 있는 파일을 엽니다 `C:\windows\system32\drivers\etc\hosts` . 
    2. 로컬 UI (필수 구성 요소)의 **장치** 페이지에서 저장 한 정보를 사용 하 여 호스트 파일에 항목을 만듭니다. 

        예를 들어, `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` 장치 IP 주소 및 DNS 도메인을 사용 하 여 다음 항목을 만들려면이 끝점을 복사 합니다. 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Kubernetes pod에 연결할 수 있는지 확인 하려면 cmd 프롬프트 또는 PowerShell 세션을 시작 합니다. 유형:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
이제 네임 스페이스에 데이터 컨트롤러 및 데이터 서비스 응용 프로그램을 배포한 다음 응용 프로그램 및 해당 로그를 볼 수 있습니다.

### <a name="create-data-controller"></a>데이터 컨트롤러 만들기

데이터 컨트롤러는 API, 컨트롤러 서비스, 부트스트래퍼, 모니터링 데이터베이스 및 대시보드를 제공 하기 위해 Kubernetes 클러스터에 배포 되는 pod의 컬렉션입니다. 앞에서 만든 네임 스페이스의 Azure Stack Edge 장치에 있는 데이터 컨트롤러를 Kubernetes 클러스터에 만들려면 다음 단계를 수행 합니다.   

1. 데이터 컨트롤러를 만드는 데 필요한 다음 정보를 수집 합니다.

    
    |열1  |Column2  |
    |---------|---------|
    |데이터 컨트롤러 이름     |데이터 컨트롤러에 대 한 설명이 포함 된 이름입니다. 예들 들어 `arctestdatacontroller`입니다.         |
    |데이터 컨트롤러 사용자 이름     |데이터 컨트롤러 관리자 사용자에 대 한 사용자 이름입니다. 데이터 컨트롤러의 사용자 이름 및 암호는 관리 기능을 수행 하기 위해 데이터 컨트롤러 API에 인증 하는 데 사용 됩니다.          |
    |데이터 컨트롤러 암호     |데이터 컨트롤러 관리자 사용자에 대 한 암호입니다. 보안 암호를 선택 하 고 클러스터 관리자 권한이 필요한 암호를 사용 하 여 공유 합니다.         |
    |Kubernetes 네임 스페이스의 이름     |데이터 컨트롤러를 만들려는 Kubernetes 네임 스페이스의 이름입니다.         |
    |Azure 구독 ID     |Azure의 데이터 컨트롤러 리소스를 만들 Azure 구독 GUID입니다.         |
    |Azure 리소스 그룹 이름     |Azure의 데이터 컨트롤러 리소스를 만들려는 리소스 그룹의 이름입니다.         |
    |Azure 위치     |Azure에 데이터 컨트롤러 리소스 메타 데이터가 저장 되는 Azure 위치입니다. 사용 가능한 지역 목록은 지역별 Azure 글로벌 인프라/제품을 참조 하세요.|


1. PowerShell 인터페이스에 연결하세요. 데이터 컨트롤러를 만들려면 다음을 입력 합니다. 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    이전 명령의 샘플 출력은 다음과 같습니다.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    배포를 완료 하는 데 약 5 분이 걸릴 수 있습니다.

    > [!NOTE]
    > Azure Stack Edge Pro 장치에서 Kubernetes 클러스터에 생성 된 데이터 컨트롤러는 현재 릴리스의 연결 되지 않은 모드 에서만 작동 합니다.

### <a name="monitor-data-creation-status"></a>데이터 생성 상태 모니터링

1. 다른 PowerShell 창을 엽니다.
1. 다음 명령을 사용 `kubectl` 하 여 데이터 컨트롤러의 생성 상태를 모니터링 합니다. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    컨트롤러를 만들 때 상태는 이어야 합니다 `Ready` .
    이전 명령의 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. 데이터 컨트롤러에서 실행 되는 외부 서비스에 할당 된 Ip를 확인 하려면 명령을 사용 `kubectl get svc -n <namespace>` 합니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>SQL 관리 되는 인스턴스 배포

데이터 컨트롤러를 성공적으로 만든 후에는 템플릿을 사용 하 여 데이터 컨트롤러에 SQL Managed Instance를 배포할 수 있습니다.

### <a name="deployment-template"></a>배포 템플릿

다음 배포 템플릿을 사용 하 여 장치의 데이터 컨트롤러에 SQL Managed Instance를 배포 합니다.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>메타데이터 이름
    
메타 데이터 이름은 SQL Managed Instance의 이름입니다. 앞에 있는 관련 pod는 `deployment.yaml` `sqlex-n` ( `n` 응용 프로그램과 연결 된 pod의 수)로 이름이 바뀝니다. 
    
#### <a name="password-and-username-data"></a>암호 및 사용자 이름 데이터

데이터 컨트롤러의 사용자 이름 및 암호는 관리 기능을 수행 하기 위해 데이터 컨트롤러 API에 인증 하는 데 사용 됩니다. 배포 템플릿의 데이터 컨트롤러 사용자 이름 및 암호에 대 한 Kubernetes 암호는 b a s e 64로 인코딩된 문자열입니다. 

온라인 도구를 사용 하 여 원하는 사용자 이름과 암호를 base64로 인코드 하거나, 플랫폼에 따라 기본 제공 CLI 도구를 사용할 수 있습니다. 온라인 Base64 인코딩 도구를 사용 하는 경우 도구에서 사용자 이름 및 암호 문자열 (데이터 컨트롤러를 만드는 동안 입력)을 제공 하면 도구에서 해당 하는 b a s e 64로 인코딩된 문자열을 생성 합니다.
    
#### <a name="service-type"></a>서비스 유형

서비스 유형은로 설정 되어야 합니다 `LoadBalancer` .
    
#### <a name="storage-class-name"></a>저장소 클래스 이름

배포가 데이터, 백업, 데이터 로그 및 로그에 사용할 Azure Stack Edge 장치에서 저장소 클래스를 식별할 수 있습니다. 명령을 사용  `kubectl get storageclass` 하 여 장치에 배포 된 저장소 클래스를 가져옵니다.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
위의 샘플 출력에서 장치의 저장소 클래스를 `ase-node-local` 템플릿에서 지정 해야 합니다.
 
#### <a name="spec"></a>규정

Azure Stack Edge 장치에서 SQL Managed Instance를 만들려면의 사양 섹션에서 메모리 및 CPU 요구 사항을 지정할 수 있습니다 `deployment.yaml` . 각 SQL 관리 되는 인스턴스는 다음 예제에 표시 된 것 처럼 최소 2gb 메모리와 1 개의 CPU 코어를 요청 해야 합니다. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

데이터 컨트롤러 및 1 개의 SQL Managed Instance에 대 한 자세한 크기 조정 지침은 [sql 관리 되는 인스턴스 크기 조정 세부 정보](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)를 검토 하십시오.

### <a name="run-deployment-template"></a>배포 템플릿 실행

`deployment.yaml`다음 명령을 사용 하 여를 실행 합니다.

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

다음 명령의 샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

`sqlex-0`샘플 출력의 pod는 SQL Managed Instance의 상태를 나타냅니다.

## <a name="remove-data-controller"></a>데이터 컨트롤러 제거

데이터 컨트롤러를 제거 하려면 배포한 전용 네임 스페이스를 삭제 합니다.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에 상태 비저장 응용 프로그램을 배포](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)합니다.
