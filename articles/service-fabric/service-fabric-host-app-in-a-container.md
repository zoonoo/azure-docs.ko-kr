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
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Azure Service Fabric에 컨테이너의 .NET 앱 배포

이 자습서에서는 Visual Studio 2017 Update 3 Preview를 사용하여 Windows 컨테이너에 기존 ASP.NET 응용 프로그램을 배포하는 방법을 보여 줍니다. 그런 다음 Visual Studio Team Services를 사용하여 Azure에 컨테이너를 배포하고 Service Fabric 클러스터에서 컨테이너를 호스트하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

1. Windows 10에서 컨테이너를 실행할 수 있도록 [Windows용 Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)를 설치합니다.
2. [Windows 10 컨테이너 빠른 시작][link-container-quickstart]을 숙지합니다.
3. 이 문서에서는 **Fabrikam Fiber**를 사용합니다. 이 샘플 앱은 [여기][link-fabrikam-github]서 다운로드할 수 있습니다.
4. [Azure PowerShell][link-azure-powershell-install]
5. [Visual Studio 2017용 지속적인 업데이트 도구 확장][link-visualstudio-cd-extension]

>[!NOTE]
>컴퓨터에서 Windows 컨테이너 이미지를 처음 실행하는 경우 Docker CE가 컨테이너에 대한 기본 이미지를 끌어와야 합니다. 이 자습서에서 사용되는 이미지의 크기는 14GB입니다. Powershell에서 다음 명령을 실행하여 기본 이미지를 끌어옵니다.
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>응용 프로그램 컨테이너화

컨테이너에서 응용 프로그램 실행을 시작하려면 Visual Studio에서 **Docker 지원**을 프로젝트에 추가해야 합니다. 응용 프로그램에 **Docker 지원**을 추가하면 두 가지 동작이 수행됩니다. 첫째, _docker_ 파일이 프로젝트에 추가됩니다. 이 새로운 파일은 컨테이너 이미지를 빌드하는 방법을 설명합니다. 둘째, 새로운 _docker-compose_ 프로젝트가 솔루션에 추가됩니다. 이 새로운 프로젝트에는 몇 개의 docker-compose 파일이 포함되어 있으며, 이 파일을 사용하여 컨테이너 실행 방법을 설명할 수 있습니다.

[Visual Studio 컨테이너 도구][link-visualstudio-container-tools] 작업에 대한 자세한 정보

### <a name="add-docker-support"></a>Docker 지원 추가

1. Visual Studio에서 **FabrikamFiber.CallCenter.sln** 파일을 엽니다.

2. **FabrikamFiber.Web** 프로젝트 > **추가** > **Docker 지원**을 마우스 오른쪽 단추로 클릭합니다.

### <a name="add-support-for-sql"></a>SQL 지원 추가

이 응용 프로그램은 SQL을 데이터 공급자로 사용하므로 응용 프로그램을 실행하려면 SQL Server가 필요합니다. 이 자습서에서는 컨테이너에서 실행 중인 SQL Server를 사용합니다.
컨테이너에서 SQL Server를 실행하도록 Docker에 지시하려면 docker-compose 프로젝트의 docker-compose.override.yml 파일에서 SQL Server 컨테이너 이미지를 참조할 수 있습니다. 그러면 Visual Studio에서 응용 프로그램을 디버그할 때 컨테이너에서 실행 중인 SQL Server가 사용됩니다.

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

   >[!NOTE]
   >항상 컨테이너에서 SQL Server를 실행하려면 docker-compose.override.yml 파일 대신 docker-compose.yml 파일에 앞의 코드를 추가할 수 있습니다.


4. `fabrikamfiber.web` 노드를 수정하고 `depends_on:`이라는 새 자식 노드를 추가합니다. 그러면 `db` 서비스(SQL Server 컨테이너)가 웹 응용 프로그램(fabrikamfiber.web)보다 먼저 시작됩니다.

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
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

이 자습서의 나머지 부분에서는 Visual Studio Team Services를 사용하여 컨테이너를 빌드 및 배포하고 Azure Container Registry로 푸시한 다음 Azure에서 실행 중인 Service Fabric에 배포합니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기

응용 프로그램을 배포할 Service Fabric 클러스터가 이미 있는 경우 이 단계를 건너뛸 수 있습니다. 아직 없다면 계속해서 Service Fabric 클러스터를 만들어 보겠습니다.

>[!NOTE]
>다음 절차에서는 자체 서명된 인증서로 보호된 Service Fabric 클러스터를 만듭니다. 이 클러스터는 배포 과정에서 생성되어 KeyVault에 배치됩니다. Azure Active Directory 인증을 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기][link-servicefabric-create-secure-clusters] 문서를 참조하세요.

1. 아래에 참조된 Azure 템플릿 및 매개 변수 파일의 로컬 복사본을 다운로드합니다.
    * [Service Fabric용 Azure Resource Manager 템플릿][link-sf-clustertemplate] - Service Fabric 클러스터를 정의하는 Resource Manager 템플릿입니다.
    * [템플릿 매개 변수 파일] [link-sf-clustertemplate-parameters] - 클러스터 배포를 사용자 지정할 매개 변수 파일입니다.
2. 매개 변수 파일에서 다음 매개 변수를 사용자 지정합니다.
  
   | 매개 변수를 포함해야 합니다.       | 설명 |
   | --------------- | ----------- |
   | clusterName     | 클러스터의 이름입니다. |
   | adminUserName   | 클러스터 가상 컴퓨터의 로컬 관리자 계정입니다. |
   | adminPassword   | 클러스터 가상 컴퓨터의 로컬 관리자 계정 암호입니다. |
   | clusterLocation | 클러스터를 배포할 Azure 지역입니다. |
   | vmInstanceCount | 클러스터의 노드 수입니다(1일 수 있음). |

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
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 매개 변수는 매개 변수 파일에 지정된 clusterName 매개 변수와 일치해야 하며, 도메인은 선택한 Azure 지역(예: `clustername.eastus.cloudapp.azure.com`)에 연결되어야 합니다.
   
    구성이 완료되면 Azure에서 생성된 클러스터에 대한 정보가 출력되고 인증서가 -CertificateOutputFolder 디렉터리에 복사됩니다.

8. 인증서를 **두 번 클릭**하여 로컬 컴퓨터에 설치합니다.

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
   
   구성이 완료되면 리포지토리에 업데이트를 푸시할 때마다 컨테이너가 Service Fabric에 배포됩니다.

7. **팀 탐색기**를 사용하여 **빌드를 시작**하고 해당 컨테이너 응용 프로그램이 Service Fabric에서 실행되는 것을 확인합니다.

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
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

