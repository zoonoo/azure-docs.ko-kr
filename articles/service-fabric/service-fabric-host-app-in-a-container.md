---
title: "Azure Service Fabric에 컨테이너의 .NET 앱 배포 | Microsoft Docs"
description: "Docker 컨테이너의 Visual Studio에서 .NET 앱을 패키지하는 방법을 설명합니다. 그런 다음 이 새로운 &quot;컨테이너&quot; 앱은 Service Fabric 클러스터에 배포됩니다."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: ko-kr
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Azure Service Fabric에 컨테이너의 .NET 앱 배포

이 자습서에서는 Visual Studio 2017 Update 3 Preview를 사용하여 Windows 컨테이너에 기존 ASP.NET 응용 프로그램을 배포하는 방법을 보여 줍니다. 그런 다음 Visual Studio Team Services를 사용하여 Azure에 컨테이너를 배포하고 Service Fabric 클러스터에서 컨테이너를 호스트하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

1. Windows 10에서 컨테이너를 실행할 수 있도록 [Windows용 Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)를 설치합니다.
2. [Windows 10 컨테이너 빠른 시작][link-container-quickstart]을 숙지합니다.
3. 이 자습서에서는 [여기][link-fabrikam-github]에서 다운로드할 수 있는 샘플 앱인 **Fabrikam Fiber CallCenter**를 사용합니다.
4. [Azure PowerShell][link-azure-powershell-install]
5. [Visual Studio 2017용 지속적인 업데이트 도구 확장][link-visualstudio-cd-extension]
6. [Azure 구독][link-azure-subscription] 및 [Visual Studio Team Services 계정][link-vsts-account]. 모든 서비스의 무료 계층을 사용하여 이 자습서를 진행할 수 있습니다.

>[!NOTE]
>컴퓨터에서 Windows 컨테이너 이미지를 처음 실행하는 경우 Docker CE가 컨테이너에 대한 기본 이미지를 끌어와야 합니다. 이 자습서에서 사용되는 이미지의 크기는 14GB입니다. Powershell에서 다음 명령을 실행하여 기본 이미지를 끌어옵니다.
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>응용 프로그램 컨테이너화

컨테이너에서 응용 프로그램 실행을 시작하려면 Visual Studio에서 **Docker 지원**을 프로젝트에 추가해야 합니다. 응용 프로그램에 **Docker 지원**을 추가하면 두 가지 동작이 수행됩니다. 첫째, _docker_ 파일이 프로젝트에 추가됩니다. 이 새로운 파일은 컨테이너 이미지를 빌드하는 방법을 설명합니다. 둘째, 새로운 _docker-compose_ 프로젝트가 솔루션에 추가됩니다. 새 프로젝트에는 몇 가지 docker-compose 파일이 포함되어 있습니다. Docker-compose 파일을 사용하여 컨테이너가 실행되는 방법을 설명할 수 있습니다.

[Visual Studio 컨테이너 도구][link-visualstudio-container-tools] 작업에 대한 자세한 정보

### <a name="add-docker-support"></a>Docker 지원 추가

1. Visual Studio에서 **FabrikamFiber.CallCenter.sln** 파일을 엽니다.

2. **FabrikamFiber.Web** 프로젝트 > **추가** > **Docker 지원**을 마우스 오른쪽 단추로 클릭합니다.

### <a name="add-support-for-sql"></a>SQL 지원 추가

이 응용 프로그램은 SQL을 데이터 공급자로 사용하므로 응용 프로그램을 실행하려면 SQL Server가 필요합니다. 이 자습서에서는 로컬 디버깅을 위해 컨테이너에서 실행되는 SQL Server를 사용합니다.
컨테이너에서 SQL Server를 실행하려면 디버깅할 때 docker-compose.override.yml 파일에서 SQL Server 컨테이너 이미지를 참조할 수 있습니다. 

1. **솔루션 탐색기**를 엽니다.

2. **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml**을 엽니다.

