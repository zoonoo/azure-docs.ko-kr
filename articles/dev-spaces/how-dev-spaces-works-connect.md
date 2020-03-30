---
title: 개발 컴퓨터를 AKS 클러스터에 연결하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 개발 컴퓨터를 Azure Kubernetes 서비스 클러스터에 연결하는 Azure 개발자 공간을 사용하는 프로세스에 대해 설명합니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241713"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>개발 컴퓨터를 AKS 클러스터에 연결하는 방법

Azure 개발자 공간을 사용하면 개발 컴퓨터를 AKS 클러스터에 연결하여 클러스터에서 실행 중인 것처럼 개발 컴퓨터에서 코드를 실행하고 디버깅할 수 있습니다. Azure Dev Spaces는 개발 컴퓨터와 클러스터 간에 트래픽을 리디렉션하는 원격 에이전트 역할을 하는 클러스터에서 포드를 실행하여 연결된 AKS 클러스터 간의 트래픽을 리디렉션합니다. 이 트래픽 리디렉션을 사용하면 AKS 클러스터에서 실행중인 개발 컴퓨터 및 서비스의 코드가 동일한 AKS 클러스터에 있는 것처럼 통신할 수 있습니다. 또한 이 연결을 통해 개발 컴퓨터에서 컨테이너유무에 관계없이 코드를 실행하고 디버깅할 수 있습니다. 개발 컴퓨터를 클러스터에 연결하면 응용 프로그램을 신속하게 개발하고 종단 간 테스트를 수행할 수 있습니다.

## <a name="connecting-to-your-cluster"></a>클러스터에 연결

MacOS 또는 Windows 10에서 실행되는 [Azure 개발자 공간][azds-vs-code] 확장 이 설치된 [Visual Studio 코드를][vs-code] 사용하여 기존 AKS 클러스터에 연결합니다. 연결을 설정하면 클러스터의 새 또는 기존 포드에서 개발 컴퓨터로 모든 트래픽을 리디렉션할 수 있습니다.

> [!NOTE]
> 비주얼 스튜디오 코드를 사용하여 클러스터에 연결하는 경우 Azure 개발자 공간 확장은 개발 컴퓨터로 서비스를 리디렉션할 수 있는 옵션을 제공합니다. 이 옵션은 리디렉션을 위한 창을 식별하는 편리한 방법입니다. AKS 클러스터와 개발 컴퓨터 간의 모든 리디렉션은 포드용입니다.

클러스터에 연결해도 클러스터에서 Azure 개발자 공간을 사용하도록 설정할 필요가 없습니다. 대신 Azure 개발자 공간 확장이 클러스터에 대한 연결을 설정하면 다음과 같은

* AKS 클러스터의 포드에 있는 컨테이너를 트래픽을 개발 컴퓨터로 리디렉션하는 원격 에이전트 컨테이너로 바꿉니다. 새 포드를 리디렉션할 때 Azure 개발자 공간은 원격 에이전트를 사용하여 AKS 클러스터에 새 포드를 만듭니다.
* 개발 컴퓨터에서 [kubectl 포트포워드를][kubectl-port-forward] 실행하여 개발 컴퓨터에서 클러스터에서 실행되는 원격 에이전트로 트래픽을 전달합니다.
* 원격 에이전트를 사용하여 클러스터에서 환경 정보를 수집합니다. 이 환경 정보에는 환경 변수, 가시적 서비스, 볼륨 마운트 및 비밀 마운트가 포함됩니다.
* 개발 컴퓨터의 서비스가 클러스터에서 실행 중인 것과 동일한 변수에 액세스할 수 있도록 Visual Studio Code 터미널에서 환경을 설정합니다.  
* 호스트 파일을 업데이트하여 AKS 클러스터의 서비스를 개발 컴퓨터의 로컬 IP 주소로 매핑합니다. 이러한 호스트 파일 항목을 사용하면 개발 컴퓨터에서 실행되는 코드가 클러스터에서 실행중인 다른 서비스에 요청을 할 수 있습니다. 호스트 파일을 업데이트하기 위해 Azure 개발자 공간은 클러스터에 연결할 때 개발 컴퓨터에서 관리자 액세스를 요청합니다.

