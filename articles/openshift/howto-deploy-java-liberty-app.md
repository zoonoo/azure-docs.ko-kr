---
title: Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty를 사용 하 여 Java 응용 프로그램 배포
description: Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty를 사용 하 여 Java 응용 프로그램을 배포 합니다.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, 마이크로 프로필, liberty, websphere-liberty, aro, openshift, red hat
ms.openlocfilehash: 41891b58942efbfd705747cc16219185f2a2daa2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018395"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty를 사용 하 여 Java 응용 프로그램 배포

이 가이드에서는 open Liberty/WebSphere Liberty 런타임에 Java, Java EE, [자카르타 ee](https://jakarta.ee/)또는 [마이크로 프로필](https://microprofile.io/) 응용 프로그램을 실행 한 다음 open Liberty 연산자를 사용 하 여 컨테이너 화 된 응용 프로그램을 Azure Red HAT openshift (ARO) 4 클러스터에 배포 하는 방법을 보여 줍니다. 이 문서에서는 Liberty 응용 프로그램을 준비 하 고, application Docker 이미지를 빌드하고, ARO 4 클러스터에서 컨테이너 화 된 응용 프로그램을 실행 하는 과정을 안내 합니다.  Open Liberty에 대 한 자세한 내용은 [Open Liberty project 페이지](https://openliberty.io/)를 참조 하세요. IBM WebSphere 대 한 자세한 내용은 Liberty [Websphere Liberty product 페이지를](https://www.ibm.com/cloud/websphere-liberty)참조 하세요.

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>필수 조건

이 가이드를 성공적으로 진행 하려면 다음 필수 구성 요소를 완료 합니다.

> [!NOTE]
> Azure Red Hat OpenShift에는 OpenShift 클러스터를 만들고 실행하는 데 최소 40개의 코어가 필요합니다. 새 Azure 구독에 대한 기본 Azure 리소스 할당량은 이 요구 사항을 충족하지 않습니다. 리소스 제한 늘리기를 요청하려면 [표준 할당량: VM 시리즈별 제한 늘리기](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)를 참조하세요. 무료 평가판 구독은 할당량 증가에 적합 하지 않습니다. 할당량 증가를 요청 하기 전에 [종 량 제 구독으로 업그레이드](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription) 하세요.

1. Unix와 비슷한 운영 체제가 설치 된 로컬 컴퓨터를 준비 합니다 (예: Ubuntu, macOS).
1. Java SE 구현 (예: [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9))을 설치 합니다.
1. [Maven](https://maven.apache.org/download.cgi) 3.5.0 이상을 설치 합니다.
1. OS에 대 한 [Docker](https://docs.docker.com/get-docker/) 를 설치 합니다.
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 2.0.75 이상을 설치 합니다.
1. [`envsubst`](https://command-not-found.com/envsubst)운영 체제에 사전 설치 되지 않은 경우를 확인 하 고 설치 합니다.
1. 로컬 시스템에서이 샘플에 대 한 코드를 복제 합니다. 이 샘플은 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro)에 있습니다.
1. [Azure Red Hat OpenShift 4 클러스터 만들기](/azure/openshift/tutorial-create-cluster)의 지침을 따릅니다.

   "Red Hat pull 비밀 가져오기" 단계는 선택 사항으로 레이블이 지정 되어 있지만 **이 문서에 필요** 합니다.  끌어오기 암호를 사용 하면 Azure Red Hat OpenShift 클러스터에서 Open Liberty 연산자를 찾을 수 있습니다.

   클러스터에서 메모리를 많이 사용 하는 응용 프로그램을 실행 하려는 경우 매개 변수를 사용 하 여 작업자 노드에 대 한 적절 한 가상 컴퓨터 크기를 지정 합니다 `--worker-vm-size` . 예를 들어 `Standard_E4s_v3` 는 클러스터에 Elasticsearch 연산자를 설치 하기 위한 최소 가상 머신 크기입니다. 자세한 내용은 다음을 참조하세요.

   * [클러스터를 만드는 Azure CLI](https://docs.microsoft.com/cli/azure/aro?view=azure-cli-latest&preserve-view=true#az-aro-create)
   * [메모리 최적화를 위해 지원 되는 가상 머신 크기](/azure/openshift/support-policies-v4#memory-optimized)
   * [Elasticsearch 연산자를 설치 하기 위한 필수 구성 요소](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. [Azure Red Hat OpenShift 4 클러스터에 연결](/azure/openshift/tutorial-connect-cluster)의 단계를 수행 하 여 클러스터에 연결 합니다.
   * 이 문서의 뒷부분에 나오는 명령을 사용 하기 때문에 "OpenShift CLI 설치"의 단계를 수행 해야 `oc` 합니다.
   * 와 같은 클러스터 콘솔 URL을 기록 합니다 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * 자격 증명을 기록해 둡니다 `kubeadmin` .

1. 사용자에 대 한 토큰을 사용 하 여 OpenShift CLI에 로그인 할 수 있는지 확인 `kubeadmin` 합니다.

### <a name="enable-the-built-in-container-registry-for-openshift"></a>OpenShift에 기본 제공 컨테이너 레지스트리 사용

이 자습서의 단계에서는 OpenShift로 액세스할 수 있는 컨테이너 레지스트리에 푸시 해야 하는 Docker 이미지를 만듭니다. 가장 간단한 옵션은 OpenShift에서 제공 하는 기본 제공 레지스트리를 사용 하는 것입니다. 기본 제공 컨테이너 레지스트리를 사용 하도록 설정 하려면 [Azure Red Hat OpenShift 4 용 기본 제공 컨테이너 레지스트리 구성](built-in-container-registry.md)의 단계를 따르세요. 이 문서에서는 이러한 단계의 세 가지 항목을 사용 합니다.

* OpenShift 웹 콘솔에 로그인 하는 데 사용 되는 Azure AD 사용자의 사용자 이름 및 암호입니다.
* `oc whoami`OpenShift CLI에 로그인 하는 단계를 수행한 후의 출력입니다.  이 값은 토론에 대해 **aad 사용자** 라고 합니다.
* 컨테이너 레지스트리 URL입니다.

기본 제공 컨테이너 레지스트리를 사용 하도록 설정 하는 단계를 완료할 때 이러한 항목을 적어 둡니다.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Java 앱에 대 한 OpenShift 네임 스페이스 만들기

1. 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 `kubeadmin` 합니다.
2. **관리**  >  **네임** 스페이스  >  **네임 스페이스 만들기** 로 이동 합니다.
3. `open-liberty-demo` **이름** 을 입력 하 고 다음 그림과 같이 **만들기** 를 선택 합니다.

   ![네임 스페이스 만들기](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Demo 프로젝트에 대 한 관리자 만들기

이미지 관리 외에도 **aad 사용자** 에 게는 ARO 4 클러스터의 데모 프로젝트에서 리소스를 관리할 수 있는 관리 권한이 부여 됩니다.  OpenShift CLI에 로그인 하 고 다음 단계를 수행 하 여 필요한 권한을 **aad 사용자** 에 게 부여 합니다.

1. 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 `kubeadmin` 합니다.
1. 웹 콘솔의 맨 오른쪽에서 로그인 한 사용자의 상황에 맞는 메뉴를 확장 하 고 **로그인 명령 복사** 를 선택 합니다.
1. 필요한 경우 동일한 사용자를 사용 하 여 새 탭 창에 로그인 합니다.
1. **토큰 표시** 를 선택 합니다.
1. 아래와 같이 **이 토큰을 사용 하 여 로그인** 아래 나열 된 값을 클립보드에 복사 하 고 셸에서 실행 합니다.
1. 다음 명령을 실행 하 여 `admin` 네임 스페이스의 **aad 사용자** 에 게 역할을 부여 합니다 `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Open Liberty OpenShift 연산자를 설치 합니다.

클러스터를 만들고 연결한 후 Open Liberty 연산자를 설치 합니다.  Open Liberty 연산자의 기본 시작 페이지는 [GitHub](https://github.com/OpenLiberty/open-liberty-operator)에 있습니다.

1. 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 `kubeadmin` 합니다.
2. **Operators** OperatorHub로 이동 하 여  >  **OperatorHub** **Open Liberty Operator** 를 검색 합니다.
3. 검색 결과에서 **Open Liberty Operator** 를 선택 합니다.
4. **설치** 를 선택합니다.
5. Popup **Create Operator Subscription** 에서 **클러스터의 모든 네임 스페이스 (기본값)** 를 선택 하 고 **,** **업데이트 채널** 의 경우 **beta** 를, **승인 전략** 의 경우 **자동** 을 선택 합니다.

   ![Open Liberty 연산자에 대 한 운영자 구독 만들기](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Open Liberty 연산자가 표시 될 때까지 **구독** 을 선택 하 고 1 ~ 2 분을 기다립니다.
7. "Succeeded" 상태가 포함 된 Open Liberty 연산자를 관찰 합니다.  그렇지 않은 경우 계속 진행 하기 전에 문제를 진단 하 고 해결 합니다.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Open Liberty을 표시 하는 설치 된 운영자가 설치 되어 있습니다.":::

## <a name="prepare-the-liberty-application"></a>Liberty 응용 프로그램 준비

이 가이드의 예제로 Java EE 8 응용 프로그램을 사용 합니다. Open Liberty은 [JAVA EE 8 전체 프로필](https://javaee.github.io/javaee-spec/javadocs/) 호환 서버 이므로 응용 프로그램을 쉽게 실행할 수 있습니다.  Open Liberty은 또한 [자카르타 EE 8 전체 프로필 호환](https://jakarta.ee/specifications/platform/8/apidocs/)입니다.

### <a name="run-the-application-on-open-liberty"></a>Open Liberty에서 응용 프로그램 실행

Open Liberty에서 응용 프로그램을 실행 하려면 [Liberty Maven 플러그 인](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) 에서 배포용 응용 프로그램을 패키지할 수 있도록 개방형 Liberty 서버 구성 파일을 만들어야 합니다. Liberty Maven 플러그 인은 OpenShift에 응용 프로그램을 배포 하는 데 필요 하지 않습니다.  그러나이 예제에서는 dev (Open Liberty 's developer) 모드를 사용 하 여이 예제를 사용 합니다.  개발자 모드를 사용 하면 응용 프로그램을 로컬로 쉽게 실행할 수 있습니다. 로컬 컴퓨터에서 다음 단계를 완료 합니다.

1. `2-simple/src/main/liberty/config/server.xml`로 복사 하 여 `1-start/src/main/liberty/config` 기존의 길이가 0 인 파일을 덮어씁니다. 이렇게 `server.xml` 하면 Open Liberty 서버가 JAVA EE 기능으로 구성 됩니다.
1. `2-simple/pom.xml`을로 복사 `1-start/pom.xml` 합니다.  이 단계에서는를 `liberty-maven-plugin` POM에 추가 합니다.
1. `1-start`로컬 클론의 디렉터리로 변경 합니다.
1. `mvn clean package`콘솔에서를 실행 하 여 디렉터리에 war 패키지를 생성 `javaee-cafe.war` `./target` 합니다.
1. `mvn liberty:dev`을 실행 하 여 개발 모드에서 Open Liberty를 시작 합니다.
1. 서버를 시작할 때까지 기다립니다. 콘솔 출력이 다음 메시지와 함께 종료 됩니다.

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. `http://localhost:9080/`브라우저에서를 열어 응용 프로그램 홈 페이지를 방문 합니다. 응용 프로그램은 다음 이미지와 유사 하 게 표시 됩니다.

   ![JavaEE Cafe 웹 UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Ctrl + **C** 를 눌러 응용 프로그램을 중지 하 고 Liberty 서버를 엽니다.

`2-simple`로컬 클론의 디렉터리에는 위의 변경 내용이 이미 적용 된 Maven 프로젝트가 표시 됩니다.

## <a name="prepare-the-application-image"></a>응용 프로그램 이미지 준비

ARO 4 클러스터에서 Liberty 응용 프로그램을 배포 하 고 실행 하려면 [Open Liberty container images](https://github.com/OpenLiberty/ci.docker) 또는 [websphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)를 사용 하 여 응용 프로그램을 Docker 이미지로 컨테이너 화 합니다.

### <a name="build-application-image"></a>응용 프로그램 이미지 빌드

응용 프로그램 이미지를 빌드하려면 다음 단계를 완료 합니다.

1. `2-simple`로컬 클론의 디렉터리로 변경 합니다.
2. `mvn clean package`응용 프로그램을 패키지 하려면를 실행 합니다.
3. 다음 명령 중 하나를 실행 하 여 응용 프로그램 이미지를 빌드합니다.
   * Open Liberty base 이미지를 사용 하 여 빌드:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * WebSphere Liberty 기본 이미지를 사용 하 여 빌드:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Docker를 사용 하 여 응용 프로그램을 로컬로 실행

원격 클러스터에 컨테이너 화 된 응용 프로그램을 배포 하기 전에 로컬 Docker를 사용 하 여를 실행 하 여 작동 하는지 확인 합니다.

1. `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0`콘솔에서를 실행 합니다.
2. Liberty 서버가 시작 되 고 응용 프로그램이 성공적으로 배포 될 때까지 기다립니다.
3. `http://localhost:9080/`브라우저에서를 열어 응용 프로그램 홈 페이지를 방문 합니다.
4. **Control-C** 를 눌러 응용 프로그램 및 Liberty 서버를 중지 합니다.

### <a name="push-the-image-to-the-container-image-registry"></a>컨테이너 이미지 레지스트리에 이미지 푸시

응용 프로그램의 상태에 만족 하는 경우 아래 지침에 따라 기본 제공 컨테이너 이미지 레지스트리에 푸시합니다.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Azure AD 사용자로 OpenShift CLI에 로그인 합니다.

1. Azure AD 사용자의 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 합니다.

   1. InPrivate, Incognito 또는 기타 해당 브라우저 창 기능을 사용 하 여 콘솔에 로그인 합니다.
   1. **Openid connect** 선택

   > [!NOTE]
   > 여기에서 로그인 하는 데 사용 하는 사용자 이름 및 암호를 기록해 둡니다. 이 사용자 이름 및 암호는이 문서 및 다른 문서에서 다른 작업의 관리자 역할을 합니다.
1. 다음 단계를 사용 하 여 OpenShift CLI로 로그인 합니다.  논의를 위해이 프로세스를 라고 `oc login` 합니다.
   1. 웹 콘솔의 맨 오른쪽에서 로그인 한 사용자의 상황에 맞는 메뉴를 확장 하 고 **로그인 명령 복사** 를 선택 합니다.
   1. 필요한 경우 동일한 사용자를 사용 하 여 새 탭 창에 로그인 합니다.
   1. **토큰 표시** 를 선택 합니다.
   1. 아래와 같이 **이 토큰을 사용 하 여 로그인** 아래 나열 된 값을 클립보드에 복사 하 고 셸에서 실행 합니다.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>OpenShift에 대 한 컨테이너 레지스트리에 컨테이너 이미지 푸시

다음 명령을 실행 하 여 OpenShift에 대 한 컨테이너 레지스트리에 이미지를 푸시합니다.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

성공적인 출력이 다음과 같이 표시 됩니다.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

다음 명령을 사용 하 여 기본 제공 컨테이너 이미지 레지스트리에 이미지를 푸시합니다.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>ARO 4 클러스터에 응용 프로그램 배포

이제 필수 구성 요소를 통해 작업할 때 앞서 만든 Azure Red Hat OpenShift 4 클러스터에 샘플 Liberty 응용 프로그램을 배포할 수 있습니다.

### <a name="deploy-the-application-from-the-web-console"></a>웹 콘솔에서 응용 프로그램 배포

Open Liberty 연산자를 사용 하 여 Liberty 응용 프로그램을 관리 하기 때문에 "OpenLibertyApplication" 형식의 *사용자 지정 리소스 정의* 인스턴스를 만들어야 합니다. 그런 다음 운영자는 배포에 필요한 OpenShift 리소스를 관리 하는 모든 측면을 처리 합니다.

1. Azure AD 사용자의 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 합니다.
1. **홈** 을 확장 하 고 **프로젝트**  >  **liberty-demo** 를 선택 합니다.
1. **운영자**  >  **설치 연산자** 로 이동 합니다.
1. 페이지 중간에서 **Open Liberty Operator** 를 선택 합니다.
1. 페이지 중간에서 **Open Liberty Application** 을 선택 합니다.  사용자 인터페이스의 항목 탐색은 사용 중인 기술의 실제 포함 계층 구조를 미러링합니다.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java 포함](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. **OpenLibertyApplication 만들기** 를 선택 합니다.
1. 생성 된 yaml를에 있는 자신으로 바꿉니다 `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. **만들기** 를 선택합니다. OpenLibertyApplications 목록으로 돌아갑니다.
1. **Javaee-카페-simple** 을 선택 합니다.
1. 페이지 중간에서 **리소스** 를 선택 합니다.
1. 테이블에서 **javaee-카페** 에 대 한 링크를 **경로** **종류** 와 함께 선택 합니다.
1. 열리는 페이지에서 아래 **위치** 에 있는 링크를 선택 합니다.

브라우저에서 열린 응용 프로그램 홈 페이지가 표시 됩니다.

### <a name="delete-the-application-from-the-web-console"></a>웹 콘솔에서 응용 프로그램을 삭제 합니다.

응용 프로그램을 완료 한 후에는 다음 단계에 따라 Open Shift에서 응용 프로그램을 삭제 합니다.

1. 왼쪽 탐색 창에서 **연산자** 에 대 한 항목을 확장 합니다.
1. **설치 된 운영자** 를 선택 합니다.
1. **Open Liberty 연산자** 를 선택 합니다.
1. 페이지 중간에서 **Liberty 응용 프로그램 열기** 를 선택 합니다.
1. 세로 줄임표 (세로 점 3 개)를 선택한 다음 **OpenLiberty 응용 프로그램 삭제** 를 선택 합니다.

### <a name="deploy-the-application-from-cli"></a>CLI에서 응용 프로그램 배포

웹 콘솔 GUI를 사용 하는 대신 CLI에서 응용 프로그램을 배포할 수 있습니다. 아직 수행 하지 않은 경우에는 `oc` Red Hat 설명서 [CLI 시작](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)을 참조 하 여 명령줄 도구를 다운로드 하 여 설치 합니다.

1. Azure AD 사용자의 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 합니다.
2. Azure AD 사용자에 대 한 토큰을 사용 하 여 OpenShift CLI에 로그인 합니다.
3. 로컬 클론의 디렉터리를 변경 하 `2-simple` 고 다음 명령을 실행 하 여 Liberty 응용 프로그램을 ARO 4 클러스터에 배포 합니다.  명령 출력도 인라인으로 표시 됩니다.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. `1/1` `READY` 계속 하기 전에 열 아래를 확인 합니다.  그렇지 않은 경우 계속 진행 하기 전에 문제를 조사 하 고 해결 합니다.
5. 다음과 같이 명령을 사용 하 여 응용 프로그램에 대 한 경로 호스트를 검색 `oc get route` 합니다.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Liberty 응용 프로그램이 실행 되 면 브라우저에서 **경로 호스트** 의 출력을 열어 응용 프로그램 홈 페이지를 방문 합니다.

### <a name="delete-the-application-from-cli"></a>CLI에서 응용 프로그램 삭제

이 명령을 실행 하 여 CLI에서 응용 프로그램을 삭제 합니다.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

[자습서: Azure Red Hat OpenShift 4 클러스터 삭제](/azure/openshift/tutorial-delete-cluster) 의 단계를 수행 하 여 ARO 클러스터를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
>
> * Liberty 응용 프로그램 준비
> * 응용 프로그램 이미지 빌드
> * GUI 및 CLI를 사용 하 여 ARO 4 클러스터에서 컨테이너 화 된 응용 프로그램 실행

이 가이드에 사용 되는 참조에서 자세히 알아볼 수 있습니다.

* [Liberty 열기](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty 연산자](https://github.com/OpenLiberty/open-liberty-operator)
* [Liberty 서버 구성 열기](https://openliberty.io/docs/ref/config/)
* [Liberty Maven 플러그 인](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Liberty 컨테이너 이미지 열기](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)
