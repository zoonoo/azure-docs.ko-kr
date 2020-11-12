---
title: Kubernetes에 Azure SQL Edge 컨테이너 배포 - Azure SQL Edge
description: Kubernetes에 Azure SQL Edge 컨테이너 배포에 대한 자세한 정보
keywords: SQL Edge, 컨테이너, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395294"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Kubernetes에 Azure SQL Edge 컨테이너 배포

Azure SQL Edge는 Kubernetes에서 실행되는 Azure IoT Edge를 통해 IoT Edge 모듈 또는 독립 실행형 컨테이너 Pod로 Kubernetes 클러스터에 배포할 수 있습니다. 이 문서의 나머지 부분에서는 kubernetes 클러스터에 독립 실행형 컨테이너를 배포하는 내용에 집중합니다. Kubernetes에 Azure IoT Edge를 배포하는 내용에 대한 자세한 정보는 [Kubernetes의 Azure IoT Edge(미리 보기)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html)를 참조하세요.

이 자습서에서는 kubernetes 클러스터의 컨테이너에서 고가용성 Azure SQL Edge 인스턴스를 구성하는 방법을 보여줍니다. 

> [!div class="checklist"]
> * SA 암호 만들기
> * 스토리지 만들기
> * 배포 만들기
> * SSMS(SQL Server Management Studio)를 사용하여 연결
> * 실패 및 복구 확인

Kubernetes 1.6 이상에서는 [스토리지 클래스](https://kubernetes.io/docs/concepts/storage/storage-classes/), [영구적 볼륨 클레임](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims) 및 [Azure 디스크 볼륨 유형](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk)을 지원합니다. Kubernetes에서 기본적으로 Azure SQL Edge 인스턴스를 만들고 관리할 수 있습니다. 이 문서의 예제에서는 [배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)를 만들어 공유 디스크 장애 조치(failover) 클러스터 인스턴스와 비슷한 고가용성 구성을 얻는 방법을 보여 줍니다. 이 구성에서 Kubernetes는 클러스터 오케스트레이터의 역할을 수행합니다. 컨테이너의 Azure SQL Edge 인스턴스가 실패하면 오케스트레이터는 동일한 영구적 스토리지에 연결된 또 다른 컨테이너 인스턴스를 부트스트랩합니다.

![Kubernetes 클러스터의 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge.png)

위 다이어그램에서 `azure-sql-edge`는 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)의 컨테이너입니다. Kubernetes는 클러스터의 리소스를 오케스트레이션합니다. [복제본 세트](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)는 노드 실패 후에 Pod가 자동으로 복구되도록 합니다. 애플리케이션이 서비스에 연결됩니다. 이 경우 서비스는 `azure-sql-edge` 실패 후에도 동일하게 유지되는 IP 주소를 호스트하는 부하 분산 장치를 나타냅니다.

다음 다이어그램에서는 `azure-sql-edge` 컨테이너에서 오류가 발생했습니다. 오케스트레이터인 Kubernetes는 복제본 세트에 올바른 개수의 정상적인 인스턴스가 있도록 보장하고 구성에 따라 새 컨테이너를 시작합니다. 오케스트레이터가 동일한 노드에서 새 Pod를 시작하고, `azure-sql-edge`가 동일한 영구적 스토리지에 다시 연결됩니다. 서비스가 다시 생성된 `azure-sql-edge`에 연결됩니다.

