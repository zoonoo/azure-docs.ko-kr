<properties
   pageTitle="서비스 패브릭에 대한 연속 통합 | Microsoft Azure"
   description="VSTS(Visual Studio Team Services)를 사용한 서비스 패브릭 응용 프로그램에 대한 지속적인 통합 설정 방법의 개요를 확인합니다."
   services="service-fabric"
   documentationCenter="na"
   authors="mthalman-msft"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/25/2016"
   ms.author="mthalman" />

# Visual Studio Team Services를 사용한 서비스 패브릭 응용 프로그램에 대한 연속 통합 설정

이 문서에서는 자동화된 방식으로 응용 프로그램을 빌드, 패키지, 배포할 수 있도록 VSTS(Visual Studio Team Services)를 사용하여 Azure 서비스 패브릭 응용 프로그램에 대한 연속 통합을 설정하는 단계를 설명합니다.

이 문서는 최신 절차를 반영하며 시간이 지나면 변경될 수 있습니다.

## 필수 조건

시작하려면 이 단계를 따르세요.

1. Team Services 계정에 대한 액세스 권한이 있는지 확인하거나 직접 [계정을 만듭니다](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

2. Team Services 팀 프로젝트에 대한 액세스 권한이 있는지 확인하거나 직접 [프로젝트를 만듭니다](https://www.visualstudio.com/docs/setup-admin/create-team-project).

3. 응용 프로그램을 배포할 수 있는 서비스 패브릭 클러스터가 있는지 확인하거나 [Azure 포털](service-fabric-cluster-creation-via-portal.md), [Azure Resource Manager 템플릿](service-fabric-cluster-creation-via-arm.md) 또는 [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md)를 사용하여 만듭니다.

4. 서비스 패브릭 응용 프로그램(.sfproj) 프로젝트를 이미 만들었는지 확인합니다.

>[AZURE.NOTE] 사용자 지정 빌드 에이전트는 더 이상 필요하지 않습니다. Team Services 호스트 빌드 에이전트는 이제 서비스 패브릭 클러스터 관리 소프트웨어가 미리 설치된 상태로 제공되므로 해당 에이전트에서 직접 응용 프로그램을 배포할 수 있습니다.

## 소스 파일 구성 및 공유

가장 먼저 할 일은 Team Services 내에서 실행할 배포 프로세스에 사용할 게시 프로필을 준비하는 것입니다. 게시 프로필은 이전에 준비한 클러스터를 대상으로 지정하도록 구성해야 합니다.

1.	응용 프로그램 프로젝트 내에서 지속적인 통합 워크플로에 사용할 게시 프로필을 선택하고 원격 클러스터에 응용 프로그램을 게시하는 방법에 관한 [게시 지침](service-fabric-publish-app-remote-cluster.md)을 따릅니다. 그러나 실제로 응용 프로그램을 게시할 필요는 없습니다. 모든 정보를 올바르게 구성한 후 게시 대화 상자에서 **저장** 하이퍼링크를 클릭하면 됩니다.
2.	Team Services 내에서 이루어질 각 배포에 대해 응용 프로그램을 업그레이드하려면 업그레이드를 사용하도록 게시 프로필을 구성하는 것이 좋습니다. 1단계에 사용한 것과 같은 게시 대화 상자에서 **응용 프로그램 업그레이드** 확인란을 선택했는지 확인합니다. [추가 업그레이드 설정 구성](service-fabric-visualstudio-configure-upgrade.md)에 대해 더 자세히 알아봅니다. **저장** 하이퍼링크를 클릭하여 설정을 게시 프로필에 저장합니다.
3.	게시 프로필에 대한 변경 내용을 저장했는지 확인하고 게시 대화 상자를 취소합니다.
4.	이제 [응용 프로그램 프로젝트 소스 파일을 Team Services와 공유](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs)할 수 있습니다. 소스 파일을 Team Services에서 액세스할 수 있으면 이제 빌드 생성의 다음 단계로 이동할 수 있습니다.

## 빌드 정의 만들기

Team Services 빌드 정의는 순차적으로 실행되는 빌드 단계 집합으로 구성된 워크플로를 설명합니다. 만들려는 빌드 정의의 목표는 응용 프로그램을 최종적으로 클러스터에 배포하는 데 사용할 수 있는 서비스 패브릭 응용 프로그램 패키지를 몇몇 다른 보완 파일과 함께 생성하는 것입니다. Team Services [빌드 정의](https://www.visualstudio.com/docs/build/define/create)에 대해 자세히 알아봅니다.

### 정의 만들기

1.	Visual Studio Team Services에서 팀 프로젝트를 엽니다.
2.	**빌드** 탭을 선택합니다.
3.	녹색 **+** 기호를 선택하여 새 빌드 정의를 만듭니다.
4.	열리는 대화 상자에서 대화 상자의 아래쪽의 **비어 있음**을 선택합니다.
5.	**다음**을 선택합니다.
6.	서비스 패브릭 응용 프로그램과 연결된 리포지토리 및 분기를 선택합니다.
7.	사용하려는 에이전트 큐를 선택합니다. 호스트 에이전트가 지원됩니다.
8.	**만들기**를 선택합니다.
9.	**변수** 탭으로 전환한 후 다음 변수를 추가합니다.
   1. 이름: BuildConfiguration, 값: Release
   2. 이름: BuildPlatform, 값: x64
9. **빌드** 탭으로 다시 전환한 후 정의에 다음 빌드 단계를 추가합니다.
   1. NuGet Installer
   2. Visual Studio 빌드(이름: Build solution)
      1. 플랫폼: $(BuildPlatform)
      2. 구성: $(BuildConfiguration)
   3. Visual Studio 빌드(이름: Package application)
      1. 솔루션: **\\*.sfproj
      2. MSBuild 인수: /t:Package /p:PackageLocation=$(build.artifactstagingdirectory)\\applicationpackage
      3. 플랫폼: $(BuildPlatform)
      4. 구성: $(BuildConfiguration)
   4. 서비스 패브릭 앱 버전 업데이트
      1. 응용 프로그램 패키지: $(build.artifactstagingdirectory)\\applicationpackage
   5. 파일 복사
      1. 원본 폴더: $(build.sourcesdirectory)
      2. 콘텐츠: **\\PublishProfiles\\*.xml &lt;new line&gt; **\\ApplicationParameters\\*.xml
      3. 대상 폴더: $(build.artifactstagingdirectory)\\projectartifacts
   6. 빌드 아티팩트 게시
      1. 게시 경로: $(build.artifactstagingdirectory)
      2. 아티팩트 이름: drop
      3. 아티팩트 형식: Server
10. 빌드 정의를 저장하고 이름을 지정합니다.

### 작업의 기본 필드 값 확인

1.	**NuGet 복원** 및 **빌드 솔루션** 빌드 단계에 대한 **솔루션** 입력 필드를 확인합니다. 기본적으로 이러한 빌드 단계는 연결된 리포지토리에 포함된 모든 솔루션 파일에 대해 실행됩니다. 빌드 정의가 이러한 솔루션 파일 중 하나에서만 작동하도록 하려면 해당 파일에 대한 경로를 명시적으로 업데이트해야 합니다.
2.	**패키지 응용 프로그램** 빌드 단계에 대한 **솔루션** 입력 필드를 확인합니다. 기본적으로 이 빌드 단계는 리포지토리에 서비스 패브릭 응용 프로그램 프로젝트(.sfproj)가 한 개만 있다고 가정합니다. 리포지토리에 그러한 파일이 여러 개 있고 그 중 하나만을 이 빌드 정의에 대상으로 지정하려면 해당 파일에 대한 경로를 명시적으로 업데이트해야 합니다. 리포지토리 내에 응용 프로그램 프로젝트 여러 개를 패키징하려면 빌드 정의에 각각 응용 프로그램 프로젝트를 대상으로 지정하는 추가 **Visual Studio 빌드** 단계를 만들어야 합니다. 또한 패키지 위치가 각 단계에 대해 고유하도록 해당 빌드 단계 각각에 대해 **MSBuild 인수** 필드를 업데이트해야 할 수 있습니다.
3.	**서비스 패브릭 앱 버전 업데이트** 빌드 단계에 정의된 버전 관리 동작을 확인합니다. 기본적으로 이 빌드 단계는 응용 프로그램 패키지의 매니페스트 파일에 있는 모든 버전 값에 빌드 번호를 추가합니다. 자세한 내용은 [작업 설명서 페이지](https://go.microsoft.com/fwlink/?LinkId=820529)를 참조하세요. 이는 각 업그레이드 배포에 이전 배포와 다른 버전 값이 필요하므로 응용 프로그램의 업그레이드를 지원하는 데 유용합니다. 워크플로에 응용 프로그램 업그레이드를 사용하지 않으려는 경우 이 빌드 단계를 사용하지 않도록 설정할 수 있습니다.
4.	빌드 정의에 대해 실행한 변경 내용을 저장합니다.

### 시도

**큐 빌드**를 선택하여 수동으로 빌드를 시작합니다. 또한 빌드는 푸시 또는 체크인할 때 트리거됩니다. 빌드가 성공적으로 실행되고 있는지 확인했으면 이제 응용 프로그램을 클러스터에 배포할 릴리스 정의를 정의하는 작업으로 이동할 수 있습니다.

## 릴리스 정의 만들기

Team Services 릴리스 정의는 순차적으로 실행되는 작업 집합으로 구성된 워크플로를 설명합니다. 만들려는 릴리스 정의의 목표는 응용 프로그램 패키지를 가져와서 클러스터에 배포하는 것입니다. 빌드 정의와 릴리스 정의를 함께 사용할 경우 소스 파일로 시작하여 클러스터에서 실행 중인 응용 프로그램에서 종료할 때까지 전체 워크플로를 실행할 수 있습니다. Team Services [릴리스 정의](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)에 대해 자세히 알아봅니다.

### 정의 만들기

1.	Visual Studio Team Services에서 프로젝트를 엽니다.
2.	**릴리스** 탭을 선택합니다.
3.	녹색 **+** 기호를 선택하여 새 릴리스 정의를 만들고 메뉴에서 **릴리스 정의 만들기**를 선택합니다.
4.	열리는 대화 상자에서 대화 상자의 아래쪽의 **비어 있음**을 선택합니다.
5.	**다음**을 선택합니다.
6.	이 릴리스 정의의 원본으로 사용할 빌드 정의를 선택합니다. 릴리스 정의는 선택한 빌드 정의에 의해 생성된 아티팩트를 참조합니다.
7.	Team Services가 자동으로 새 릴리스를 만들고 빌드가 완료될 때마다 서비스 패브릭 응용 프로그램을 배포하게 하려면 **연속 배포** 확인란을 선택합니다.
8.	사용하려는 에이전트 큐를 선택합니다. 호스트 에이전트가 지원됩니다.
9.	**만들기**를 선택합니다.
10.	페이지 맨 위의 연필 아이콘을 클릭하여 정의 이름을 편집합니다.
11.	**작업 추가** 단추를 선택합니다.
12.	열리는 대화 상자에서 **배포** 범주 내에 있는 **서비스 패브릭 응용 프로그램 배포** 작업의 추가 단추를 클릭합니다.
13.	대화 상자를 닫습니다.
14.	클러스터에 응용 프로그램을 배포하는 데 사용하려는 게시 프로필의 경로를 작업의 **게시 프로필** 입력 필드에 정의합니다. 이 경로는 연결된 빌드 정의에 의해 생성된 게시 아티팩트에 포함된 파일을 참조합니다. 예제 경로는 $(system.defaultworkingdirectory)/MyBuildDefinition/drop/projectartifacts/Solution/AppProject/PublishProfiles/Cloud.xml입니다.
15.	클러스터에 배포할 응용 프로그램 패키지의 경로를 작업의 **응용 프로그램 패키지** 입력 필드에 정의합니다. 이 경로는 연결된 빌드 정의에 의해 생성된 게시 아티팩트에 포함된 위치를 참조합니다. 예제 경로는 $(system.defaultworkingdirectory)/MyBuildDefinition/drop/applicationpackage입니다.
16.	작업의 **클러스터 연결** 입력 필드에서 응용 프로그램을 배포해야 하는 클러스터를 선택합니다. 클러스터 연결은 배포 작업이 클러스터에 연결할 수 있도록 하는 데 필요한 정보를 제공합니다. 클러스터에 대한 클러스터 연결이 아직 없는 경우 추가할 필드 옆의 **관리** 하이퍼링크를 선택합니다. 이때 열리는 페이지에서 다음 단계를 수행합니다.
    1. **새 서비스 끝점**을 선택한 다음 메뉴에서 **Azure 서비스 패브릭**을 선택합니다.
    2. 이 끝점에서 대상으로 지정한 클러스터가 사용 중인 인증 유형을 선택합니다.
    2. **연결 이름** 필드에서 연결의 이름을 정의합니다. 일반적으로 클러스터의 이름을 사용합니다.
    3. **클러스터 끝점** 필드에서 클라이언트 연결 끝점 URL을 정의합니다. 예: https://contoso.westus.cloudapp.azure.com:19000.
    4. Azure Active Directory 자격 증명의 경우 **사용자 이름** 및 **암호** 필드에서 클러스터에 연결하는 데 사용할 자격 증명을 정의합니다.
    5. 인증서 기반 인증의 경우 **클라이언트 인증서** 필드에서 클라이언트 인증서 파일의 Base64 인코딩을 정의합니다. 이 값을 가져오는 방법에 대한 자세한 내용은 해당 필드에 대한 도움말 팝업을 참조하세요. 인증서가 암호로 보호된 경우 **암호** 필드에서 암호를 정의합니다.
    6. **확인**을 클릭하여 변경을 확인합니다. 릴리스 정의를 다시 탐색한 후 **클러스터 연결** 필드의 새로 고침 아이콘을 클릭하여 방금 추가한 끝점을 확인합니다.
17.	릴리스 정의를 저장합니다.

만들어지는 정의는 하나의 작업: **서비스 패브릭 응용 프로그램 배포**로 구성됩니다. 이 작업에 대한 자세한 내용은 [작업 설명서 페이지](https://go.microsoft.com/fwlink/?LinkId=820528)를 참조하세요.

### 시도

**릴리스** 단추 메뉴에서 **릴리스 만들기**를 선택하여 수동으로 릴리스를 만듭니다. 이때 열리는 대화 상자에서 릴리스의 기반으로 할 빌드를 선택하고 **만들기**를 클릭합니다. 연속 배포를 활성화한 경우 연결된 빌드 정의가 빌드를 완료할 때 릴리스도 자동으로 생성됩니다.

## 다음 단계

서비스 패브릭 응용 프로그램으로 연속 통합에 대한 자세한 내용은 다음 문서를 참조하세요.

 - [Team Services 문서 홈](https://www.visualstudio.com/docs/overview)
 - [Team Services에서 빌드 관리](https://www.visualstudio.com/docs/build/overview)
 - [Team Services에서 릴리스 관리](https://www.visualstudio.com/docs/release/overview)

<!---HONumber=AcomDC_0727_2016-->