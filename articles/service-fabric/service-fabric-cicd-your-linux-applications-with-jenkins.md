---
title: "Jenkins를 사용한 Azure Service Fabric Linux 응용 프로그램 연속 빌드 및 통합 | Microsoft Docs"
description: "Jenkins를 사용한 Service Fabric Linux 응용 프로그램 연속 빌드 및 통합"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: saysa
ms.openlocfilehash: 89b356c3959b7cb63a746805d60535e07f0d6898
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins를 사용하여 Linux 응용 프로그램 빌드 및 배포
Jenkins는 앱의 연속 통합 및 배포를 위한 인기 있는 도구입니다. Jenkins를 사용하여 Azure Service Fabric 응용 프로그램을 빌드하고 배포하는 방법은 다음과 같습니다.

## <a name="general-prerequisites"></a>일반 필수 조건
- 로컬로 설치된 Git가 있어야 합니다. 운영 체제에 따라 [Git 다운로드 페이지](https://git-scm.com/downloads)에서 적절한 Git 버전을 설치할 수 있습니다. Git을 처음 접하는 경우 [Git 설명서](https://git-scm.com/docs)에서 자세히 알아봅니다.
- 유용한 Service Fabric Jenkins 플러그 인이 있어야 합니다. [Service Fabric 다운로드](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)에서 다운로드할 수 있습니다. Edge 브라우저를 사용하는 경우 .zip에서 .hpi으로 다운로드한 파일의 확장명을 바꿉니다.

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Service Fabric 클러스터 내에서 Jenkins 설정

Service Fabric 클러스터 내부 또는 외부에서 Jenkins를 설정할 수 있습니다. 다음 섹션에서는 Azure Storage 계정을 사용하여 컨테이너 인스턴스 상태를 저장하면서 클러스터 내에서 이를 설정하는 방법을 보여 줍니다.

### <a name="prerequisites"></a>필수 조건
1. Service Fabric Linux 클러스터를 준비합니다. Azure Portal에서 만든 Service Fabric 클러스터에는 Docker가 이미 설치되어 있습니다. 클러스터를 로컬로 실행하는 경우 ``docker info`` 명령을 사용하여 Docker가 설치되어 있는지 확인합니다. 설치되어 있지 않으면 다음 명령을 사용하여 적절하게 설치합니다.

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > 8081 포트가 클러스터의 사용자 지정 끝점으로 지정되었는지 확인합니다.
   >

2. 다음 단계를 사용하여 응용 프로그램을 복제합니다.
  ```sh
  git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
  cd service-fabric-java-getting-started/Services/JenkinsDocker/
  ```

3. 다음과 같이 파일 공유에서 Jenkins 컨테이너의 상태를 유지합니다.
  * 클러스터와 **동일한 지역**에서 Azure 저장소 계정을 ``sfjenkinsstorage1``과 같은 이름으로 만듭니다.
  * ``sfjenkins`` 같은 이름의 저장소 계정에서 **파일 공유**를 만듭니다.
  * 파일 공유에 대한 **연결**을 클릭하고 **Linux에서 연결** 아래에 표시된 값을 적어둡니다. 이 값은 다음과 비슷합니다.
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > cifs 공유를 마운트하려면 클러스터 노드에 cifs-utils 패키지를 설치해야 합니다.       
  >

4. ```setupentrypoint.sh``` 스크립트의 자리 표시자 값을 3단계의 Azure 저장소 세부 정보로 업데이트합니다.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * ``[REMOTE_FILE_SHARE_LOCATION]``을 위 3단계의 연결 출력 값인 ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins``로 바꿉니다.
  * ``[FILE_SHARE_CONNECT_OPTIONS_STRING]``을 위 3단계의 ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` 값으로 바꿉니다.

5. **보안 클러스터에만 해당:** Jenkins에서 보안 클러스터에 응용 프로그램 배포를 구성하려면 Jenkins 컨테이너 내에서 인증서에 액세스할 수 있어야 합니다. Linux 클러스터의 경우 인증서(PEM)가 X509StoreName으로 지정된 저장소에서 컨테이너로 단순히 복사됩니다. ContainerHostPolicies 아래의 ApplicationManifest에서 이 인증서 참조를 추가하고 지문 값을 업데이트합니다. 지문 값은 노드에 위치한 인증서의 값이어야 합니다.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > 지문 값은 보안 클러스터에 연결하는 데 사용되는 인증서와 일치해야 합니다. 
  >

6. 클러스터에 연결하고 컨테이너 응용 프로그램을 설치합니다.

  **보안 클러스터**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **안전하지 않은 클러스터**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  그러면 클러스터에 Jenkins 컨테이너를 설치하고 Service Fabric Explorer를 사용하여 모니터링할 수 있습니다.

    > [!NOTE]
    > 클러스터에 Jenkins 이미지를 다운로드하는 데 몇 분이 걸릴 수 있습니다.
    >

### <a name="steps"></a>단계
1. 브라우저에서 ``http://PublicIPorFQDN:8081``으로 이동합니다. 로그인하는 데 필요한 초기 관리자 암호의 경로가 제공됩니다. 
2. Jenkins 컨테이너가 실행 중인 노드를 확인하려면 Service Fabric Explorer를 살펴봅니다. 이 노드에 SSH(Secure Shell)로 로그인합니다.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. ``docker ps -a``를 사용하여 컨테이너 인스턴스 ID를 가져옵니다.
4. 컨테이너에 SSH(Secure Shell) 로그인을 수행하고 Jenkins 포털에서 표시된 경로를 붙여넣습니다. 예를 들어 포털에서 경로 `PATH_TO_INITIAL_ADMIN_PASSWORD`가 표시된 경우 다음을 실행합니다.

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. [Jenkins 시작] 페이지에서 [설치할 플러그 인 선택] 옵션을 선택하고, **없음** 확인란을 선택하고, [설치]를 클릭합니다.
6. 사용자를 만들거나 관리자로 계속하려면 선택합니다.

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Service Fabric 클러스터 외부에서 Jenkins 설정

Service Fabric 클러스터 내부 또는 외부에서 Jenkins를 설정할 수 있습니다. 다음 섹션에서는 클러스터 외부에서 설정하는 방법을 보여 줍니다.

### <a name="prerequisites"></a>필수 조건
Docker를 설치해야 합니다. 다음 명령을 사용하여 터미널에서 Docker를 설치할 수 있습니다.

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

이렇게 하면 터미널에서 ``docker info``를 실행할 때 출력에서 Docker 서비스가 실행되는 것을 확인할 수 있습니다.

### <a name="steps"></a>단계
  1. Service Fabric Jenkins 컨테이너 이미지를 끌어옵니다. ``docker pull rapatchi/jenkins:v9``
  2. 다음 컨테이너 이미지를 실행합니다. ``docker run -itd -p 8080:8080 rapatchi/jenkins:v9``
  3. 컨테이너 이미지 인스턴스 ID를 가져옵니다. 명령 ``docker ps –a``를 사용하여 모든 Docker 컨테이너를 나열할 수 있습니다.
  4. 다음 단계에 따라 Jenkins 포털에 로그인합니다.

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    컨테이너 ID가 2d24a73b5964이면 2d24를 사용합니다.
    * 이 암호는 ``http://<HOST-IP>:8080``인 포털에서 Jenkins 대시보드에 로그인하는 데 필요합니다.
    * 처음으로 로그인한 후에 사용자 고유의 사용자 계정을 만들어 향후 목적을 위해 사용하거나 관리자 계정을 계속 사용할 수 있습니다. 사용자를 만들면 해당 계정으로 계속 사용해야 합니다.
  5. [새 SSH 키 생성 및 SSH 에이전트에 추가](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)에서 설명한 단계를 사용하여 Jenkins를 사용하도록 GitHub을 설정합니다.
        * GitHub에서 제공되는 지침을 사용하여 SSH 키를 생성하고 SSH 키를 리포지토리를 호스팅하는 GitHub 계정에 추가합니다.
        * (호스트가 아닌) Jenkins Docker 셸에서 이전 링크에 언급된 명령을 실행합니다.
      * 호스트에서 Jenkins 셸에 로그인하려면 다음 명령을 사용합니다.

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Jenkins 컨테이너 이미지가 호스팅되는 클러스터 또는 컴퓨터에 공용 연결 IP가 있는지 확인합니다. 그러면 Jenkins 인스턴스가 GitHub에서 알림을 받을 수 있습니다.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>포털에서 Service Fabric Jenkins 플러그 인 설치

1. ``http://PublicIPorFQDN:8081``으로 이동합니다.
2. Jenkins 대시보드에서 **Jenkins 관리** > **플러그 인 관리** > **고급**을 선택합니다.
여기에서는 플러그 인을 업로드할 수 있습니다. **파일 선택**을 선택한 다음, 필수 구성 요소에서 다운로드했거나 [여기서](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi) 다운로드할 수 있는 **serviceFabric.hpi** 파일을 선택합니다. **업로드**를 선택하면 Jenkins에서 플러그 인을 자동으로 설치합니다. 요청된 경우 다시 시작을 허용합니다.

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins 작업 만들기 및 구성

1. 대시보드에서 **새 항목**을 만듭니다.
2. 항목 이름을 입력합니다(예: **MyJob**). **자유로운 프로젝트**를 선택하고 **확인**을 클릭합니다.
3. 작업 페이지로 이동하여 **구성**을 클릭합니다.

   a. 일반 섹션에서 **GitHub 프로젝트**에 대한 확인란을 선택하고 GitHub 프로젝트 URL을 지정합니다. 이 URL은 Jenkins CI/CD(연속 통합, 연속 배포) 흐름과 통합하려는 Service Fabric Java 응용 프로그램을 호스트합니다(예: ``https://github.com/sayantancs/SFJenkins``).

   b. **소스 코드 관리** 섹션 아래에서 **Git**를 선택합니다. Jenkins CI/CD 흐름과 통합하려는 Service Fabric Java 응용 프로그램을 호스트하는 리포지토리 URL을 지정합니다(예: ``https://github.com/sayantancs/SFJenkins.git``). 또한 여기에서 빌드할 분기를 지정할 수 있습니다(예: **/master**).
4. Jenkins와 통신할 수 있도록 리포지토리를 호스팅하는 *GitHub*을 구성합니다. 다음 단계를 사용하세요.

   a. GitHub 리포지토리 페이지로 이동합니다. **설정** > **통합 및 서비스**로 이동합니다.

   b. **서비스 추가**를 선택하고 **Jenkins**를 입력하고 **Jenkins-GitHub 플러그 인**을 선택합니다.

   c. Jenkins Webhook URL을 입력합니다(기본적으로 ``http://<PublicIPorFQDN>:8081/github-webhook/``이여야 함). **서비스 추가/업데이트**를 클릭합니다.

   d. 테스트 이벤트가 사용자의 Jenkins 인스턴스로 전송됩니다. GitHub의 웹후크에서 녹색 확인 표시가 나타나고 프로젝트가 빌드됩니다.

   e. **트리거 빌드** 섹션 아래에서 원하는 빌드 옵션을 선택합니다. 이 예에서는 리포지토리에 일부 푸시가 발생할 때마다 빌드를 트리거하려고 합니다. 따라서 **GITScm 폴링에 대한 GitHub 후크 트리거**를 선택합니다. (이전에는 이 옵션을 **변경 내용이 GitHub에 푸시될 경우에 빌드**라고 했습니다.)

   f. **Java 응용 프로그램에 대한 빌드 섹션:** **빌드 섹션** 아래의 **빌드 단계 추가** 드롭다운에서 **Gradle 스크립트 호출** 옵션을 선택합니다. 고급 메뉴가 열리는 위젯에서 응용 프로그램에 대한 **루트 빌드 스크립트**의 경로를 지정합니다. 지정된 경로에서 build.gradle을 선택하면 이에 따라 적절하게 작동합니다. Eclipse 플러그 인 또는 Yeoman 생성기를 사용하여 ``MyActor``이라는 프로젝트를 만든 경우 루트 빌드 스크립트는 ``${WORKSPACE}/MyActor``를 포함해야 합니다. 이 기능을 표시한 예제는 다음 스크린샷을 참조하세요.

    ![Service Fabric Jenkins 빌드 작업][build-step]

   g. **.Net Core 응용 프로그램에 대한 빌드 섹션:** **빌드 섹션** 아래의 **빌드 단계 추가** 드롭다운에서 **셸 실행** 옵션을 선택합니다. 표시되는 명령 상자에서 먼저 디렉터리를 build.sh 파일이 있는 경로로 변경해야 합니다. 디렉터리가 변경되면 build.sh 스크립트를 실행할 수 있으며 응용 프로그램이 빌드됩니다.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    다음은 Jenkins 작업 이름 CounterServiceApplication을 사용하여 [카운터 서비스](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService)를 빌드하는 데 사용되는 명령 예제입니다.

    ![Service Fabric Jenkins 빌드 작업][build-step-dotnet]
  
   h. **빌드 후 작업** 드롭다운에서 **Service Fabric 프로젝트 배포**를 선택합니다. 여기에서 Jenkins가 컴파일한 Service Fabric 응용 프로그램을 배포한 클러스터 세부 정보를 제공해야 합니다. 인증서 경로는 컨테이너 내에서 Certificates_JenkinsOnSF_Code_MyCert_PEM 환경 변수의 값을 반환하여 찾을 수 있습니다. 이 경로는 클라이언트 키 및 클라이언트 인증서 필드에 사용할 수 있습니다.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    또한 응용 프로그램을 배포하는 데 사용된 추가 응용 프로그램 세부 정보를 제공할 수 있습니다. 이 기능을 표시한 예제는 다음 스크린샷을 참조하세요.

    ![Service Fabric Jenkins 빌드 작업][post-build-step]

      > [!NOTE]
      > 이 클러스터는 Service Fabric을 사용하여 Jenkins 컨테이너 이미지를 배포하는 경우에 Jenkins 컨테이너 응용 프로그램을 호스팅하는 것과 동일할 수 있습니다.
      >

## <a name="next-steps"></a>다음 단계
이제 GitHub 및 Jenkins가 구성되었습니다. 리포지토리 예제의 ``MyActor`` 프로젝트에서 일부 샘플을 변경하는 것을 고려합니다. https://github.com/sayantancs/SFJenkins 원격 ``master`` 분기(또는 사용하도록 구성된 분기)에 변경 내용을 푸시합니다. 사용자가 구성한 Jenkins 작업 ``MyJob``이 트리거됩니다. 그러면 GitHub에서 변경 내용을 가져오고, 해당 내용을 빌드하고, 빌드 후 작업에서 지정한 클러스터 끝점으로 응용 프로그램을 배포합니다.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

