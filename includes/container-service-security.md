---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 39bb75a6f834789f91cb590ffebb72f45624eb25
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148867"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(사용되지 않음) Azure Container Service에서 Docker 컨테이너 보호

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

이 문서에서는 Azure Container Service에 배포된 Docker 컨테이너 보호를 위한 고려 사항과 권장 사항을 소개합니다. 여기에 나와 있는 고려 사항은 대부분 Azure 또는 다른 환경에 배포된 Docker 컨테이너에 일반적으로 적용됩니다. 

## <a name="image-security"></a>이미지 보안

컨테이너는 하나 이상의 리포지토리에 저장된 이미지에서 작성됩니다. 이러한 리포지토리는 공용 또는 프라이빗 컨테이너 레지스트리에 속할 수 있습니다. 공용 레지스트리의 예는 [Docker 허브](https://hub.docker.com/)입니다. 프라이빗 레지스트리의 예는 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/)이며 온-프레미스 또는 가상 프라이빗 클라우드에 설치할 수 있습니다. [Azure Container Registry](../articles/container-registry/container-registry-intro.md)를 포함한 클라우드 기반 개인 컨테이너 레지스트리 서비스도 있습니다.

### <a name="public-and-private-images"></a>공용 및 프라이빗 이미지
일반적으로, 공개적으로 게시된 소프트웨어 패키지와 마찬가지로 공개적으로 사용 가능한 컨테이너 이미지는 보안을 보장하지 않습니다. 컨테이너 이미지는 여러 소프트웨어 계층으로 구성되며 각 소프트웨어 계층은 취약점이 있을 수 있습니다. 이미지의 소유자(신뢰할 수 있는 출처인지 여부를 결정하기 위해), 구성되는 소프트웨어 계층 및 소프트웨어 버전을 포함하여 컨테이너 이미지의 출처를 이해하는 것이 중요합니다. 

예를 들어 Docker 허브에서 공식적인 [nginx 리포지토리](https://hub.docker.com/_/nginx/)로 이동하고 **태그** 탭을 탐색하면 각 이미지에 색으로 지정된 취약성이 표시됩니다. 각 색상은 이미지의 소프트웨어 계층에서 취약성을 나타냅니다. Docker 허브에서 취약성 검색에 대한 자세한 내용은 [Docker 허브에서 공식적 리포지토리 이해](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/)를 참조하세요.

![Docker 허브에서 Nginx 이미지](./media/container-service-security/docker-hub-nginx.png)

기업은 Azure Container Registry 또는 Docker Trusted Registry와 같은 프라이빗 레지스트리에서 이미지를 저장 및 검색하는 보안 공격으로부터 스스로를 보호하기 위해 보안을 깊이 고려합니다. 관리되는 프라이빗 레지스트리를 제공하는 것 외에도, Azure Container Registry는 읽기 전용, 쓰기 및 소유자 권한에 대한 역할 기반 액세스를 포함하여 기본 인증 흐름에 대해 Azure Active Directory를 통한 [서비스 주체 기반 인증](../articles/container-registry/container-registry-authentication.md)을 지원합니다.

### <a name="image-security-scanning"></a>이미지 보안 검색

프라이빗 레지스트리를 사용하는 경우에도 추가 보안 유효성 검사를 위해 이미지 검색 솔루션을 사용하는 것이 좋습니다. 컨테이너 이미지의 각 소프트웨어 계층은 컨테이너 이미지의 다른 계층과 관계없이 잠재적으로 취약성이 있습니다. 점점 더 많은 기업들이 컨테이너 기술을 기반으로 프로덕션 워크로드 배포를 시작함에 따라 조직에 대한 보안 위협을 방지하기 위해 이미지 검색이 더욱 중요해지고 있습니다. 

[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) 및 [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) 등과 같은 보안 모니터링 및 검색 솔루션을 사용하여 개인 레지스트리에서 컨테이너 이미지를 검색하여 잠재적인 취약성을 식별할 수 있습니다. 다양한 솔루션이 제공하는 검색 깊이를 파악하는 것이 중요합니다. 예를 들어, 일부 솔루션은 알려진 취약성에 대해서만 이미지 계층을 교차 검증할 수 있습니다. 이러한 솔루션은 특정 패키지 관리자 소프트웨어를 통해 빌드된 이미지 계층 소프트웨어는 검증하지 못할 수 있습니다. 다른 솔루션은 보다 심도 있는 검색 통합 기능을 포함하며 모든 패키지된 소프트웨어에서 취약성을 발견할 수 있습니다.

### <a name="production-deployment-rules-and-audit"></a>프로덕션 배포 규칙 및 감사
애플리케이션을 프로덕션에 배포한 후에는 프로덕션 환경에 사용된 이미지가 안전하고 취약성이 없는지 확인하기 위해 몇 가지 규칙을 설정해야 합니다.

* 일반적으로 부분적으로라도 취약한 이미지는 프로덕션 환경에서 실행해서는 안됩니다. 또한 프로덕션에 배포된 모든 이미지는 이상적으로 소수의 사용자만 액세스할 수 있는 프라이빗 레지스트리에 저장해야 합니다. 또한 효과적으로 관리할 수 있도록 프로덕션 이미지 수를 적게 유지하는 것도 중요합니다.

* 공개적으로 사용 가능한 컨테이너 이미지에서 소프트웨어의 출처를 찾아내는 것은 어렵기 때문에 원본에서 이미지를 작성하여 계층의 출처를 파악하는 것이 좋습니다. 자체 구축된 컨테이너 이미지에 취약성이 있을 경우 고객은 보다 신속하게 해결 방법을 찾을 수 있습니다. 공용 이미지가 있는 고객은 공용 이미지의 루트를 찾아 수정하거나 게시자로부터 다른 보안 이미지를 얻어야 합니다.

* 프로덕션에 배포된 철저하게 검색된 이미지는 애플리케이션의 수명 동안 최신 상태가 보장되지 않습니다. 보안 취약성은 이전에 알려지지 않았거나 프로덕션 배포 후에 도입 된 이미지 계층에 대해 보고될 수 있습니다. 최신 상태가 아니거나 잠시 동안 업데이트되지 않은 이미지를 식별하려면 프로덕션 환경에 배포된 이미지를 주기적으로 감사해야 합니다. 청록색 배포 방법론과 롤링 업그레이드 메커니즘을 사용하여 가동 중지 시간 없이 컨테이너 이미지를 업데이트할 수 있습니다. 이미지 검색은 이전 섹션에서 설명한 도구를 사용하여 수행할 수 있습니다. 

* 이미지를 빌드하는 CI(연속 통합) 파이프라인 및 통합 보안 검색을 통해 보안 컨테이너 이미지로 안전한 프라이빗 레지스트리를 유지 관리할 수 있습니다. CI 솔루션에 기본 제공된 취약성 검색은 모든 테스트를 통과한 이미지가 프로덕션 워크로드가 배포된 프라이빗 레지스트리에 푸시되는지 확인합니다. CI 파이프라인 오류는 취약한 이미지가 프로덕션 워크로드 배포에 사용되는 프라이빗 레지스트리에 푸시되지 않도록 합니다. 또한 이미지 수가 많은 경우에는 이미지 보안 검색을 자동화합니다. 그렇지 않고 이미지에서 보안 취약성을 수동으로 감사하는 것은 매우 번거롭고 오류가 발생하기 쉽습니다.

## <a name="host-level-container-isolation"></a>호스트 수준 컨테이너 격리
고객이 Azure 리소스에 컨테이너 애플리케이션을 배포하면 리소스 그룹의 구독 수준에서 배포되고 다중 테넌트가 아닙니다. 즉, 고객이 다른 사용자와 구독을 공유하는 경우 같은 구독의 두 배포간에 빌드할 수 있는 경계가 없습니다. 따라서 컨테이너 수준 보안은 보장되지 않습니다. 

또한 컨테이너가 커널과 호스트의 리소스(Azure Container Service에서 클러스터의 Azure VM임)를 공유한다는 것을 이해하는 것이 중요합니다. 따라서 프로덕션에서 실행 중인 컨테이너는 권한 없는 사용자 모드로 실행되어야 합니다. 루트 권한으로 컨테이너를 실행하면 전체 환경이 손상될 수 있습니다. 해커는 컨테이너에서 루트 수준 액세스 권한을 사용하여 호스트에서 전체 루트 권한에 대한 권한을 얻을 수 있습니다. 또한 읽기 전용 파일 시스템으로 컨테이너를 실행하는 것이 중요합니다. 이렇게 하면 손상된 컨테이너에 액세스할 수 있는 누군가가 파일 시스템에 악성 스크립트를 작성하고 다른 파일에 액세스하지 못하도록 할 수 있습니다. 마찬가지로 컨테이너에 할당된 리소스(예: 메모리, CPU 및 네트워크 대역폭)를 제한하는 것이 중요합니다. 이렇게 하면 해커가 리소스를 낭비하거나 신용 카드 부정 행위 또는 비트코인 마이닝과 같은 불법 활동을 시도하여 다른 컨테이너가 호스트나 클러스터에서 실행되는 것을 방지할 수 있습니다.

## <a name="orchestrator-considerations"></a>조정자 고려 사항

Azure Container Service에 제공되는 각 조정자는 고유한 보안 고려 사항을 포함합니다. 예를 들어 Container Service에서 조정자 노드에 대한 직접 SSH 액세스를 제한해야 합니다. 대신, 각 조정자의 UI나 명령줄 도구(예: Kubernetes의 경우 `kubectl`)를 사용하여 호스트에 액세스하지 않고도 컨테이너 환경을 관리해야 합니다. 자세한 내용은 [Kubernetes, DC/OS 또는 Docker Swarm 클러스터에 원격 연결](../articles/container-service/kubernetes/container-service-connect.md)을 참조하세요.

추가 조정자 관련 보안 정보는 다음 리소스를 참조하세요.

* **Kubernetes**: [Kubernetes 배포에 대한 보안 모범 사례](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [클러스터 보호](http://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**: [Docker 보안](https://www.docker.com/docker-security)

## <a name="next-steps"></a>다음 단계

* Docker 아키텍처 및 컨테이너 보안에 대한 자세한 내용은 [컨테이너 보안 소개](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf)를 참조하세요.

* Azure 플랫폼 보안에 대한 자세한 내용은 [Azure Security Center](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure)를 참조하세요.
