---
title: "Jenkins를 사용한 Azure Service Fabric Linux Java 응용 프로그램 연속 빌드 및 통합 | Microsoft Docs"
description: "Jenkins를 사용한 Linux Java 응용 프로그램 연속 빌드 및 통합"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Jenkins를 사용한 Linux Java 응용 프로그램 빌드 및 배포
Jenkins는 연속 통합 및 배포를 위한 인기 있는 도구입니다. 이 문서에서는 Jenkins를 사용하여 Service Fabric 응용 프로그램을 빌드 및 배포하는 것에 대해 살펴 봅니다.

## <a name="general-prerequisites"></a>일반 필수 조건
1. 로컬로 설치된 Git가 있어야 합니다. [여기](https://git-scm.com/downloads)에서 사용자의 OS에 기반한 적절한 Git 버전을 설치할 수 있습니다. Git를 처음 접하는 경우에 [설명서](https://git-scm.com/docs)에 나온 단계에 따라 Git를 경험해 볼 수 있습니다.
2. 유용한 Service Fabric Jenkins 플러그 인이 있어야 합니다. Service Fabric Jenkins 플러그 인을 [여기](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)에서 다운로드합니다.

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Service Fabric 클러스터 내부 Jenkins 설정

### <a name="prerequisites"></a>필수 조건
1. Service Fabric Linux 클러스터를 준비합니다. Azure Portal에서 만든 Service Fabric 클러스터에는 Docker가 이미 설치되어 있습니다. 로컬로 클러스터를 실행하는 경우 명령 ``docker info``를 사용하여 Docker가 설치되어 있는지 여부를 확인하고, 설치되어 있지 않다면 다음 명령을 사용하여 적절히 설치합니다.

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. 다음 단계를 사용하여 Service Fabric 컨테이너 응용 프로그램을 클러스터에 배포합니다.

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
그러면 클러스터에 Jenkins 컨테이너가 설치되고 Service Fabric Explorer를 사용하여 모니터링할 수 있습니다.

### <a name="steps"></a>단계
1. 브라우저에서 URL ``http://PublicIPorFQDN:8081``로 이동합니다. 로그인하는 데 필요한 초기 관리자 암호의 경로가 제공됩니다. 초기 관리자 계정으로 로그인한 후에 Jenkins를 관리자 사용자로 계속 사용하거나 사용자를 만들고 변경할 수 있습니다.

  > [!NOTE]
  > 클러스터를 만드는 동안 8081 포트를 응용 프로그램 끝점 포트로 지정해야 합니다.
  >

2. ``docker ps -a``를 사용하여 컨테이너 인스턴스 ID를 가져옵니다.
3. 컨테이너에 SSH 로그인을 수행하고 Jenkins 포털에서 표시된 경로를 붙여넣습니다. 예를 들어 포털에서 경로 `PATH_TO_INITIAL_ADMIN_PASSWORD`가 표시된 경우 다음을 수행할 수 있습니다.

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. [링크](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)에 나와 있는 단계를 사용하여 Jenkins로 작업하도록 GitHub를 설정합니다.
    * GitHub에서 제공되는 지침을 사용하여 SSH 키를 생성하고 SSH 키를 리포지토리를 호스팅하는 GitHub 계정에 추가합니다.
    * Jenkins Docker 셸에서(그리고 호스트에 없는) 이전 링크에 언급된 명령을 실행합니다.
    * 호스트에서 Jenkins 셸에 로그인하려면 다음 명령을 사용합니다.

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Service Fabric 클러스터 외부 Jenkins 설정

### <a name="prerequisites"></a>필수 조건
Docker를 설치해야 합니다. 다음 명령을 사용하여 터미널에서 Docker를 설치할 수 있습니다.

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

이렇게 하면 터미널에서 ``docker info``를 실행할 때 출력에서 Docker 서비스가 실행되는 것을 확인할 수 있습니다.

### <a name="steps"></a>단계
  1. Service Fabric Jenkins 컨테이너 이미지를 끌어옵니다. ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. 다음 컨테이너 이미지를 실행합니다. ``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. 컨테이너 이미지 인스턴스 ID를 가져옵니다. 명령 ``docker ps –a``를 사용하여 모든 Docker 컨테이너를 나열할 수 있습니다.
  4. 다음 단계에 따라 Jenkins 포털에 로그인합니다.

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    컨테이너 ID가 2d24a73b5964이면 2d24를 사용합니다.
    * 이 암호는 ``http://<HOST-IP>:8080``인 포털에서 Jenkins 대시보드에 로그인하는 데 필요합니다.
    * 처음으로 로그인을 하면 사용자 고유의 사용자 계정을 만들어 향후 목적을 위해 사용하거나 관리자 계정을 계속 사용할 수 있습니다. 사용자를 만들면 해당 계정으로 계속 사용해야 합니다.
  5. [링크](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)에 나와 있는 단계를 사용하여 Jenkins로 작업하도록 GitHub를 설정합니다.
      * GitHub에서 제공되는 지침을 사용하여 SSH 키를 생성하고 SSH 키를 리포지토리를 호스팅하는 GitHub-계정에 추가합니다.
      * Jenkins Docker 셸에서(그리고 호스트에 없는) 이전 링크에 언급된 명령을 실행합니다.
      * 호스트에서 Jenkins 셸에 로그인하려면 다음 명령을 사용합니다.

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Jenkins 컨테이너 이미지가 호스트되는 클러스터/컴퓨터에 Jenkins 인스턴스에서 수신하는 GitHub의 알림과 같은 공용 주소 IP가 있는지 확인합니다.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>포털에서 Service Fabric Jenkins 플러그 인 설치

1. ``http://PublicIPorFQDN:8081``으로 이동합니다.
2. Jenkins 대시보드에서 **Jenkins 관리** -> **플러그 인 관리** -> **고급**을 선택합니다.
여기에서는 플러그 인을 업로드할 수 있습니다. **파일 선택** 옵션을 선택한 다음 필수 구성 요소에서 다운로드 한 serviceFabric.hpi 파일을 선택합니다. 업로드를 선택하면 Jenkins에서 플러그 인을 자동으로 설치합니다. 요청된 경우 다시 시작을 허용합니다.

## <a name="creating-and-configuring-a-jenkins-job"></a>Jenkins 작업 만들기 및 구성

1. 대시보드에서 **새 항목**을 만듭니다.
2. 예를 들어 **MyJob**이라는 새 항목 이름을 입력하고 자유 스타일 프로젝트를 선택한 다음 확인을 클릭합니다.
3. 그런 다음 작업 페이지로 이동하여 **구성** -을 클릭합니다.
  * 일반 섹션의 **Github 프로젝트** 아래에서 Jenkins CI/CD 흐름과 통합하려는 Service Fabric Java 응용 프로그램을 호스트하는 GitHub 프로젝트 URL을 지정합니다(예: ``https://github.com/sayantancs/SFJenkins``).
  * **소스 코드 관리** 섹션 아래에서 **Git**를 선택합니다. Jenkins CI/CD 흐름과 통합하려는 Service Fabric Java 응용 프로그램을 호스트하는 리포지토리 URL을 지정합니다(예: ``https://github.com/sayantancs/SFJenkins.git``). 또한 여기서 빌드할 분기를 지정할 수 있습니다(예: ***/master**).
4. Jenkins와 통신할 수 있도록 다음 단계를 수행하여 *GitHub*(리포지토리를 호스팅하는)를 구성합니다.
  1. GitHub 리포지토리 페이지로 이동합니다. **설정** -> **통합 및 서비스**로 이동합니다.
  2. **서비스 추가**를 선택하고 Jenkins를 입력하고 **Jenkins-Github 플러그 인**을 선택합니다.
  3. Jenkins Webhook URL을 입력합니다(기본적으로 ``http://<PublicIPorFQDN>:8081/github-webhook/``이여야 함). 추가/업데이트 서비스를 클릭합니다.
  4. 테스트 이벤트가 사용자의 Jenkins 인스턴스로 전송됩니다. GitHub의 Webhook에서 녹색 확인 표시가 나타나고 프로젝트가 빌드됩니다!
  5. **Build Triggers**(트리거 빌드) 섹션 아래에서 원하는 빌드 옵션을 선택합니다. 이를 위해 리포지토리에 푸시가 발생할 때마다 빌드를 트리거하도록 계획한 사례를 사용합니다. 따라서 해당하는 옵션은 **GitHub hook trigger for GITScm polling**(GITScm 폴링을 위한 GitHub 후크 트리거)(이전에는 '변경 내용이 GitHub에 푸시될 때 빌드'였음)이 됩니다.
  6. **빌드 섹션** 아래 **빌드 단계 추가** 드롭다운에서 **Invoke Gradle Script**(Gradle 스크립트 호출) 옵션을 선택합니다. 제공된 위젯에서 사용자의 응용 프로그램에 대한 경로를 **루트 빌드 스크립트**로 지정합니다. 그러면 지정된 경로에서 build.gradle을 골라내고 적절하게 작동합니다. 이름이 ``MyActor``인 프로젝트를 만든 경우(Eclipse 플러그 인 또는 Yeoman 생성기를 사용) 루트 빌드 스크립트에 ``${WORKSPACE}/MyActor``를 포함해야 합니다. 그 예로 이 섹션은 주로 다음과 같습니다.

    ![Service Fabric Jenkins 빌드 작업][build-step]
  7. **Post-Build Actions**(빌드 후 작업) 드롭다운에서 **Service Fabric 프로젝트 배포**를 선택합니다. 여기에서 Jenkins가 배포할 Service Fabric 응용 프로그램을 컴파일한 클러스터 세부 정보 및 응용 프로그램을 배포하는 데 사용되는 추가 응용 프로그램 세부 정보를 제공해야 합니다. 다음 스크린샷을 참조로 사용할 수 있습니다.

    ![Service Fabric Jenkins 빌드 작업][post-build-step]

 > [!NOTE]
 > 이 클러스터는 Service Fabric을 사용하여 Jenkins 컨테이너 이미지를 배포하는 경우에 Jenkins 컨테이너 응용 프로그램을 호스팅하는 것과 동일할 수 있습니다.
 >

### <a name="end-to-end-scenario"></a>종단 간 시나리오
이제 사용자의 GitHub 및 Jenkins가 구성되었으며, 리포지토리(예: https://github.com/sayantancs/SFJenkins)의 ``MyActor`` 프로젝트에 일부 샘플 변경 사항을 만들고, 원격 ``master`` 분기(또는 함께 작동하도록 구성한 분기)로 변경 사항을 푸시할 수 있습니다. 사용자가 구성한 Jenkins 작업 ``MyJob``이 트리거됩니다. 수행될 것은 기본적으로 GitHub에서 변경 내용을 가져오고, 해당 내용을 빌드하고, 빌드 후 작업에서 지정한 클러스터 끝점으로 응용 프로그램을 배포하는 것입니다.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

