---
title: AKS 클러스터에 개발 컴퓨터를 연결 하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces를 사용 하 여 개발 컴퓨터를 Azure Kubernetes Service 클러스터에 연결 하는 프로세스에 대해 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241713"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>AKS 클러스터에 개발 컴퓨터를 연결 하는 방법

Azure Dev Spaces를 사용 하면 개발 컴퓨터를 AKS 클러스터에 연결 하 여 클러스터에서 실행 되는 것 처럼 개발 컴퓨터에서 코드를 실행 하 고 디버그할 수 있습니다. Azure Dev Spaces은 원격 에이전트 역할을 하는 pod를 클러스터에서 실행 하 여 연결 된 AKS 클러스터 간에 트래픽을 리디렉션하여 개발 컴퓨터와 클러스터 간에 트래픽을 리디렉션합니다. 이러한 트래픽 리디렉션을 통해 AKS 클러스터에서 실행 되는 서비스와 개발 컴퓨터의 코드가 동일한 AKS 클러스터에 있는 것 처럼 통신할 수 있습니다. 이 연결을 사용 하 여 개발 컴퓨터에서 컨테이너를 사용 하거나 사용 하지 않고 코드를 실행 및 디버그할 수도 있습니다. 개발 컴퓨터를 클러스터에 연결 하면 신속 하 게 응용 프로그램을 개발 하 고 종단 간 테스트를 수행할 수 있습니다.

## <a name="connecting-to-your-cluster"></a>클러스터에 연결

MacOS 또는 Windows 10에서 실행 되는 [Azure Dev Spaces][azds-vs-code] 확장이 설치 된 [Visual Studio Code][vs-code] 를 사용 하 여 기존 AKS 클러스터에 연결 합니다. 연결을 설정할 때 클러스터의 신규 또는 기존 pod로 들어오고 나가는 모든 트래픽을 개발 컴퓨터로 리디렉션하는 옵션이 있습니다.

> [!NOTE]
> Visual Studio Code를 사용 하 여 클러스터에 연결 하는 경우 Azure Dev Spaces 확장은 서비스를 개발 컴퓨터로 리디렉션하는 옵션을 제공 합니다. 이 옵션은 리디렉션의 pod를 식별 하는 편리한 방법입니다. AKS 클러스터와 개발 컴퓨터 간의 모든 리디렉션은 pod 용입니다.

클러스터에 연결 하는 경우 클러스터에서 Azure Dev Spaces를 사용 하도록 설정 하지 않아도 됩니다. 대신 Azure Dev Spaces 확장에서 클러스터에 대 한 연결을 설정할 때 다음을 수행 합니다.

* AKS 클러스터에서 pod의 컨테이너를 개발 컴퓨터로 트래픽을 리디렉션하는 원격 에이전트 컨테이너로 바꿉니다. 새 pod를 리디렉션하는 경우 Azure Dev Spaces 원격 에이전트를 사용 하 여 AKS 클러스터에 새 pod를 만듭니다.
* 개발 컴퓨터에서 [kubectl 포트][kubectl-port-forward] 를 실행 하 여 개발 컴퓨터에서 클러스터를 실행 하는 원격 에이전트로 트래픽을 전달 합니다.
* 원격 에이전트를 사용 하 여 클러스터에서 환경 정보를 수집 합니다. 이 환경 정보에는 환경 변수, 표시 되는 서비스, 볼륨 탑재 및 비밀 탑재가 포함 됩니다.
* 개발 컴퓨터의 서비스가 클러스터에서 실행 되는 것과 동일한 변수에 액세스할 수 있도록 Visual Studio Code 터미널에서 환경을 설정 합니다.  
* 호스트 파일을 업데이트 하 여 AKS 클러스터의 서비스를 개발 컴퓨터의 로컬 IP 주소에 매핑합니다. 이러한 호스트 파일 항목을 통해 개발 컴퓨터에서 실행 되는 코드를 사용 하 여 클러스터에서 실행 되는 다른 서비스에 요청을 수행할 수 있습니다. 호스트 파일을 업데이트 하기 위해 클러스터에 연결할 때 개발 컴퓨터에 대 한 관리자 액세스를 요청 Azure Dev Spaces 합니다.

