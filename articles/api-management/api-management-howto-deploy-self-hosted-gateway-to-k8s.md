---
title: Kubernetes에 자체 호스팅 Azure API 관리 게이트웨이 배포 | 마이크로 소프트 문서
description: Kubernetes에 자체 호스팅 Azure API 관리 게이트웨이를 배포하는 방법 알아보기
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513836"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Kubernetes에 자체 호스팅 Azure API 관리 게이트웨이 배포

이 문서에서는 자체 호스팅 Azure API 관리 게이트웨이를 Kubernetes 클러스터에 배포하는 단계를 제공합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능이 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 추가 비용 없이 개발자 및 프리미엄 계층에서만 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한됩니다.


## <a name="prerequisites"></a>사전 요구 사항

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- Kubernetes 클러스터를 만듭니다. [미니쿠베는](https://kubernetes.io/docs/tasks/tools/install-minikube/) 개발 및 평가 목적으로 좋은 옵션입니다. 프로덕션 워크로드의 경우 [Azure Kubernetes 서비스](https://azure.microsoft.com/services/kubernetes-service/) 또는 외래 클라우드에서 Kubernetes 클러스터를 사용할 수 있습니다.
- [API 관리 인스턴스에서 게이트웨이 리소스프로비전.](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-gateway-to-kubernetes"></a>Kubernetes에 게이트웨이 배포

1. **설정**에서 **게이트웨이를 선택합니다.**
2. 배포할 게이트웨이 리소스를 선택합니다.
3. **배포를**선택합니다.
4. **토큰** 텍스트 상자의 새 토큰은 기본 **만료** 및 **비밀 키** 값을 사용하여 자동으로 생성되었습니다. 원하는 경우 둘 중 하나 또는 둘 다 조정하고 **생성을** 선택하여 새 토큰을 만듭니다.
5. 배포 스크립트에서 **Kubernetes가** 선택되어 있는지 **확인합니다.**
6. **배포** 옆에 **<게이트웨이 이름>.yml** 파일 링크를 선택하여 파일을 다운로드합니다.
7. 필요에 따라 yml 파일의 포트 매핑 및 컨테이너 이름을 조정합니다.
8. 텍스트 **배포** 상자의 오른쪽 끝에 있는 **복사** 아이콘을 `kubectl` 선택하여 명령을 클립보드에 저장합니다. 
9. 명령을 터미널(또는 명령) 창에 붙여넣습니다. 명령은 다운로드한 환경 파일이 현재 디렉터리에 있을 것으로 예상합니다.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. 명령을 실행합니다. 이 명령은 Microsoft 컨테이너 레지스트리에서 다운로드한 자체 호스팅 게이트웨이 이미지를 사용하여 Kubernetes 클러스터에 컨테이너를 실행하고 HTTP(8080) 및 HTTPS(443) 포트를 노출하도록 컨테이너를 구성하도록 지시합니다.
11. 아래 명령을 실행하여 게이트웨이 포드가 실행 되고 있는지 확인합니다. 포드 이름은 다를 수 있습니다. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 아래 명령을 실행하여 게이트웨이 서비스가 실행 되고 있는지 확인합니다. 서비스 이름은 다를 수 있습니다. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Azure 포털로 돌아가방금 배포한 게이트웨이 노드가 정상 상태를 보고하고 있는지 확인합니다.

![게이트웨이 상태](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> 명령을 <code>kubectl logs <gateway-pod-name></code> 사용하여 자체 호스팅 게이트웨이 로그의 스냅숏을 봅니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [Azure API Management 자체 호스팅 게이트웨이 개요를](self-hosted-gateway-overview.md) 참조하십시오.
* [Azure Kubernetes 서비스에](https://docs.microsoft.com/azure/aks/intro-kubernetes) 대해 자세히 알아보기


