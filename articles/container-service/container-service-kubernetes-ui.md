---
title: "Azure Container Service에서 Kubernetes 웹 UI 사용 | Microsoft Docs"
description: "Azure Container Service에서 Kubernetes 웹 UI 사용"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: what-goes-here?
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: ba0848c14344f137a7032a0098d2c1e07e387960
ms.openlocfilehash: 6ede8e11361c9df2a83c71d97cf47e2306d25371


---

# <a name="microsoft-azure-container-service-engine---using-the-kubernetes-web-ui"></a>Microsoft Azure Container Service 엔진 - Kubernetes 웹 UI 사용

## <a name="prerequisites"></a>필수 조건
이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.

또한 Azure CLI 및 kubectl 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](https://github.com/azure/azure-cli#installation)의 지침을 따르세요.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>개요

### <a name="connect-to-the-web-ui"></a>웹 UI에 연결
다음을 실행하여 Kubernetes 웹 UI를 시작할 수 있습니다.

```console
$ az acs kubernetes browse
```

보안 프록시에 로컬 시스템을 Kubernetes 웹 UI에 연결할 것을 지정하도록 구성된 웹 브라우저가 열립니다.

### <a name="create-and-expose-a-service"></a>서비스 만들기 및 공개
Kubernetes 웹 UI의 오른쪽 위에 있는 창에 '만들기' 단추를 표시됩니다.

![Kubernetes 만들기 UI](media/k8s/create.png)

응용 프로그램 만들기를 시작할 수 있는 대화 상자가 열립니다.
이름 `hello-nginx`를 지정합니다. Docker의 [`nginx` 컨테이너](https://hub.docker.com/_/nginx/)를 사용하고 이 웹 서비스의 3개 복제본을 배포합니다.

![Kubernetes 포드 만들기 대화 상자](media/k8s/nginx.png)

계속해서 '외부' Kubernetes 서비스를 만들어 트래픽 부하를 3개 복제본으로 분산합니다.  '외부'를 선택하고 포트 80을 입력합니다.

![Kubernetes 서비스 만들기 대화 상자](media/k8s/service.png)

마지막으로 '배포' 단추를 눌러 이러한 컨테이너 및 서비스를 배포합니다.

![Kubernetes 배포](media/k8s/deploy.png)

### <a name="view-your-containers"></a>컨테이너 보기
'배포'를 누르면 UI에 배포 시의 서비스 보기가 표시됩니다.

![Kubernetes 상태](media/k8s/status.png)

UI의 왼쪽에서 원으로 나타낸 각 Kubernetes 개체의 상태를 확인할 수 있습니다. 원이 부분적으로만 채워져 있으면 개체가 아직 배포 중인 것입니다. 개체가 완전히 배포되면 녹색 확인 표시가 표시됩니다.

![Kubernetes가 배포됨](media/k8s/deployed.png)

모든 기능이 실행 중이면 포드 중 하나를 클릭하여 실행 중인 웹 서비스에 대 한 자세한 내용을 확인할 수 있습니다.

![Kubernetes 포드](media/k8s/pods.png)

포드 관련 보기에서는 포드의 컨테이너에 대한 정보와 해당 컨테이너에서 사용되는 CPU 및 메모리 리소스를 확인할 수 있습니다.

![Kubernetes 리소스](media/k8s/resources.png)

리소스가 보이지 않으면 모니터링 데이터가 전파될 때까지 몇 분 정도 기다려야 할 수 있습니다.

'로그' 링크를 클릭하여 컨테이너에 대한 로그를 확인할 수도 있습니다.

![kubernetes 로그](media/k8s/logs.png)

### <a name="viewing-your-service"></a>서비스 보기
컨테이너를 실행하는 것 외에도, Kubernetes UI는 부하 분산 장치를 프로비전하여 클러스터에 컨테이너로 트래픽을 가져오는 외부 `Service`를 만들었습니다.

왼쪽 탐색 창에서 '서비스'를 클릭하여 모든 서비스를 볼 수 있습니다(현재는 하나만 있음).

![Kubernetes 서비스](media/k8s/service-deployed.png)

해당 보기에서 서비스에 할당된 외부 IP 주소를 볼 수 있습니다.
해당 IP 주소를 클릭하면 부하 분산 장치 뒤에서 nginx 컨테이너가 실행되는 것이 표시됩니다.

![nginx 보기](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>서비스 크기 조정
UI에서 개체를 보는 것 외에, Kubernetes API 개체를 편집하고 업데이트할 수도 있습니다.

먼저 왼쪽 탐색 창에서 '배포'를 클릭하여 서비스에 대한 `Deployment`로 이동합니다.

해당 보기에서 ReplicaSet을 클릭하고 위쪽 탐색 모음에서 '편집' 단추를 클릭합니다.

![kubernetes 편집](media/k8s/edit.png)

`spec.replicas` 필드를 '2'로 편집하고 '업데이트'를 누릅니다.

이렇게 하면 포드 중 하나가 삭제되어 삭제할 복제본 수가 2가 됩니다.

 




<!--HONumber=Dec16_HO3-->


