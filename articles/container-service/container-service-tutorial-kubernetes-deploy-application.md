---
title: "Azure Container Service 자습서 - 응용 프로그램 배포 | Microsoft Docs"
description: "Azure Container Service 자습서 - 응용 프로그램 배포"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 94e1eacd6864a7fcaf753a11e855f6e69aee0f98
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017

---

# <a name="run-applications-in-kubernetes"></a>Kubernetes에서 응용 프로그램 실행

이 자습서에서는 응용 프로그램 예제를 Kubernetes 클러스터에 배포합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Kubernetes 개체 소개
> * Kubernetes 매니페스트 파일 다운로드
> * Kubernetes에서 응용 프로그램 실행
> * 응용 프로그램 테스트

이후 자습서에서 이 응용 프로그램은 규모 확장되고 업데이트되며 Kubernetes 클러스터에서 모니터링됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

최소한 이 자습서에는 Kubernetes 클러스터가 필요합니다.

## <a name="kubernetes-objects"></a>Kubernetes 개체

Kubernetes에 컨테이너화된 응용 프로그램을 배포할 때 다양한 Kubernetes 개체가 만들어집니다. 각 개체는 클러스터에 대한 원하는 상태를 나타냅니다. 예를 들어 간단한 응용 프로그램은 밀접하게 관련된 컨테이너의 그룹화인 Pod, 네트워크로 연결된 저장소인 영구적 볼륨 및 응용 프로그램의 상태를 관리하는 배포로 구성될 수 있습니다. 

모든 Kubernetes 개체에 대한 자세한 내용은 kubernetes.io의 [Kubernetes 개념](https://kubernetes.io/docs/concepts/)을 참조하세요.

## <a name="get-manifest-files"></a>매니페스트 파일 가져오기

이 자습서에서는 Kubernetes 매니페스트를 사용하여 Kubernetes 개체를 배포합니다. Kubernetes 매니페스트는 개체 구성 지침이 포함된 YAML 파일입니다.

이 자습서의 각 개체에 대한 매니페스트 파일은 이전 자습서에서 복제된 Azure Vote 응용 프로그램 리포지토리에서 제공됩니다. 아직 이 리포지토리를 복제하지 않은 경우 다음 명령을 사용하여 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

매니페스트 파일은 복제된 리포지토리의 다음 디렉터리에 있습니다.

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>응용 프로그램 실행

### <a name="storage-objects"></a>저장소 개체

Azure 투표 응용 프로그램은 MySQL 데이터베이스를 포함하므로 데이터베이스 파일을 Pod 간에 공유할 수 있는 볼륨에 저장하려고 할 것입니다. 이 구성에서는 MySQL Pod가 다시 만들어질 때 데이터베이스 파일은 그대로 유지됩니다.

`storage-resources.yaml` 매니페스트 파일은 영구적 볼륨이 만들어지는 방법 및 위치를 정의하는 [저장소 클래스 개체](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses)를 만듭니다. Kubernetes에 여러 볼륨 플러그 인을 사용할 수 있습니다. 이 경우에는 [Azure 디스크](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk) 플러그 인이 사용됩니다. 

[영구적 볼륨 클레임](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)도 만들어지는데, 이는 저장소를 구성하고(저장소 클래스 사용) Pod에 할당합니다.

다음을 실행하여 저장소 개체를 만듭니다.

```bash
kubectl create -f storage-resources.yaml
```

완료되면 가상 디스크가 만들어지고 결과 Kubernetes Pod에 연결됩니다. 가상 디스크는 자동으로 Kubernetes 클러스터와 같은 리소스 그룹에 있는 저장소 계정에 만들어지고 저장소 클래스 개체(Standard_LRS)와 같게 구성됩니다.

### <a name="secure-sensitive-values"></a>중요한 값 보안 유지

[Kubernetes 비밀](https://kubernetes.io/docs/concepts/configuration/secret/)은 중요 정보를 위한 보안 저장소를 제공합니다. Azure 투표 데이터베이스 자격 증명은 `pod-secrets.yaml` 파일을 사용하여 비밀에 저장됩니다. 

다음을 실행하여 비밀 개체를 만듭니다.

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>배포 만들기

[Kubernetes 배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)는 Kubernetes Pod의 상태를 관리합니다. 이 관리에는 원하는 복제본 수가 실행되고 있는지, 볼륨이 탑재되었는지, 그리고 적절한 컨테이너 이미지가 사용되고 있는지를 확인하는 등의 작업이 포함됩니다.

`azure-vote-deployment.yaml` 매니페스트 파일은 Azure 투표 응용 프로그램의 프런트 엔드 및 백 엔드 부분에 대한 배포를 만듭니다.

#### <a name="update-image-names"></a>이미지 이름 업데이트

Azure Container Registry를 사용하여 이미지를 저장하는 경우 이미지 이름 앞에 ACR 로그인 서버 이름을 추가해야 합니다.

[az acr list](/cli/azure/acr#list) 명령을 사용하여 ACR 로그인 서버 이름을 가져옵니다.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

`azure-vote-deployment.yaml` 파일의 *azure-vote-front* 및 *azure-vote-back* 컨테이너 이미지 이름을 업데이트합니다.

프런트 엔드 이미지 이름 예:

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

백 엔드 이미지 이름 예:

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>배포 개체 만들기

[kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create)를 실행하여 Azure 투표 응용 프로그램을 시작합니다.

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>응용 프로그램 공개

[Kubernetes 서비스](https://kubernetes.io/docs/concepts/services-networking/service/)는 Pod 액세스 방법을 정의합니다. Azure 투표 앱에서 백 엔드 배포는 내부적으로 배포 이름으로 액세스할 수 있어야 합니다. 프런트 엔드 배포는 인터넷을 통해 액세스할 수 있어야 합니다. Azure 투표 앱 서비스 구성은 `services.yaml` 매니페스트 파일에서 정의됩니다.

다음을 실행하여 서비스를 만듭니다.

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>응용 프로그램 테스트

모든 개체를 만들고 나면 azure-vote-front 서비스의 외부 IP 주소를 통해 응용 프로그램에 액세스할 수 있습니다. 이 서비스를 만드는 데 몇 분 정도 걸릴 수 있습니다. 서비스 만들기 프로세스를 모니터링하려면 다음 명령을 실행합니다. *azure-vote-front* 서비스의 *EXTERNAL-IP* 값이 *pending*에서 IP 주소로 전환되면 응용 프로그램이 준비되었으며 외부 IP 주소에서 액세스할 수 있습니다.

```bash
kubectl get service -w
```

출력:

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

서비스가 준비되면 `CTRL-C`를 실행하여 kubectl 조사식을 종료합니다.

반환된 외부 IP 주소로 이동하여 응용 프로그램을 확인합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 투표 응용 프로그램을 Azure Container Service Kubernetes 클러스터에 배포했습니다. 완료된 작업은 다음과 같습니다.  

> [!div class="checklist"]
> * Kubernetes 개체 소개
> * Kubernetes 매니페스트 파일 다운로드
> * Kubernetes에서 응용 프로그램 실행
> * 응용 프로그램 테스트

다음 자습서로 이동하여 Kubernetes 응용 프로그램과 기본 Kubernetes 인프라를 모두 크기 조정하는 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [Kubernetes 응용 프로그램 및 인프라 크기 조정](./container-service-tutorial-kubernetes-scale.md)