3. `services:` 노드 아래에 `db:`라는 새 노드를 추가합니다. 이 노드는 컨테이너에서 SQL Server를 실행하도록 선언합니다.

   ```yml
     db:
       image: microsoft/mssql-server-windows-developer
       environment:
         sa_password: "Password1"
         ACCEPT_EULA: "Y"
       ports:
         - "1433"
       healthcheck:
         test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
         interval: 1s
         retries: 20
   ```

   >[!NOTE]
   >호스트에서 연결할 수 있기만 하면 어떤 SQL Server든지 로컬 디버깅에 사용할 수 있습니다. 그러나 **localdb**는 `container -> host` 통신을 지원하지 않습니다.

   >[!WARNING]
   >컨테이너에서 SQL Server를 실행하면 컨테이너가 중지될 때 데이터가 지속되지 않습니다. 프로덕션 환경에서는 이 구성을 사용하지 마세요.

4. `fabrikamfiber.web` 노드를 수정하고 `depends_on:`이라는 새 자식 노드를 추가합니다. 그러면 `db` 서비스(SQL Server 컨테이너)가 웹 응용 프로그램(fabrikamfiber.web)보다 먼저 시작됩니다.

   ```yml
     fabrikamfiber.web:
       depends_on:
         - db
   ```

5. **FabrikamFiber.Web** 프로젝트에서 **web.config** 파일의 연결 문자열이 컨테이너의 SQL Server를 가리키도록 업데이트합니다.

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >웹 응용 프로그램의 릴리스 버전을 빌드할 때 다른 SQL Server를 사용하려면 web.release.config 파일에 다른 연결 문자열을 추가합니다.

6. **F5** 키를 눌러 컨테이너에서 응용 프로그램을 실행하고 디버그합니다.

   내부 NAT 네트워크의 컨테이너 IP 주소(일반적으로 172.x.x.x)를 사용하여 응용 프로그램의 정의된 시작 페이지가 Edge에서 열립니다. Visual Studio 2017을 사용하여 컨테이너의 응용 프로그램을 디버그하는 방법에 대한 자세한 내용은 [이 문서][link-debug-container]를 참조하세요.

   ![컨테이너의 fabrikam 예][image-web-preview]

이제 컨테이너에서 응용 프로그램을 빌드하고 패키지할 준비가 되었습니다. 컴퓨터에서 컨테이너 이미지를 빌드한 후 컨테이너 레지스트리에 푸시하고 실행할 호스트로 끌어올 수 있습니다.

이 자습서의 나머지 부분에서는 Visual Studio Team Services를 사용하여 컨테이너를 Azure에서 실행되는 Service Fabric에 배포합니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기

응용 프로그램을 배포할 Service Fabric 클러스터가 이미 있는 경우 이 단계를 건너뛸 수 있습니다. 아직 없다면 계속해서 Service Fabric 클러스터를 만들어 보겠습니다.

>[!NOTE]
>다음 절차에서는 KeyVault에 있는 자체 서명된 인증서로 보호되는 단일 노드(단일 가상 컴퓨터) 미리 보기 Service Fabric 클러스터를 만듭니다.
>단일 노드 클러스터는 여러 가상 컴퓨터로 확장할 수 없으며 미리 보기 클러스터는 새로운 버전으로 업그레이드할 수 없습니다.
>Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기][link-azure-pricing-calculator]를 사용합니다.
>Service Fabric 클러스터 만들기에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기][link-servicefabric-create-secure-clusters] 문서를 참조하세요.

1. 이 GitHub 리포지토리에서 Azure Resource Manager 템플릿의 로컬 복사본과 매개 변수 파일을 다운로드합니다.
    * [Service Fabric용 Azure Resource Manager 템플릿][link-sf-clustertemplate]
       - **azuredeploy.json** - Service Fabric 클러스터를 정의하는 Azure Resource Manager 템플릿입니다.
       - **azuredeploy.parameters.json** - 클러스터 배포를 사용자 지정할 매개 변수 파일입니다.
2. 매개 변수 파일에서 다음 매개 변수를 사용자 지정합니다.
  
   | 매개 변수를 포함해야 합니다.       | 설명 | 제안 값 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | 클러스터를 배포할 Azure 지역입니다. | *예: westeurope, eastasia, eastus* |
   | clusterName     | 클러스터의 이름입니다. | *예: bobs-sfpreviewcluster* |
   | adminUserName   | 클러스터 가상 컴퓨터의 로컬 관리자 계정입니다. | *모든 유효한 Windows Server 사용자 이름* |
   | adminPassword   | 클러스터 가상 컴퓨터의 로컬 관리자 계정 암호입니다. | *모든 유효한 Windows Server 암호* |
   | clusterCodeVersion | 실행할 Service Fabric 버전입니다. (미리 보기 버전은 255.255.X.255) | **255.255.5713.255** |
   | vmInstanceCount | 클러스터의 가상 컴퓨터 수(1 또는 3-99일 수 있음)입니다. | **1** |