클러스터에서 Azure Dev Spaces 사용 하도록 설정 된 경우 [Azure Dev Spaces에서 제공 하는 트래픽 리디렉션을][how-it-works-routing]사용할 수도 있습니다. Azure Dev Spaces에서 제공 하는 트래픽 리디렉션을 통해 자식 개발 공간에서 실행 되는 서비스의 복사본에 연결할 수 있습니다. 자식 개발 공간을 사용 하면 자식 공간의 서비스 인스턴스를 대상으로 하는 트래픽만 리디렉션하여 서비스의 부모 공간 인스턴스를 수정 하지 않고 유지 하므로 부모 dev 공간에서 작업 하는 다른 사용자가 중단 되지 않도록 방지할 수 있습니다.

클러스터에 연결 하면 서비스를 개발 컴퓨터에서 실행 하 고 있는지 여부에 관계 없이 트래픽이 개발 컴퓨터로 라우팅됩니다.

## <a name="running-code-on-your-development-computer"></a>개발 컴퓨터에서 코드 실행

AKS 클러스터에 대 한 연결을 설정한 후에는 컨테이너 화 없이 컴퓨터에서 기본적으로 모든 코드를 실행할 수 있습니다. 원격으로 실행 되는 코드에서 해당 트래픽을 허용 하 고 처리할 수 있도록 원격 에이전트가 수신 하는 모든 네트워크 트래픽이 연결 중에 지정 된 로컬 포트로 리디렉션됩니다. 개발 컴퓨터에서 실행 되는 코드에서 클러스터의 환경 변수, 볼륨 및 암호를 사용할 수 있습니다. 또한 호스트 파일 항목 및 포트 전달이 Azure Dev Spaces 하 여 개발자 컴퓨터에 추가 되기 때문에 사용자 코드는 클러스터의 서비스 이름을 사용 하 여 클러스터에서 실행 되는 서비스로 네트워크 트래픽을 전송 하 고 클러스터에서 실행 중인 서비스에 해당 트래픽을 전달할 수 있습니다.

코드가 개발 컴퓨터에서 실행 되 고 있으므로 개발에 일반적으로 사용 하는 모든 도구를 사용 하 여 코드를 실행 하 고 디버그할 수 있습니다. 사용자가 연결 하는 전체 시간 동안 개발 컴퓨터와 클러스터 간에 트래픽이 라우팅됩니다. 이 영구 연결을 사용 하면 연결을 다시 설정 하지 않고도 필요한 만큼 코드를 시작, 중지 및 다시 시작할 수 있습니다.

또한 Azure Dev Spaces는 *azds-local* 파일을 통해 개발 컴퓨터에서 AKS 클러스터의 pod에 사용할 수 있는 환경 변수 및 탑재 된 파일을 복제 하는 방법을 제공 합니다. 이 파일을 사용 하 여 새 환경 변수 및 볼륨 탑재를 만들 수도 있습니다.

## <a name="additional-configuration-with-azds-localenv"></a>Azds-local를 사용 하 여 추가 구성

*Azds-local* 파일을 사용 하면 AKS 클러스터에서 pod에 사용할 수 있는 환경 변수 및 탑재 된 파일을 복제할 수 있습니다. *Azds-local* 파일에서 다음 작업을 지정할 수 있습니다.

* 볼륨을 다운로드 하 고 해당 볼륨의 경로를 환경 변수로 설정 합니다.
* 볼륨에서 개별 파일 또는 파일 집합을 다운로드 하 고 개발 컴퓨터에 탑재 합니다.
* 연결 된 클러스터에 관계 없이 서비스를 사용할 수 있도록 설정 합니다.

다음은 *azds-local* 파일의 예입니다.

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

기본 *azds-local* 파일은 자동으로 생성 되지 않으므로 프로젝트의 루트에 파일을 수동으로 만들어야 합니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

AKS 클러스터에 연결 되 면 클러스터의 진단 로그가 개발 컴퓨터의 [임시 디렉터리][azds-tmp-dir]에 기록 됩니다. Visual Studio Code 사용 하 여 *진단 정보 표시* 명령을 사용 하 여 AKS 클러스터의 현재 환경 변수 및 DNS 항목을 인쇄할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

AKS 클러스터에 로컬 개발 컴퓨터를 연결 하는 작업을 시작 하려면 [개발 컴퓨터를 AKS 클러스터에 연결][connect]을 참조 하세요.

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