클러스터에서 Azure 개발자 공간을 사용하도록 설정한 경우 [Azure 개발자 공간에서 제공하는 트래픽 리디렉션을][how-it-works-routing]사용할 수도 있습니다. Azure 개발자 공간에서 제공하는 트래픽 리디렉션을 사용하면 자식 개발 공간에서 실행되는 서비스의 복사본에 연결할 수 있습니다. 자식 개발 공간을 사용하면 서비스의 자식 공간 인스턴스를 대상으로 하는 트래픽만 리디렉션하여 서비스의 상위 공간 인스턴스를 수정하지 않은 상태로 두므로 부모 개발 공간에서 작업하는 다른 사용자를 방해하지 않도록 할 수 있습니다.

클러스터에 연결하면 개발 컴퓨터에서 서비스가 실행되고 있는지 여부에 관계없이 트래픽이 개발 컴퓨터로 라우팅됩니다.

## <a name="running-code-on-your-development-computer"></a>개발 컴퓨터에서 코드 실행

AKS 클러스터에 대한 연결을 설정한 후에는 컨테이너화 없이 기본적으로 컴퓨터에서 코드를 실행할 수 있습니다. 원격 에이전트가 수신하는 모든 네트워크 트래픽은 연결 중에 지정된 로컬 포트로 리디렉션되므로 기본적으로 실행 중인 코드가 해당 트래픽을 수락하고 처리할 수 있습니다. 클러스터의 환경 변수, 볼륨 및 비밀은 개발 컴퓨터에서 실행되는 코드에 사용할 수 있습니다. 또한 Azure Dev Spaces에서 개발자 컴퓨터에 추가된 호스트 파일 항목 및 포트 포워딩으로 인해 코드는 클러스터의 서비스 이름을 사용하여 클러스터에서 실행되는 서비스로 네트워크 트래픽을 보낼 수 있으며 해당 트래픽은 클러스터에서 실행 중인 서비스입니다.

코드가 개발 컴퓨터에서 실행되고 있으므로 일반적으로 개발에 사용하는 모든 도구를 사용하여 코드를 실행하고 디버깅할 수 있습니다. 트래픽은 연결된 전체 시간 동안 개발 컴퓨터와 클러스터 간에 라우팅됩니다. 이 영구 연결을 사용하면 연결을 다시 설정할 필요 없이 필요한 만큼 코드를 시작, 중지 및 다시 시작할 수 있습니다.

또한 Azure 개발자 공간은 *azds-local.env* 파일을 통해 개발 컴퓨터의 AKS 클러스터의 포드에서 사용할 수 있는 환경 변수 및 탑재된 파일을 복제하는 방법을 제공합니다. 이 파일을 사용하여 새 환경 변수 및 볼륨 마운트를 만들 수도 있습니다.

## <a name="additional-configuration-with-azds-localenv"></a>azds-local.env를 가진 추가 구성

*azds-local.env* 파일을 사용하면 AKS 클러스터의 포드에서 사용할 수 있는 환경 변수 및 탑재파일을 복제할 수 있습니다. *azds-local.env* 파일에서 다음 작업을 지정할 수 있습니다.

* 볼륨을 다운로드하고 해당 볼륨에 대한 경로를 환경 변수로 설정합니다.
* 볼륨에서 개별 파일 또는 파일 집합을 다운로드하여 개발 컴퓨터에 탑재합니다.
* 연결된 클러스터에 관계없이 서비스를 사용할 수 있도록 합니다.

다음은 *azds-local.env* 파일예제입니다.

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

기본 *azds-local.env* 파일이 자동으로 만들어지지 않으므로 프로젝트의 루트에서 수동으로 파일을 만들어야 합니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

AKS 클러스터에 연결하면 클러스터의 진단 로그가 개발 컴퓨터의 [임시 디렉터리에][azds-tmp-dir]기록됩니다. Visual Studio 코드를 사용하여 진단 *정보 표시* 명령을 사용하여 AKS 클러스터에서 현재 환경 변수 및 DNS 항목을 인쇄할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

로컬 개발 컴퓨터를 AKS 클러스터에 연결하기 시작하려면 [개발 컴퓨터를 AKS 클러스터에 연결합니다.][connect]

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