3. **PowerShell**을 엽니다.
4. Azure에 **로그인**합니다.

   ```powershell
   Login-AzureRmAccount
   ```

5. 클러스터를 배포할 **구독**을 선택합니다.

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. Service Fabric에서 사용된 인증서의 암호를 만들고 **암호화**합니다.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. 다음 명령을 실행하여 **클러스터를 만듭니다**.

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 매개 변수는 매개 변수 파일에 지정된 clusterName 매개 변수와 일치해야 하며, 도메인은 선택한 Azure 지역(예: `clustername.eastus.cloudapp.azure.com`)에 연결되어야 합니다.
   
    구성이 완료되면 Azure에서 생성된 클러스터에 대한 정보가 출력되고 인증서가 -CertificateOutputFolder 디렉터리에 복사됩니다.

8. 인증서 가져오기 마법사를 열려면 인증서를 **두 번 클릭**합니다.

9. 기본 설정을 사용하지만 **이 키를 내보낼 수 있도록 표시** 확인란을 **개인 키 보호** 단계에서 선택해야 합니다. 이 자습서의 뒷부분에 나오는 Azure Container Registry를 Service Fabric 클러스터 인증으로 구성할 때 Visual Studio에서 인증서를 내보내야 합니다.

10. 이제 브라우저에서 Service Fabric Explorer를 열 수 있습니다. URL은 PowerShell CmdLet의 출력에 있는 **ManagementEndpoint**(예: *https://mycluster.westeurope.cloudapp.azure.com:19080*)입니다. 

>[!NOTE]
>Service Fabric Explorer를 열면 인증서 오류가 표시되는데 이는 자체 서명된 인증서를 사용하고 있기 때문입니다. Edge에서는 *세부 정보*를 클릭한 다음 *웹 페이지로 이동* 링크를 클릭합니다. Chrome에서는 *고급* 및 *진행* 링크를 클릭합니다.

>[!NOTE]
>클러스터 작성이 실패한 경우 이미 배포된 리소스를 업데이트하는 명령을 언제든지 다시 실행할 수 있습니다. 실패한 배포의 일부로 인증서가 만들어진 경우 새 인증서가 생성됩니다. 클러스터 만들기 문제를 해결하려면 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기][link-servicefabric-create-secure-clusters] 문서를 참조하세요.

## <a name="getting-the-application-ready-for-the-cloud"></a>클라우드에 대해 응용 프로그램 준비

Azure의 Service Fabric에서 응용 프로그램을 실행할 수 있도록 준비하려면 다음 두 단계를 완료해야 합니다.

1. Service Fabric 클러스터에서 웹 응용 프로그램에 연결할 포트 노출
2. 응용 프로그램을 위해 프로덕션 준비가 된 SQL Database 제공

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1. Service Fabric에서 웹 응용 프로그램 노출
구성한 Service Fabric 클러스터는 기본적으로 Azure Load Balancer에서 포트 80을 열어 들어오는 트래픽과 클러스터의 균형을 조정합니다. docker-compose.yml 파일을 통해 이 포트에 컨테이너를 노출시킬 수 있습니다.

1. **솔루션 탐색기**를 엽니다.

2. **docker-compose** > **docker-compose.yml**을 엽니다.

3. `fabrikamfiber.web` 노드를 수정하고 `ports:`이라는 새 자식 노드와 문자열 `- "80:80"`을 추가합니다. 전체 docker-compose.yml 파일은 다음과 같아야 합니다.

   ```yml
      version: '3'

      services:
        fabrikamfiber.web:
          image: fabrikamfiber.web
          build:
            context: .\FabrikamFiber.Web
            dockerfile: Dockerfile
          ports:
            - "80:80"
   ```

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2. 응용 프로그램을 위해 프로덕션 준비가 된 SQL Database 제공
이 응용 프로그램을 컨테이너화하고 로컬 디버깅을 사용하도록 설정한 경우 컨테이너에서 SQL Server를 실행하도록 설정합니다. 이 방법은 응용 프로그램을 로컬에서 디버깅할 때 좋은 솔루션입니다. 데이터베이스의 데이터를 유지할 필요가 없기 때문입니다. 그러나 프로덕션 환경에서 실행하는 경우에는 데이터를 데이터베이스에 유지해야 합니다. 현재는 컨테이너에 데이터를 유지할 수 있는 방법이 없으므로 프로덕션 데이터를 컨테이너의 SQL Server에 저장할 수 없습니다.

