---
title: Azure Container Instances를 Jenkins 빌드 에이전트로 사용
description: Azure Container Instances를 Jenkins 빌드 에이전트로 사용하는 방법을 알아봅니다.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances를 Jenkins 빌드 에이전트로 사용

Azure Container Instances는 컨테이너화된 워크로드를 실행하기 위한 격리된 주문형, 버스터블 환경을 제공합니다. 이러한 특성 때문에 Azure Container Instances는 Jenkins 빌드 작업을 대규모로 실행할 수 있는 우수한 플랫폼을 만들어 줍니다. 이 문서에서는 ACI를 통해 빌드 대상으로 미리 구성된 Jenkins 서버를 배포하고 사용하는 방법을 안내합니다.

Azure Container Instances에 대한 자세한 내용은 [Azure Container Instances 정보][about-aci]를 참조하세요.

## <a name="deploy-jenkins-server"></a>Jenkins 서버 배포

Azure Portal에서 **리소스 만들기**를 선택하고 **Jenkins**를 검색합니다. 게시자가 **Microsoft**인 Jenkins를 선택하고 **만들기**를 선택합니다.

기본 양식에서 다음 정보를 입력하고 모두 마쳤으면 **확인**을 클릭합니다.

- **이름** - Jenkins 배포의 이름입니다.
- **사용자 이름** - 이 사용자 이름은 Jenkins 가상 머신의 관리 사용자로 사용됩니다.
- **인증 유형** - SSH 공개 키를 권장합니다. 선택했으면 Jenkins 가상 머신에 로그인할 때 사용할 SSH 공개 키를 복사합니다.
- **구독** - Azure 구독을 선택합니다.
- **리소스 그룹** - 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.
- **위치** - Jenkins 서버의 위치를 선택합니다.

![Jenkins 포털 배포 기본 설정](./media/container-instances-jenkins/jenkins-portal-01.png)

추가 설정 양식에서 다음 항목을 완료합니다.

- **크기** - Jenkins 가상 머신에 적합한 크기 지정 옵션을 선택합니다.
- **VM 디스크 유형** - Jenkins 서버의 HDD(하드 디스크 드라이브) 또는 SSD(반도체 드라이브)를 지정합니다.
- **가상 네트워크** - (선택 사항) 가상 네트워크를 선택하여 기본 설정을 수정합니다.
- **서브넷** - 서브넷을 선택하고, 정보를 확인한 다음, **확인**을 선택합니다.
- **공용 IP 주소** - 공용 IP 주소를 선택하면 사용자 지정 이름을 지정하고, SKU를 구성하고, 메서드를 할당할 수 있습니다.
- **도메인 이름 레이블** - Jenkins 가상 머신에 대한 정규화된 URL을 만드는 값을 지정합니다.
- **Jenkins 릴리스 종류** - 옵션(LTS, 주간 빌드 또는 Azure 검증 완료)에서 원하는 릴리스 종류를 선택합니다.

![Jenkins 포털 배포 추가 설정](./media/container-instances-jenkins/jenkins-portal-02.png)

[Azure 관리 서비스 ID][managed-service-identity]가 Jenkins 인스턴스에 대한 인증 ID를 자동으로 만들게 하려면 서비스 주체 통합에서 **Auto(MSI)** 를 선택합니다. 사용자 고유의 서비스 주체 자격 증명을 입력하려면 수동을 선택합니다.

클라우드 에이전트는 Jenkins 빌드 작업을 위한 클라우드 기반 플랫폼을 구성합니다. 이 문서에서는 ACI를 선택합니다. ACI 클라우드 에이전트를 사용하면 각 Jenkins 빌드 작업이 한 Azure Container Instances에서 실행됩니다.

![Jenkins 포털 배포 클라우드 통합 설정](./media/container-instances-jenkins/jenkins-portal-03.png)

통합 설정을 마쳤으면 **확인**을 클릭한 다음, 유효성 검사 요약에서 **확인**을 다시 클릭합니다. 사용 약관 요약에서 **만들기**를 클릭합니다. Jenkins 서버가 배포될 때까지 몇 분 정도 걸립니다.

## <a name="configure-jenkins"></a>Jenkins 구성

Azure Portal에서 Jenkins 리소스 그룹을 찾아서 Jenkins 가상 머신을 선택하고, DNS 이름을 기록해 둡니다.

