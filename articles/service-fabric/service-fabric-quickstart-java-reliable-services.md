---
title: "Azure Service Fabric Java 응용 프로그램 만들기 | Microsoft Docs"
description: "Service Fabric 빠른 시작 샘플을 사용하여 Azure에 대한 Java 응용 프로그램을 만듭니다."
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
ms.date: 10/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 8f4d121ba76d63b70fa6976125457942a0e98aa9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-java-application"></a>Java 응용 프로그램 만들기
Azure Service Fabric은 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

이 빠른 시작은 Linux 개발자 컴퓨터에서 Eclipse IDE를 사용하여 Microsoft Azure Service Fabric에 첫 번째 Java 응용 프로그램을 배포하는 방법을 보여줍니다. 완료하면 투표 결과를 클러스터의 상태 저장 백 엔드 서비스에 저장하는 Java 웹 프런트 엔드가 있는 투표 응용 프로그램이 생깁니다.

![응용 프로그램 스크린샷](./media/service-fabric-quickstart-java/votingapp.png)

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Eclipse를 Service Fabric Java 응용 프로그램을 위한 도구로 사용
> * 로컬 클러스터에 응용 프로그램 배포 
> * Azure에서 응용 프로그램을 클러스터에 배포
> * 응용 프로그램을 여러 노드에 걸쳐 스케일 아웃

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 다음이 필요합니다.
1. [Service Fabric SDK 및 Service Fabric 명령줄 인터페이스 (CLI) 설치](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git 설치](https://git-scm.com/)
3. [Eclipse 설치](https://www.eclipse.org/downloads/)
4. Eclipse 플러그 인을 설치하기 위한 선택적 단계를 수행하여 [Java 환경 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) 

## <a name="download-the-sample"></a>샘플 다운로드
명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.
```
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
1. 다음 명령을 실행하여 클러스터를 만듭니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    로컬 클러스터를 시작하는 데 몇 시간이 걸립니다. 클러스터가 완전히 작동 중인지 확인하려면 **http://localhost:19080**에서 Service Fabric Explorer에 액세스합니다. 5개의 정상 노드는 로컬 클러스터가 작동되어 실행 중임을 나타냅니다. 
    
    ![로컬 클러스터 정상](./media/service-fabric-quickstart-java/localclusterup.png)

2. Eclipse를 엽니다.
3. File -> Open Projects from File System...을 선택합니다. 
4. Directory를 클릭하고 Github에서 복제한 `service-fabric-java-quickstart` 폴더에서 `Voting` 디렉터리를 선택합니다. Finish를 클릭합니다. 

    ![Eclipse Import 대화 상자](./media/service-fabric-quickstart-java/eclipseimport.png)
    
5. 이제 Eclipse용 Package Explorer에 `Voting` 프로젝트가 있습니다. 
6. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Service Fabric** 드롭다운 아래에서 **Publish Application...**을 클릭합니다. **PublishProfiles/Local.json**을 Target Profile로 선택하고 Publish를 클릭합니다. 

    ![Publish Dialog Local](./media/service-fabric-quickstart-java/localjson.png)
    
7. 즐겨찾는 웹 브라우저를 열고 **http://localhost:8080**에 액세스하여 응용 프로그램에 액세스합니다. 

    ![응용 프로그램 프런트 엔드](./media/service-fabric-quickstart-java/runninglocally.png)
    
이제 투표 옵션 집합을 추가하고 투표 하기를 시작할 수 있습니다. 응용 프로그램이 실행되고 모든 데이터가 Service Fabric 클러스터에 저장되며 별도의 데이터베이스가 필요하지 않습니다.

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

### <a name="add-certificate-information-to-your-application"></a>응용 프로그램에 인증서 정보 추가

인증서 지문은 Service Fabric 프로그래밍 모델을 사용하므로 응용 프로그램에 추가해야 합니다. 

1. 보안 클러스터에서 실행할 때 인증서의 지문이 ```Voting/VotingApplication/ApplicationManiest.xml``` 파일에 있어야 합니다. 다음 명령을 실행하여 인증서의 지문을 추출합니다.

    ```bash
    openssl x509 -in [CERTIFICATE_FILE] -fingerprint -noout
    ```

2. ```Voting/VotingApplication/ApplicationManiest.xml```에서 **ApplicationManifest** 태그 아래에 다음 코드 조각을 합니다. **X509FindValue**는 이전 단계의 지문이어야 합니다(세미콜론 없음). 

    ```xml
    <Certificates>
        <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
    </Certificates>   
    ```
    
### <a name="deploy-the-application-using-eclipse"></a>Eclipse를 사용하여 응용 프로그램 배포
응용 프로그램과 클러스터가 준비되면 Eclipse에서 클러스터에 직접 배포할 수 있습니다.

1. **PublishProfiles** 디렉터리 아래에서 **Cloud.json** 파일을 열고 `ConnectionIPOrURL`과 `ConnectionPort` 필드에 정보를 적절히 입력합니다. 예제가 제공됩니다. 

    ```bash
    {
         "ClusterConnectionParameters": 
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Service Fabric** 드롭다운 아래에서 **Publish Application...**을 클릭합니다. **PublishProfiles/Cloud.json**을 Target Profile로 선택하고 Publish를 클릭합니다. 

    ![Publish Dialog Cloud](./media/service-fabric-quickstart-java/cloudjson.png)

3. 즐겨찾는 웹 브라우저를 열고 **http://\<ConnectionIPOrURL>:8080**에 액세스하여 응용 프로그램에 액세스합니다. 

    ![응용 프로그램 프런트 엔드 클라우드](./media/service-fabric-quickstart-java/runningcloud.png)
    
## <a name="scale-applications-and-services-in-a-cluster"></a>클러스터에서 응용 프로그램 및 서비스 크기 조정
서비스는 해당 서비스에 대한 로드 변동량을 수용하도록 클러스터 간에 쉽게 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 서비스 크기를 조정합니다. 서비스의 크기를 조정하는 여러 가지 방법이 있으며 Service Fabric CLI(sfctl)의 스크립트 또는 명령을 사용할 수 있습니다. 이 예제에서는 Service Fabric Explorer를 사용합니다.

Service Fabric Explorer는 모든 Service Fabric 클러스터에서 실행되고 클러스터 HTTP 관리 포트(19080)로 이동하여 브라우저에서 액세스할 수 있습니다(예: `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`).

웹 프런트 엔드 서비스의 크기를 조정하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`
2. 트리 뷰에서 **fabric:/Voting/VotingWeb** 노드 옆에 있는 줄임표(...)를 클릭하고 **Scale Service**를 선택합니다.

    ![Service Fabric Explorer Scale Service](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    이제 웹 프런트 엔드 서비스의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 이 수를 **2**로 변경하고 **Scale Service**를 클릭합니다.
4. 트리 뷰에서 **fabric:/Voting/VotingWeb** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

    ![Service Fabric Explorer Scale Service 완료](./media/service-fabric-quickstart-java/servicescaled.png)

    이제 두 개의 인스턴스가 있는 서비스를 인스턴스가 실행되는 노드를 볼 수 있는 트리 뷰에서 볼 수 있습니다.

이 간단한 관리 작업으로 사용자 로드를 처리하는 프런트 엔드 서비스에 사용 가능한 리소스를 두 배로 증가시킬 수 있습니다. 서비스를 안정적으로 실행하기 위해 서비스의 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Eclipse를 Service Fabric Java 응용 프로그램을 위한 도구로 사용
> * 로컬 클러스터에 Java 응용 프로그램 배포 
> * Azure에서 Java 응용 프로그램을 클러스터에 배포
> * 응용 프로그램을 여러 노드에 걸쳐 스케일 아웃

* [Eclipse를 사용하여 Java에서 서비스 디버깅](service-fabric-debugging-your-application-java.md)에 관해 자세히 알아보세요.
* [Jenkins를 사용하여 연속 통합 & 배포 설정](service-fabric-cicd-your-linux-applications-with-jenkins.md)에 관해 알아보세요.
* 다른 [Java 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started) 체크 아웃