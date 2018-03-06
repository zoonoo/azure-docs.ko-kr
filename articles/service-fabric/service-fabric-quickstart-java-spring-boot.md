---
title: "Azure Service Fabric에 Spring Boot 응용 프로그램 배포 | Microsoft Docs"
description: "이 자습서에서는 Spring Boot 응용 프로그램 예제를 사용하여 Azure Service Fabric에 Spring Boot 응용 프로그램을 배포합니다."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: ab860b8525bcb77d3ab35d3f649532713c661b61
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>빠른 시작 Azure에 Java Spring Boot 응용 프로그램 배포
Azure Service Fabric은 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

이 빠른 시작에서는 Service Fabric에 Spring Boot 응용 프로그램을 배포하는 방법을 보여줍니다. 이 빠른 시작에서는 Spring 웹 사이트의 [시작](https://spring.io/guides/gs/spring-boot/) 샘플을 사용합니다. 이 빠른 시작에서는 친숙한 명령줄 도구를 사용하여 Spring Boot 샘플을 Service Fabric 응용 프로그램으로 배포하는 방법을 안내합니다. 작업이 완료되면 Service Fabric에서 Spring Boot 시작 샘플을 사용합니다. 

![응용 프로그램 스크린샷](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Service Fabric에 Spring Boot 응용 프로그램 배포
> * 로컬 클러스터에 응용 프로그램 배포 
> * Azure에서 응용 프로그램을 클러스터에 배포
> * 응용 프로그램을 여러 노드에 걸쳐 스케일 아웃
> * 가용성에 아무런 영향을 주지 않고 서비스에 대한 장애 조치 수행

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 다음이 필요합니다.
1. [Service Fabric SDK 및 Service Fabric 명령줄 인터페이스 (CLI) 설치](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git 설치](https://git-scm.com/)
3. [Yeoman 설치](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Java 환경 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>샘플 다운로드
명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 Spring Boot 시작 샘플 앱을 복제합니다.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Spring Boot 응용 프로그램 패키지 
1. 복제된 `gs-spring-boot` 디렉터리 내에서 `yo azuresfguest` 명령을 실행합니다. 

2. 각 프롬프트에 다음 세부 정보를 입력합니다. 

    ![Yeoman 항목](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` 폴더에 `entryPoint.sh`라는 파일을 만듭니다. 파일에 다음을 추가합니다. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

이 단계에서는 Service Fabric에 배포할 수 있는 Spring Boot 시작 샘플에 Service Fabric 응용 프로그램을 만들었습니다.

## <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
1. 다음 명령을 실행하여 클러스터를 만듭니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    로컬 클러스터를 시작하는 데 몇 시간이 걸립니다. 클러스터가 완전히 작동 중인지 확인하려면 **http://localhost:19080**에서 Service Fabric Explorer에 액세스합니다. 5개의 정상 노드는 로컬 클러스터가 작동되어 실행 중임을 나타냅니다. 
    
    ![로컬 클러스터 정상](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. `gs-spring-boot/SpringServiceFabric` 폴더로 이동합니다.
3. 다음 명령을 실행하여 로컬 클러스터에 연결합니다. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. `install.sh` 스크립트를 실행합니다. 

    ```bash
    ./install.sh
    ```

5. 즐겨찾는 웹 브라우저를 열고 **http://localhost:8080**에 액세스하여 응용 프로그램에 액세스합니다. 

    ![응용 프로그램 프런트 엔드](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
이제 Service Fabric 클러스터에 배포된 Spring Boot 응용 프로그램에 액세스할 수 있습니다.  

## <a name="deploy-the-application-to-azure"></a>Azure에 응용 프로그램 배포

### <a name="set-up-your-azure-service-fabric-cluster"></a>Azure Service Fabric Cluster 설정
응용 프로그램을 Azure의 클러스터에 배포하려면 고유한 클러스터를 만듭니다.

파티 클러스터는 Azure에서 호스팅되는 시간이 제한된 체험용 Service Fabric 클러스터이며 누구든지 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있는 Service Fabric 팀에서 실행합니다. 파티 클러스터에 대한 액세스 권한을 얻으려면 [지침에 따릅니다](http://aka.ms/tryservicefabric). 

보안 파티 클러스터에서 관리 작업을 수행하기 위해 Service Fabric Explorer, CLI 또는 Powershell을 사용할 수 있습니다. Service Fabric Explorer를 사용하려면 파티 클러스터 웹 사이트에서 PFX 파일을 다운로드하고, 인증서 저장소(Windows 또는 Mac) 또는 브라우저 자체(Ubuntu)로 인증서를 가져와야 합니다. 파티 클러스터의 자체 서명된 인증서에는 암호가 없습니다. 

Powershell 또는 CLI를 사용하여 관리 작업을 수행하려면 PFX(Powershell) 또는 PEM(CLI)이 필요합니다. PFX를 PEM 파일로 변환하려면 다음 명령을 실행합니다.  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

자체 클러스터를 만드는 방법은 [Azure에서 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

> [!Note]
> Spring Boot 서비스는 들어오는 트래픽에 대해 포트 8080에서 수신 대기하도록 구성됩니다. 클러스터에 대해 포트가 열려 있는지 확인합니다. Party 클러스터를 사용하는 경우 이 포트가 열려 있습니다.
>

### <a name="deploy-the-application-using-cli"></a>CLI를 사용하여 응용 프로그램 배포
응용 프로그램과 클러스터가 준비되면 명령줄에서 클러스터에 직접 배포할 수 있습니다.

1. `gs-spring-boot/SpringServiceFabric` 폴더로 이동합니다.
2. 다음 명령을 실행하여 Azure 클러스터에 연결합니다. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    클러스터가 자체 서명된 인증서로 보호되는 경우 실행할 명령은 다음과 같습니다. 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. `install.sh` 스크립트를 실행합니다. 

    ```bash
    ./install.sh
    ```

4. 즐겨찾는 웹 브라우저를 열고 **http://\<ConnectionIPOrUrl>:8080**에 액세스하여 응용 프로그램에 액세스합니다. 

    ![응용 프로그램 프런트 엔드](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
이제 Service Fabric 클러스터에 배포된 Spring Boot 응용 프로그램에 액세스할 수 있습니다.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>클러스터에서 응용 프로그램 및 서비스 크기 조정
서비스는 해당 서비스에 대한 로드 변동량을 수용하도록 클러스터 간에 쉽게 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 서비스 크기를 조정합니다. 서비스의 크기를 조정하는 여러 가지 방법이 있으며 Service Fabric CLI(sfctl)의 스크립트 또는 명령을 사용할 수 있습니다. 이 예제에서는 Service Fabric Explorer를 사용합니다.

Service Fabric Explorer는 모든 Service Fabric 클러스터에서 실행되고 클러스터 HTTP 관리 포트(19080)로 이동하여 브라우저에서 액세스할 수 있습니다(예: `http://localhost:19080`).

웹 프런트 엔드 서비스의 크기를 조정하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `http://localhost:19080`
2. 트리 뷰에서 **fabric:/SpringServiceFabric/SpringGettingStarted** 노드 옆에 있는 줄임표(...)를 클릭하고 **서비스 크기 조정**을 선택합니다.

    ![Service Fabric Explorer Scale Service](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    이제 서비스의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 숫자를 **3**으로 변경하고 **Scale Service**를 클릭합니다.

    명령줄을 사용하여 서비스의 크기를 조정하는 다른 방법은 다음과 같습니다.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. 트리 뷰에서 **fabric:/SpringServiceFabric/SpringGettingStarted** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

    ![Service Fabric Explorer Scale Service 완료](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    서비스에는 3개의 인스턴스가 있고, 트리 뷰에는 인스턴스가 실행되는 노드가 표시됩니다.

이 간단한 관리 작업을 통해 Spring 서비스에서 사용자 로드를 처리할 수 있도록 사용 가능한 리소스가 늘어납니다. 서비스를 안정적으로 실행하기 위해 서비스의 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="fail-over-services-in-a-cluster"></a>클러스터의 서비스 장애 조치 
서비스 장애 조치를 보여 주기 위해 Service Fabric Explorer를 사용하여 노드 다시 시작을 시뮬레이션합니다. 서비스의 인스턴스 하나만 실행되고 있는지 확인합니다. 

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `http://localhost:19080`
2. 서비스의 인스턴스를 실행하는 노드 옆에 있는 줄임표(...)를 클릭하고 노드를 다시 시작합니다. 

    ![Service Fabric Explorer 다시 시작 노드](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. 서비스의 인스턴스가 다른 노드로 이동되고, 응용 프로그램에서 가동 중지 시간이 발생하지 않습니다. 

    ![Service Fabric Explorer 다시 시작 노드 성공](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Service Fabric에 Spring Boot 응용 프로그램 배포
> * 로컬 클러스터에 응용 프로그램 배포 
> * Azure에서 응용 프로그램을 클러스터에 배포
> * 응용 프로그램을 여러 노드에 걸쳐 스케일 아웃
> * 가용성에 아무런 영향을 주지 않고 서비스에 대한 장애 조치 수행

* [Service Fabric 프로그래밍 모델을 사용하여 Java 마이크로 서비스 빌드](service-fabric-quickstart-java-reliable-services.md)에 대해 자세히 알아봅니다.
* [Jenkins를 사용하여 지속적인 통합 및 배포 설정](service-fabric-cicd-your-linux-applications-with-jenkins.md)에 대해 알아봅니다.
* 다른 [Java 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started) 체크 아웃