![Jenkins 로그인 지침](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Jenkins VM의 DNS 이름을 찾아서 반환된 SSH 문자열을 복사합니다.

![Jenkins 로그인 지침](./media/container-instances-jenkins/jenkins-portal-04.png)

개발 시스템에서 터미널 세션을 열고 마지막 단계의 SSH 문자열을 붙여넣습니다. 'username'을 Jenkins 서버를 배포할 때 지정한 사용자 이름으로 업데이트합니다.

연결되면 다음 명령을 실행하여 초기 관리자 암호를 검색합니다.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

SSH 세션과 터널이 계속 실행되도록 그대로 두고, 브라우저에서 http://localhost:8080으로 이동합니다. 다음 이미지처럼 초기 관리자 암호를 필드에 붙여넣습니다. 여기까지 마쳤으면 **계속**을 선택합니다.

![Jenkins 잠금 해제](./media/container-instances-jenkins/jenkins-portal-05.png)

**권장 플러그 인 설치**를 선택하여 권장 Jenkins 플러그 인을 모두 설치합니다.

![Jenkins 플러그 인 설치](./media/container-instances-jenkins/jenkins-portal-06.png)

새로운 관리 사용자 계정을 만듭니다. 이 계정은 Jenkins 인스턴스에 로그인하여 작업하는 데 사용됩니다.

![Jenkins 사용자 만들기](./media/container-instances-jenkins/jenkins-portal-07.png)

모두 마쳤으면 **저장 및 끝내기**를 선택한 다음, **Jenkins를 사용하여 시작**을 선택하여 구성을 완료합니다.

이제 Jenkins는 코드를 빌드하고 배포할 준비가 완료되었습니다. 이 예에서는 간단한 Java 응용 프로그램을 사용하여 Azure Container Instances에서 Jenkins 빌드를 시연합니다.

## <a name="create-build-job"></a>빌드 작업 만들기

컨테이너 이미지를 Jenkins 빌드 대상으로 사용할 때 빌드에 필요한 모든 도구를 포함하는 이미지를 지정해야 합니다. 이미지를 지정하려면 **Jenkins 관리** > **시스템 구성**을 선택하고 **클라우드** 섹션이 나올 때까지 아래로 스크롤합니다. 이 예제에서는 Docker 이미지 값을 `microsoft/java-on-azure-jenkins-slave`로 업데이트합니다.

모두 마쳤으면 **저장**을 클릭하여 Jenkins 대시보드로 돌아갑니다.

![Jenkins 클라우드 구성](./media/container-instances-jenkins/jenkins-aci-image.png)

이제 Jenkins 빌드 작업을 만듭니다. **새 항목**을 선택하고, 빌드 프로젝트의 이름을 지정하고(예: `aci-java-demo`), **프리스타일 프로젝트**를 선택하고, **확인**을 클릭합니다.

![Jenkins 작업 만들기](./media/container-instances-jenkins/jenkins-new-job.png)

**일반**에서 **이 프로젝트를 실행할 수 있는 위치 제한**을 선택합니다. 레이블 식에 `linux`를 입력합니다. 이렇게 구성하면 이 빌드 작업이 ACI 클라우드에서 실행됩니다.

![Jenkins 작업 만들기](./media/container-instances-jenkins/jenkins-job-01.png)

소스 코드 관리에서 `Git`를 선택하고 리포지토리 URL에 `https://github.com/spring-projects/spring-petclinic.git`를 입력합니다. 이 GitHub 리포지토리는 응용 프로그램 예제 코드를 포함하고 있습니다.

![Jenkins 작업에 소스 코드 추가](./media/container-instances-jenkins/jenkins-job-02.png)

빌드 아래에서 **빌드 단계 추가**를 선택하고 `Invoke top-level Maven targets`을 선택합니다. 빌드 단계 목표로 `package`를 입력합니다.

![Jenkins 빌드 단계 추가](./media/container-instances-jenkins/jenkins-job-03.png)

완료되면 **저장** 을 선택합니다.

## <a name="run-the-build-job"></a>빌드 작업 실행

빌드 작업을 테스트하고 빌드 플랫폼으로 Azure Container Instances를 관찰하려면 수동으로 빌드를 시작합니다.

**지금 빌드**를 선택하여 빌드 작업을 시작합니다. 작업이 시작될 때까지 몇 분 정도 걸리며, 작업이 실행되면 다음 이미지와 비슷한 상태가 표시됩니다.

![Jenkins 빌드 단계 추가](./media/container-instances-jenkins/jenkins-job-status.png)

작업이 실행되는 동안 Azure Portal을 열고 Jenkins 리소스 그룹을 살펴봅니다. Azure Container Instance가 만들어진 것을 볼 수 있습니다. Jenkins 작업이 실행되고 있는 이 인스턴스의 내부에 있습니다.

![ACI에서 Jenkins 빌드](./media/container-instances-jenkins/jenkins-aci.png)

Jenkins가 구성된 Jenkins 실행기 수(기본값 2)보다 많은 작업을 실행하면 여러 Azure Container Instances가 만들어집니다.

![ACI에서 Jenkins 빌드](./media/container-instances-jenkins/jenkins-aci-multi.png)

모든 빌드 작업이 완료되면 Azure container instances가 제거됩니다.

![ACI에서 Jenkins 빌드](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>다음 단계

Azure의 Jenkins에 대한 자세한 내용은 [Azure 및 Jenkins][jenkins-azure]를 참조하세요.

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md