![Pod 실패 후 Kubernetes 클러스터의 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

다음 다이어그램에서는 `azure-sql-edge` 컨테이너를 호스트하는 노드에서 오류가 발생했습니다. 오케스트레이터가 다른 노드에서 새 Pod를 시작하고, `azure-sql-edge`가 동일한 영구적 스토리지에 다시 연결됩니다. 서비스가 다시 생성된 `azure-sql-edge`에 연결됩니다.

![노드 실패 후 Kubernetes 클러스터의 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>사전 요구 사항

* **Kubernetes 클러스터**
   - 이 자습서를 완료하려면 Kubernetes 클러스터가 필요합니다. 이 단계에서는 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)을 사용하여 클러스터를 관리합니다. 

   - 이 자습서에서는 목적을 달성하기 위해 Azure Kubernetes Service를 사용하여 Azure SQL Edge를 배포합니다. `kubectl`을 사용하여 AKS에서 단일 노드 Kubernetes 클러스터를 만들고 연결하려면 [AKS(Azure Kubernetes Service) 클러스터 배포](../aks/tutorial-kubernetes-deploy-cluster.md)를 참조하세요. 

   >[!NOTE]
   >노드 오류로부터 보호하려면 Kubernetes 클러스터에 두 개 이상의 노드가 필요합니다.

* **Azure CLI**
   - 이 자습서의 지침은 Azure CLI 2.10.1에서 유효성이 검사되었습니다.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>SQL Edge 배포에 사용할 kubernetes 네임스페이스 만들기

Kubernetes 클러스터에 새 네임스페이스를 만듭니다. 이 네임스페이스는 SQL Edge 및 모든 필수 아티팩트를 배포하는 데 사용됩니다. Kubernetes 네임스페이스에 대한 자세한 내용은 [네임스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)를 참조하세요.

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>SA 암호 만들기

Kubernetes 클러스터에서 SA 암호를 만듭니다. Kubernetes는 [비밀](https://kubernetes.io/docs/concepts/configuration/secret/) 등의 암호와 같은 중요한 구성 정보를 관리할 수 있습니다.

다음 명령은 SA 계정의 암호를 만듭니다.

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   `MyC0m9l&xP@ssw0rd`를 복잡한 암호로 바꿉니다.

## <a name="create-storage"></a>스토리지 만들기

Kubernetes 클러스터에서 [영구적 볼륨](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) 및 [영구적 볼륨 클레임](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection)을 구성합니다. 다음 단계를 완료합니다. 

1. 스토리지 클래스 및 영구적 볼륨 클레임을 정의하는 매니페스트를 만듭니다.  매니페스트는 스토리지 프로비저닝 프로그램, 매개 변수 및 [회수 정책](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming)을 지정합니다. Kubernetes 클러스터는 이 매니페스트를 사용하여 영구적 스토리지를 만듭니다. 

   다음 yaml 예제에서는 스토리지 클래스 및 영구적 볼륨 클레임을 정의합니다. 이 Kubernetes 클러스터가 Azure에 있기 때문에 스토리지 클래스 프로비저닝 프로그램은 `azure-disk`입니다. 스토리지 계정 유형은 `Standard_LRS`입니다. 영구적 볼륨 클레임의 이름은 `mssql-data`입니다. 영구적 볼륨 클레임 메타데이터에는 스토리지 클래스에 다시 연결하는 주석이 포함되어 있습니다. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   파일(예: **pvc.yaml** )을 저장합니다.

2. Kubernetes에서 영구적 볼륨 클레임을 만듭니다.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>`은 파일을 저장한 위치입니다.

   영구적 볼륨은 Azure Storage 계정으로 자동으로 생성되고 영구적 볼륨 클레임에 바인딩됩니다. 

    ![영구적 볼륨 클레임 명령 스크린샷](media/deploy-kubernetes/pvc-cmd.png)

3. 영구적 볼륨 클레임을 확인합니다.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>`은 영구적 볼륨 클레임의 이름입니다.

   이전 단계에서 영구적 볼륨 클레임의 이름은 `mssql-data`입니다. 영구적 볼륨 클레임에 대한 메타데이터를 확인하려면 다음 명령을 실행합니다.

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   반환된 메타데이터에는 `Volume`이라는 값이 있습니다. 이 값은 Blob의 이름에 매핑됩니다.

   ![Volume을 포함하여 반환된 메타데이터 스크린샷](media/deploy-kubernetes/describe-volume.png)

4. 영구적 볼륨을 확인합니다.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl`이 자동으로 생성되어 영구적 볼륨 클레임에 바인딩된 영구적 볼륨에 대한 메타데이터를 반환합니다. 

## <a name="create-the-deployment"></a>배포 만들기

이 예제에서는 Azure SQL Edge 인스턴스를 호스트하는 컨테이너를 Kubernetes 배포 개체라고 설명합니다. 배포는 복제본 세트를 만듭니다. 복제본 세트는 Pod를 만듭니다. 

이 단계에서는 Azure SQL Edge Docker 이미지를 기반으로 컨테이너를 설명하는 매니페스트를 만듭니다. 매니페스트는 `mssql-data` 영구적 볼륨 클레임 및 Kubernetes 클러스터에 이미 적용된 `mssql` 비밀을 참조합니다. 또한 매니페스트는 [서비스](https://kubernetes.io/docs/concepts/services-networking/service/)를 설명합니다. 이 서비스는 부하 분산 장치입니다. 부하 분산 장치는 Azure SQL Edge 인스턴스가 복구된 후에도 IP 주소가 유지되도록 보장합니다. 

1. 배포를 설명하는 매니페스트(YAML 파일)를 만듭니다. 다음 예제에서는 Azure SQL Edge 컨테이너 이미지를 기반으로 컨테이너를 포함하여 배포에 대해 설명합니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   앞의 코드를 `sqldeployment.yaml`이라는 새 파일에 복사합니다. 다음 값을 업데이트합니다. 

   * MSSQL_PID `value: "Developer"`: Azure SQL Edge Developer Edition을 실행할 컨테이너를 설정합니다. Developer Edition은 프로덕션 데이터에 사용할 수 없습니다. 프로덕션용으로 배포하는 경우 버전을 `Premium`으로 설정합니다. 

      >[!NOTE]
      >자세한 내용은 [Azure SQL Edge 라이선스 획득 방법](https://azure.microsoft.com/pricing/details/sql-edge/)을 참조하세요.

   * `persistentVolumeClaim`: 이 값에는 영구적 볼륨 클레임의 이름에 매핑되는 `claimName:` 항목이 필요합니다. 이 자습서에서는 `mssql-data`를 사용합니다. 

   * `name: SA_PASSWORD`: 이 섹션에 정의된 대로 SA 암호를 설정하도록 컨테이너 이미지를 구성합니다.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Kubernetes는 컨테이너를 배포할 때 암호의 값을 얻기 위해 `mssql`이라는 비밀을 참조합니다. 

   >[!NOTE]
   >`LoadBalancer` 서비스 유형을 사용하면 1433 포트에서 인터넷을 통해 원격으로 Azure SQL Edge 인스턴스에 액세스할 수 있습니다.

   파일(예: **sqledgedeploy.yaml** )을 저장합니다.

2. 배포를 만듭니다.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>`은 파일을 저장한 위치입니다.

   ![배포 명령 스크린샷](media/deploy-kubernetes/deploy-sql-cmd.png)

   배포 및 서비스가 만들어집니다. Azure SQL Edge 인스턴스는 영구적 스토리지에 연결된 컨테이너에 있습니다.

   Pod의 상태를 보려면 `kubectl get pod -n <namespace name>`를 입력합니다.

   ![Pod 가져오기 명령 스크린샷](media/deploy-kubernetes/get-sql-pod-cmd.png)

   앞의 그림에서는 Pod가 `Running` 상태입니다. 이 상태는 컨테이너가 준비되었음을 나타냅니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

   >[!NOTE]
   >배포를 만든 후에 Pod가 표시될 때까지 몇 분 정도 걸릴 수 있습니다. 이 지연은 클러스터가 Docker 허브에서 Azure SQL Edge 컨테이너 이미지를 끌어오기 때문에 발생합니다. 이미지를 처음 끌어온 후, 해당 이미지가 이미 캐시되어 있는 노드에 배포하는 경우 후속 배포는 더 빨라질 수 있습니다. 

3. 서비스가 실행되고 있는지 확인합니다. 다음 명령 실행:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   이 명령은 서비스의 내부 및 외부 IP 주소뿐만 아니라 실행 중인 서비스도 반환합니다. `mssql-deployment` 서비스의 외부 IP 주소를 적어 둡니다. 이 IP 주소를 사용하여 Azure SQL Edge에 연결합니다. 

   ![get service 명령 스크린샷](media/deploy-kubernetes/get-service-cmd.png)

   Kubernetes 클러스터의 개체 상태에 대한 자세한 내용을 보려면 다음 명령을 실행합니다.

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Azure SQL Edge 인스턴스에 연결

설명에 따라 컨테이너를 구성한 경우 Azure 가상 네트워크 외부에서 애플리케이션에 연결할 수 있습니다. `sa` 계정과 서비스의 외부 IP 주소를 사용합니다. Kubernetes 비밀로 구성한 암호를 사용합니다. Azure SQL Edge 인스턴스에 연결하는 방법에 대한 자세한 내용은 [Azure SQL Edge에 연결](connect.md)을 참조하세요.

## <a name="verify-failure-and-recovery"></a>실패 및 복구 확인

실패 및 복구를 확인하기 위해 Pod를 삭제할 수 있습니다. 다음 단계를 수행합니다.

1. Azure SQL Edge를 실행하는 Pod를 나열합니다.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Azure SQL Edge를 실행하는 Pod의 이름을 적어 둡니다.

2. Pod를 삭제합니다.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl`은 Pod 이름으로 이전 단계에서 반환된 값입니다. 

Kubernetes는 Pod를 자동으로 다시 만들어 Azure SQL Edge 인스턴스를 복구하고 영구적 스토리지에 연결합니다. `kubectl get pods`를 사용하여 새 Pod가 배포되었는지 확인합니다. `kubectl get services`를 사용하여 새 컨테이너의 IP 주소가 동일한지 확인합니다. 

## <a name="summary"></a>요약

이 자습서에서는 고가용성을 위해 Kubernetes 클러스터에 Azure SQL Edge 컨테이너를 배포하는 방법을 알아보았습니다. 

> [!div class="checklist"]
> * SA 암호 만들기
> * 스토리지 만들기
> * 배포 만들기
> * Azure SQL Edge Management Studios(SSMS)를 사용하여 연결
> * 실패 및 복구 확인

## <a name="next-steps"></a>다음 단계

- [Kubernetes 소개](../aks/intro-kubernetes.md)
- [SQL Edge에 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)