결과적으로 서비스에 영구 SQL Database가 필요한 경우 Azure SQL Database를 사용하는 것이 좋습니다. Azure에서 관리되는 SQL Server를 설정하고 실행하려면 [Azure SQL Database 빠른 시작][[link-azure-sql]] 문서를 참조하세요.

>[!NOTE]
>FabrikamFiber.Web 프로젝트의 web.release.config 파일에서 연결 문자열을 SQL Server로 변경해야 합니다.
>SQL Database에 연결할 수 없는 경우 이 응용 프로그램은 정상적으로 실패합니다. SQL Server 없이 응용 프로그램을 배포할 수 있습니다.

## <a name="deploy-with-visual-studio"></a>Visual Studio를 사용하여 배포

Visual Studio Team Services를 사용하여 배포를 설정하려면 [Visual Studio 2017용 지속적인 업데이트 도구 확장][link-visualstudio-cd-extension]을 설치해야 합니다. Visual Studio Team Services를 구성하여 이 확장을 Azure에 쉽게 배포하고 Service Fabric 클러스터에 앱을 배포할 수 있습니다.

시작하려면 소스 제어에서 코드를 호스트해야 합니다. 이 섹션의 나머지 부분에서는 **git**을 사용 중이라고 가정합니다.

1. Visual Studio의 오른쪽 아래에서 **소스 제어에 추가** > **Git**(또는 원하는 옵션)를 클릭합니다.

   ![소스 제어 단추를 누릅니다.][image-source-control]

2. _팀 탐색기_ 창에서 **Git 리포지토리 게시**를 누릅니다.

3. VSTS 리포지토리 이름을 선택하고 **리포지토리**를 누릅니다.

   ![VSTS에 리포지토리 게시][image-publish-repo]

이제 코드가 VSTS 소스 리포지토리와 동기화되었으므로 연속 통합 및 지속적인 업데이트를 구성할 수 있습니다.

1. _솔루션 탐색기_에서 **솔루션** > **지속적인 업데이트 구성**을 마우스 오른쪽 단추로 클릭합니다.

2. Azure 구독을 선택합니다.

3. **호스트 유형**을 **Service Fabric 클러스터**로 설정합니다.

   >[!NOTE]
   >빌드하는 컨테이너 형식에 따라 Azure의 컨테이너에서 응용 프로그램을 호스트하는 옵션을 더 추가합니다. 

4. **대상 호스트**를 이전 섹션에서 만든 Service Fabric 클러스터로 설정합니다.

5. 컨테이너를 게시할 **컨테이너 레지스트리**를 선택합니다.

   >[!TIP]
   >**편집** 단추를 사용하여 컨테이너 레지스트리를 만듭니다.
    
6. **확인**을 누릅니다.

   ![Service Fabric 연속 통합 설정][image-setup-ci]
   
   구성이 완료되면 컨테이너가 Service Fabric에 배포됩니다. 리포지토리에 업데이트를 푸시할 때마다 새 빌드 및 릴리스가 실행됩니다.
   
   >[!NOTE]
   >컨테이너 이미지를 빌드하는 데는 약 15분이 소요됩니다.
   >Service Fabric 클러스터에 처음 배포하면 기본 Windows Server Core 컨테이너 이미지가 다운로드됩니다. 다운로드를 완료하는 데 5-10분이 더 소요됩니다.

7. 클러스터 URL(예: *http://mycluster.westeurope.cloudapp.azure.com*)을 사용하여 Fabrikam Call Center 응용 프로그램으로 이동합니다.

이제 Fabrikam Call Center 솔루션을 컨테이너화하고 배포했으므로 [Azure Portal][link-azure-portal]을 열고 응용 프로그램이 Service Fabric에서 실행되는 것을 확인할 수 있습니다. 응용 프로그램을 실행하려면 웹 브라우저를 열고 Service Fabric 클러스터의 URL로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Visual Studio의 컨테이너 도구][link-visualstudio-container-tools]
- [Service Fabric에서 컨테이너 시작][link-servicefabric-containers]
- [Service Fabric 응용 프로그램 만들기][link-servicefabric-createapp]

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
