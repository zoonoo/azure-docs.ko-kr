<properties
   pageTitle="서비스 패브릭에 대한 연속 통합 | Microsoft Azure"
   description="VSO(Visual Studio Online)를 사용한 서비스 패브릭 응용 프로그램에 대한 연속 통합 설정 방법의 개요를 확인합니다."
   services="service-fabric"
   documentationCenter="na"
   authors="cawa"
   manager="paulyuk"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/16/2015"
   ms.author="cawa" />

# VSO(Visual Studio Online)를 사용한 서비스 패브릭 응용 프로그램에 대한 연속 통합 설정

이 문서에서는 자동화된 방식으로 응용 프로그램을 빌드, 패키지, 배포할 수 있게 VSO(Visual Studio Online)를 사용하여 서비스 패브릭 응용 프로그램에 대한 연속 통합(CI)을 설정하는 과정을 안내합니다. 이 문서는 현재 환경을 반영하며 개발 진행에 따라 변경될 수 있습니다. 또한 이 지침에서는 매번 클러스터를 처음부터 다시 만듭니다.

## 필수 조건

시작하려면 Visual Studio Online에서 프로젝트를 설정합니다.

1. 아직 계정이 없으면 [Microsoft 계정](http://www.microsoft.com/account)을 사용하여 VSO 계정을 만듭니다.
2. Microsoft 계정을 사용하여 VSO에서 새 프로젝트를 만듭니다.
3. 새 또는 기존 서비스 패브릭 앱에 대한 소스를 이 프로젝트에 푸시합니다.

VSO 프로젝트에 대한 자세한 내용은 [Visual Studio에 연결](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)을 참조하세요.

## 설정 단계

### 자동화를 위한 인증 설정

빌드 컴퓨터를 설정하려면 먼저 자동화가 Azure에 대한 인증에 사용할 "서비스 주체"를 만들어야 합니다. 또한 Azure 주요 자격 증명 모음은 서비스 주체 인증을 지원하지 않으므로 인증서를 만들어 주요 자격 증명 모음에 업로드해야 합니다. 아무 컴퓨터에서나 다음 단계를 수행할 수 있습니다. 개발 컴퓨터를 사용하면 좋습니다.

### Azure PowerShell 설치 및 로그인

1.	Azure PowerShell을 설치합니다.
    - PowerShellGet을 설치합니다. 이 작업을 수행하려면 PowerShellGet이 포함된 [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729)을 설치해야 합니다.
    >[AZURE.NOTE]최신 업데이트가 설치된 Windows 10을 실행 중인 경우 이 단계를 건너뛸 수 있습니다.

1.	AzureRM 모듈을 설치하고 업데이트합니다.
    1.  이전 버전의 Azure PowerShell이 설치되어 있다면 제거합니다. 시작 단추를 마우스 오른쪽 단추로 누르고 "프로그램 추가/제거"를 선택합니다. "Azure PowerShell"을 검색하여 제거합니다.
    1.  Azure PowerShell 명령 프롬프트를 실행합니다.
    1.	`Install-Module AzureRM` 명령을 사용하여 "AzureRM" 모듈을 설치합니다.
    1.	`Update-AzureRM` 명령을 사용하여 "AzureRM" 모듈을 업데이트합니다.
1.	Azure 데이터 수집을 비활성화(또는 활성화)합니다.

    Azure cmdlet은 데이터 수집의 옵트인 여부를 묻는 메시지를 사용자가 선택할 때까지 표시합니다. 이 프롬프트에서는 사용자 입력을 대기하는 동안 자동화가 차단됩니다. 미리 선택하여 이 프롬프트를 표시하지 않으려면 다음 명령 중 하나를 실행합니다.

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	Azure PowerShell에 로그인합니다.
    1. 관리자 PowerShell 프롬프트를 실행하고 `Login-AzureRMAccount` 명령을 실행합니다.
    1. 나타나는 대화 상자에서 Azure 자격 증명을 입력합니다.
    1. `Get-AzureRmSubscription` 명령을 실행합니다.
    1. 사용하려는 구독을 찾은 다음 `Select-AzureRmSubscription -SubscriptionId <id for your subscription>` 명령을 실행합니다.

### 서비스 주체 만들기

1.	이 컴퓨터의 폴더에서 [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773)의 압축을 풉니다.
1.	관리자 PowerShell 명령 프롬프트에서 ServiceFabricCIManualScripts.zip의 압축을 푼 디렉터리로 변경합니다.
1.	다음 명령을 사용하여 서비스 주체에 대한 암호를 선택합니다. 이 암호는 빌드 변수로 사용되므로 기억해 두어야 합니다.

    ```
    $password = Read-Host -AsSecureString
    ```
1.	다음 매개 변수로 PowerShell 스크립트 Create-ServicePrincipal.ps1를 실행합니다.

    |매개 변수|값|
    |---|---|
    |ServicePrincipalDisplayName|임의의 이름입니다.|
    |ServicePrincipalHomePage|모든 URI입니다. 실제로 존재할 필요는 없습니다.|
    |ServicePrincipalIdentifierUri|모든 고유 URI입니다. 실제로 존재할 필요는 없습니다.|
    |ServicePrincipalSecurePassword|$password|

    스크립트가 완료되면 다음의 세 값을 출력합니다. 이 값은 빌드 변수로 사용되므로 기록해 둡니다.

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### 인증서를 만들어 새 Azure 주요 자격 증명 모음에 업로드합니다.

1.	관리자 PowerShell 프롬프트에서 ServiceFabricCIManualScripts.zip의 압축을 푼 디렉터리로 변경합니다.
1.	다음 매개 변수로 PowerShell 스크립트 CreateAndUpload-Certificate.ps1를 실행합니다.

    |매개 변수|값|
    |---|---|
    |ServiceFabricKeyVaultLocation|모든 값 클러스터를 만들려는 위치와 일치해야 합니다.|
    |ServiceFabricCertificateSecretName|모든 값|
    |ServiceFabricSecureCertificatePassword|모든 값. 빌드 컴퓨터에 인증서를 가져올 때 사용됩니다.|
    |ServiceFabricKeyVaultResourceGroupName|모든 값 클러스터에 사용하려는 리소스 그룹 이름은 사용하지 않습니다.|
    |ServiceFabricKeyVaultName|모든 값|
    |ServiceFabricPfxFileOutputPath|모든 값 이 파일은 빌드 컴퓨터에 인증서를 가져오는 데 사용됩니다.|

    스크립트가 완료되면 다음의 세 값을 출력합니다. 이 값은 빌드 변수로 사용되므로 기록해 둡니다.

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## 빌드 컴퓨터 설정

이 명령을 통해 만드는 빌드 정의는병렬 빌드를 지원하지 않으며, 별도의 컴퓨터에서라도 마찬가지입니다. 각 빌드가 동일한 리소스 그룹/클러스터를 경쟁하게 되기 때문입니다. 여러 빌드 에이전트를 실행할 경우 이러한 간섭을 방지하기 위해 다음 명령/스크립트를 수정해야 합니다.

### Visual Studio 2015 설치

1.	이미 컴퓨터를 프로비전한 경우(또는 자체 제공할 계획인 경우) [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)를 해당 컴퓨터에 설치합니다.
2.	컴퓨터가 아직 없는 경우 미리 설치된 Visual Studio 2015를 통해 Azure VM(가상 컴퓨터)를 신속하게 프로비전할 수 있습니다. 다음을 수행합니다.
    1.	[Azure 관리 포털](http://portal.azure.com)에 로그인합니다.
    1.	화면의 왼쪽 맨 위 모퉁이에서 **New** 명령을 선택합니다.
    1.	**마켓플레이스**를 선택합니다.
    1.	**Visual Studio 2015**를 검색합니다.
    1.	**계산** > **가상 컴퓨터** > **갤러리에서**를 선택합니다.
    1.	**Visual Studio Enterprise 2015 With Azure SDK 2.7 on Windows Server 2012 R2** 이미지를 선택합니다.

        >[AZURE.NOTE]Azure SDK는 필수 구성 요소가 아니지만 Visual Studio 2015만 설치하는 이미지는 제공되지 않습니다.

    1.	대화 상자의 지침에 따라 VM을 만듭니다. 최적의 디스크 및 CPU 성능을 위해서는 D 시리즈를 선택하는 것이 좋습니다.

### 서비스 패브릭 SDK 설치

[서비스 패브릭 SDK](https://azure.microsoft.com/campaigns/service-fabric/)를 설치합니다.

### 서비스 패브릭 SDK의 NuGet 리포지토리 등록

1.	빌드 컴퓨터에 `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` 디렉터리를 만듭니다(아직 없는 경우).
2.	NuGet.config 파일이 이미 이 디렉터리에 있으면 해당 파일을 열고 다음 요소를 `<packageSources>`의 자식으로 추가합니다.

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	NuGet.config가 아직 없는 경우 다음과 같은 내용으로 만듭니다. `<path to service fabric SDK>`를 빌드 컴퓨터의 서비스 패브릭 SDK 경로로 대체합니다.
>[AZURE.NOTE]`<path to service fabric SDK>`의 기본값은 `%ProgramFiles%\Microsoft SDKs\Service Fabric`입니다.

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Azure PowerShell 설치

Azure PowerShell을 설치하려면 이전 섹션 **Azure PowerShell 설치 및 로그인**의 단계를 따릅니다. **Azure PowerShell 로그인** 하위 섹션은 건너뜁니다.

### Azure PowerShell 모듈을 로컬 서비스 계정에 등록

1. Win + R을 누른 다음 **regedit**를 입력하고 Enter를 누릅니다.
2. `HKEY_Users\.Default\Environment` 노드를 마우스 오른쪽 단추로 클릭하고 **새로 만들기 > 확장 가능한 문자열 값**을 선택합니다.
3. 이름에 `PSModulePath`, 값에 `%PROGRAMFILES%\WindowsPowerShell\Modules`를 입력합니다.

>[AZURE.NOTE]빌드 에이전트를 시작하기 *전에* 이 작업을 수행해야 합니다. 그렇지 않으면 새 환경 변수를 선택하지 않습니다.

### 자동화 인증서 가져오기

1.	빌드 컴퓨터에 인증서를 가져옵니다. 다음을 수행합니다.
    1. CreateAndUpload-Certificate.ps1 스크립트를 통해 만든 PFX 파일을 빌드 컴퓨터에 복사합니다.
    1. 관리 PowerShell 계정을 열고 앞서 GenerateCertificate.ps1에 전달한 암호를 사용하여 다음 명령을 실행합니다.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	인증서 관리자를 실행합니다.
    1. Windows 제어판을 엽니다. 시작 단추를 마우스 오른쪽 단추로 클릭하고 **제어판**을 선택합니다.
    1. **인증서**를 검색합니다.
    1. **관리 도구** > **컴퓨터 인증서 관리**를 선택합니다.


1.	로컬 서비스 계정에 자동화 인증서 사용을 위한 권한을 부여합니다.
    1.	**인증서 - 로컬 컴퓨터**에서 **개인**을 확장한 다음 **인증서**를 선택합니다.
    1.	목록에서 해당하는 인증서를 찾습니다.
    1.	인증서를 마우스 오른쪽 단추로 클릭한 다음 **모든 작업** > **개인 키 관리**를 선택합니다.
    1.	**추가** 단추를 선택하고 **로컬 서비스**를 입력한 다음 **이름 확인**을 선택합니다.
    1.	**확인** 단추를 선택한 다음 인증서 관리자를 닫습니다.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### 빌드 에이전트 등록

1.	agent.zip을 다운로드합니다. 다음을 수행합니다.
    1.	팀 프로젝트에 로그인합니다(예: ****https://[your-VSO-account-name].visualstudio.com**).
1.	화면 오른쪽 위 모퉁이에서 '기어' 아이콘을 선택합니다.
    1.	제어판에서 **에이전트 풀** 탭을 선택합니다.
    1.	**에이전트 다운로드**를 선택하고 agent.zip 파일을 다운로드합니다.
    1.	앞서 만든 빌드 컴퓨터에 agent.zip을 복사합니다.
    1.	빌드 컴퓨터에서 `C:\agent`(또는 짧은 경로의 위치)에 agent.zip의 압축을 풉니다.

        >[AZURE.NOTE]ASP.NET 5 웹 서비스 구축을 계획 중이라면 배포 중 **PathTooLongExceptions** 오류가 발생하지 않게 이 폴더에 가능한 가장 짧은 이름을 선택하는 것이 좋습니다.

1.	관리자 PowerShell 명령 프롬프트에서 `C:\agent\ConfigureAgent.ps1`을 실행합니다. 스크립트가 다음 매개 변수를 입력하라는 메시지를 표시합니다.

    |매개 변수|값|
    |---|---|
    |에이전트 이름|기본값(`Agent-[machine name]`)을 적용합니다.
    |TFS Url|팀 프로젝트 URL을 입력합니다(예: `https://[your-VSO-account-name].visualstudio.com`).
    |에이전트 풀|에이전트 풀의 이름을 입력합니다. 에이전트 풀을 만들지 않은 경우 기본 값을 적용합니다.|
    |작업 폴더|기본값을 적용합니다. 빌드 에이전트가 실제로 응용 프로그램을 빌드하는 폴더입니다. 참고: ASP.NET 5 웹 서비스 구축을 계획 중이라면 배포 중 PathTooLongExceptions 오류가 발생하지 않게 이 폴더에 가능한 가장 짧은 이름을 선택하는 것이 좋습니다.|
    |Windows 서비스로 설치?|기본값은 N입니다. 이 값을 Y로 변경합니다.|
    |서비스를 실행하는 사용자 계정|기본값(`NT AUTHORITY\LocalService)`)을 적용합니다.|
    |기존 에이전트 구성 취소?|기본값(**N**)을 적용합니다.|

1. 자격 증명을 입력하라는 메시지가 표시됩니다. 팀 프로젝트에 대한 권한이 있는 Microsoft 계정의 자격 증명을 입력합니다.
1. 빌드 에이전트가 등록된 것을 확인합니다. 다음을 수행합니다.

    1. 웹 브라우저로 돌아가(`https://[your-VSO-account-name].visualstudio.com/_admin/_AgentPool` 페이지에 있어야 함) 페이지를 새로 고칩니다.
    1. 앞서 ConfigureAgent.ps1을 실행할 때 선택한 에이전트 풀을 선택합니다.
    1. 빌드 에이전트가 목록에 있고 녹색 상태가 강조 표시되었는지 확인합니다. 빨강으로 강조 표시된 경우 빌드 에이전트가 VSO에 연결하는 데 문제가 있는 것입니다.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## 빌드 정의 설정

### 연속 통합 스크립트를 응용 프로그램에 대한 소스 제어에 추가합니다.

1.	소스 제어에서 임의의 폴더에 [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775)의 압축을 풉니다.
1.	결과 파일을 체크인합니다.

### 빌드 정의 만들기

1.	빈 빌드 정의를 만듭니다. 다음을 수행합니다.
    1.	Visual Studio Online에서 프로젝트를 엽니다.
    1.	**빌드** 탭을 선택합니다.
    1.	녹색 **+** 기호를 선택하여 새 빌드 정의를 만듭니다.
    1.	**비어 있음**을 선택하고 **다음** 단추를 선택합니다.
    1.  오른쪽 리포지토리 및 분기가 선택되었는지 확인합니다.
    1.  빌드 에이전트를 등록한 에이전트 대기열을 선택하고 **연속 통합** 확인란을 선택합니다.
1.	**변수** 탭에서 해당 값을 갖는 다음 변수를 만듭니다.

    |변수|값|Secret|큐 시간에 허용|
    |---|---|---|---|
    |BuildConfiguration|릴리스||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|CreateServicePrincipal.ps1에 전달한 암호|X||
    |ServicePrincipalId|CreateServicePrincipal.ps1의 출력에 있음|||
    |ServicePrincipalTenantId|CreateServicePrincipal.ps1의 출력에 있음|||
    |ServicePrincipalSubscriptionId|CreateServicePrincipal.ps1의 출력에 있음|||
    |ServiceFabricCertificateThumbprint|GenerateCertificate.ps1의 출력에 있음|||
    |ServiceFabricKeyVaultId|GenerateCertificate.ps1의 출력에 있음|||
    |ServiceFabricCertificateSecretId|GenerateCertificate.ps1의 출력에 있음|||
    |ServiceFabricClusterResourceGroupName|임의의 이름|||
    |ServiceFabricClusterName|임의의 이름|||
    |ServiceFabricClusterLocation|사용자 키 자격 증명 모음의 위치와 일치하는 임의의 이름|||
    |ServiceFabricClusterAdminPassword|임의의 이름|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>`참고: 게시 프로필에서의 연결 끝점은 무시됩니다. 대신 임시 클러스터에 대한 연결 끝점을 사용합니다.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` .sfproj 파일이 들어 있는 폴더여야 합니다.||||

1.	**트리거** 탭에서 **연속 통합** 및 **일괄 변경** 옵션을 선택합니다.
1.	**일반** 탭에서 빌드 에이전트를 등록한 큐를 선택합니다.
1.	빌드 정의를 저장하고 이름을 지정합니다. 나중에 원하면 이 이름을 변경할 수 있습니다.

### "빌드" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.
2.	**빌드** > **MSBuild**를 선택합니다.
3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **빌드**로 변경합니다.
4.	**솔루션** 단추 옆의 **...** 버튼을 선택한 다음 .sln 파일을 선택합니다.
5.	**플랫폼**에 `$(BuildPlatform)`을 입력합니다.
6.	**구성**에 `$(BuildConfiguration)`을 입력합니다.
7.	**NuGet 패키지 복원** 확인란을 선택합니다(아직 선택하지 않은 경우).
8.	빌드 정의를 저장합니다.

### "패키지" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.
2.	**빌드** > **MSBuild**를 선택합니다.
3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **패키지**로 변경합니다.
4.	**솔루션** 필드 옆의 **…** 버튼을 선택한 다음 응용 프로그램 프로젝트의 .sfproj 파일을 선택합니다.
5.	**플랫폼**에 `$(BuildPlatform)`을 입력합니다.
6.	**구성**에 `$(BuildConfiguration)`을 입력합니다.
7.	**MSBuild 인수**에 `/t:Package`를 입력합니다.
8.	**NuGet 패키지 복원** 확인란을 선택 취소합니다(아직 취소하지 않은 경우).
9.	빌드 정의를 저장합니다.

### "클러스터 리소스 그룹 제거" 단계추가

이전 빌드에서 자체 정리되지 않은 경우(예: 정리되기 전에 빌드가 취소됨) 새 리소스 그룹과 충돌하는 기존 리소스 그룹이 있을 수 있습니다. 충돌을 방지하기 위해 새 리소스 그룹을 만들기 전에 잔여 리소스 그룹(및 관련 리소스)을 모두 정리해야 합니다.

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.
2.	**유틸리티** > **PowerShell**을 선택합니다.
3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **클러스터 리스소 그룹 제거**로 변경합니다.
4.	**스크립트 파일 이름** 옆의 **...** 명령을 선택합니다. 자동화 스크립트의 압축을 푼 위치로 이동한 다음 **Remove-ClusterResourceGroup.ps1**을 선택합니다.
5.	**인수**에 `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`를 입력합니다.
6.	빌드 정의를 저장합니다.

### "클러스터 보호를 위한 프로비전 및 배포" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.
2.	**유틸리티** > **PowerShell**을 선택합니다.
3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **클러스터 보호를 위한 프로비전 및 배포**로 변경합니다.
4.	**스크립트 파일 이름** 옆의 **...** 버튼을 선택합니다. 자동화 스크립트의 압축을 푼 위치로 이동한 다음 **ProvisionAndDeploy-SecureCluster.ps1**을 선택합니다.
5.	"인수"에 `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`를 입력합니다.
6.	빌드 정의를 저장합니다.

### "클러스터 리소스 그룹 제거" 단계추가

임시 클러스터를 마쳤으므로 이제 정리해야 합니다. 정리하지 않으면 임시 클러스터에 대한 비용이 계속 청구됩니다. 이 단계에서는 리소스 그룹을 제거하여 클러스터와 그룹 내 모든 다른 리소스를 제거합니다.

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.
1.	**유틸리티** > **PowerShell**을 선택합니다.
1.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **클러스터 리스소 그룹 제거**로 변경합니다.
1.	**스크립트 파일 이름** 옆의 **...** 버튼을 선택합니다. 자동화 스크립트의 압축을 푼 위치로 이동한 다음 **RemoveClusterResourceGroup.ps1**을 선택합니다.
1.	"인수"에 `-ServicePrincipalPassword "$(ServicePrincipalPassword)`를 입력합니다.
1.	**제어 옵션**에서 **항상 실행** 확인란을 선택합니다.
1.	빌드 정의를 저장합니다.

### 사용해 보세요.

**큐 빌드** 명령을 선택하여 빌드를 시작합니다. 체크인해도 빌드가 트리거됩니다.


## 대체 솔루션

위의 지침은 각각의 빌드에 대해 새 클러스터를 만들며 빌드 마지막에 해당 클러스터를 제거합니다. 그 대신 각 빌드가 응용 프로그램 업그레이드를 수행하게 하려면(기존 클러스터에 대해) 다음 단계를 수행합니다.

1.	Azure 관리 포털 또는 Azure PowerShell을 통해 수동으로 테스트 클러스터를 만듭니다. "ProvisionAndDeploy SecureCluster.ps1" 스크립트를 참고로 사용할 수 있습니다.
1.	[이 지침](service-fabric-visualstudio-configure-upgrade.md)에 따라 응용 프로그램 업그레이드를 지원하는 게시 프로필을 구성합니다.

1.	**클러스터 보호를 위한 프로비전 및 배포** 단계를 Deploy-FabricApplication.ps1을 직접 호출하는 단계로 대체합니다(게시 프로필로 전달).
1.	빌드 정의에서 **클러스터 리소스 그룹 제거** 빌드 단계를 모두 제거합니다.

## 다음 단계

서비스 패브릭 응용 프로그램과의 연속 통합에 대한 자세한 내용은 다음 문서에서 확인합니다. [빌드 설명서 홈](https://msdn.microsoft.com/Library/vs/alm/Build/overview) - [빌드 에이전트 배포](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows) - [빌드 정의 만들기 및 구성](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_1203_2015-->