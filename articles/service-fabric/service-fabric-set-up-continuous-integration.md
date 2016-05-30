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
   ms.date="03/29/2016"
   ms.author="mthalman" />

# Visual Studio Team Services를 사용한 서비스 패브릭 응용 프로그램에 대한 연속 통합 설정

이 문서에서는 자동화된 방식으로 응용 프로그램을 빌드, 패키지, 배포할 수 있도록 VSTS(Visual Studio Team Services)를 사용하여 Azure 서비스 패브릭 응용 프로그램에 대한 연속 통합을 설정하는 단계를 설명합니다. 이 지침에서는 매번 클러스터를 처음부터 다시 만듭니다.

이 문서는 최신 절차를 반영하며 시간이 지나면 변경될 수 있습니다.

## 필수 조건

시작하려면 Visual Studio Team Services에서 프로젝트를 설정합니다.

1. 아직 Team Services 계정이 없으면 [Microsoft 계정](http://www.microsoft.com/account)을 사용하여 계정을 설정합니다.

2. Microsoft 계정을 사용하여 Team Services에서 새 프로젝트를 만듭니다.

3. 새 또는 기존 서비스 패브릭 앱에 대한 소스를 이 프로젝트에 푸시합니다.

Team Services 프로젝트 작업에 대한 자세한 내용은 [Visual Studio에 연결](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)을 참조하세요.

## 서비스 주체 설정

### 자동화를 위한 인증 설정

빌드 컴퓨터를 설정하기 전에 빌드 에이전트가 Azure에 인증하는 데 사용하는 [서비스 주체](../resource-group-create-service-principal-portal.md)를 만들어야 합니다. 또한 Azure 주요 자격 증명 모음은 서비스 주체 인증을 지원하지 않으므로 인증서를 만들고 주요 자격 증명 모음에 업로드해야 합니다. 아무 컴퓨터에서나 다음 단계를 수행할 수 있습니다. 개발 컴퓨터를 사용하면 좋습니다.

### Azure PowerShell 설치 및 로그인

1.  PowerShellGet을 설치합니다.

    a. 최신 업데이트가 설치된 Windows 10을 실행 중인 경우 이 단계를 건너뛸 수 있습니다(PowerShellGet이 이미 설치됨).

    b. 그렇지 않은 경우 PowerShellGet을 포함하는 [Windows Management Framework 5.0](https://aka.ms/wmf5download)을 설치합니다.

2.	AzureRM 모듈을 설치하고 업데이트합니다. 이전 버전의 Azure PowerShell이 설치되어 있다면 제거합니다.

    a. 시작 단추를 마우스 오른쪽 단추로 클릭한 다음 **프로그램 추가/제거**를 선택합니다.

    b. "Azure PowerShell"을 검색하여 제거합니다.

    c. 관리자로 PowerShell 명령 프롬프트를 엽니다.

    d. `Install-Module AzureRM` 명령을 사용하여 AzureRM 모듈을 설치합니다.

3.	Azure 데이터 수집을 비활성화(또는 활성화)합니다.

    Azure cmdlet은 데이터 수집의 옵트인 여부를 묻는 메시지를 사용자가 선택할 때까지 표시합니다. 이 프롬프트에서는 사용자 입력을 대기하는 동안 자동화가 차단됩니다. 이 프롬프트를 표시하지 않으려면 다음 명령 중 하나를 실행하여 프롬프트를 표시하지 않도록 미리 선택합니다.

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Azure PowerShell에 로그인합니다.

    a. `Login-AzureRmAccount` 명령을 실행합니다.

    b. 나타나는 대화 상자에서 Azure 자격 증명을 입력합니다.

    c. `Get-AzureRmSubscription` 명령을 실행합니다.

    d. 사용하려는 구독을 찾습니다.

    e. `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>` 명령을 실행합니다.

### 서비스 주체 만들기

1. [이러한 지침](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)에 따라 프로젝트에 대한 서비스 주체 및 서비스 끝점을 만듭니다.

2. 스크립트 출력의 끝에 인쇄되는 값입니다. 빌드 정의를 설정하기 위해 필요합니다.

### 인증서를 만들어 새 Azure 주요 자격 증명 모음에 업로드합니다.

>[AZURE.NOTE] 이 예제에서는 스크립트에서 자체 서명된 인증서를 생성하지만 이 인증서는 안전하지 않기 때문에 실험용으로만 허용됩니다. 대신 합법적인 인증서를 얻으려면 조직의 지침을 따릅니다. 또한 이러한 지침은 서버와 클라이언트 모두에 단일 인증서를 사용합니다. 프로덕션에서 별도 서버 및 클라이언트 인증서를 사용해야 합니다.

1. [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6)을 이 컴퓨터의 폴더에 다운로드하고 압축을 풉니다.

2. 관리자 PowerShell 프롬프트에서 `<extracted zip>/Manual` 디렉터리로 변경합니다.

3. 다음 매개 변수로 PowerShell 스크립트 `CreateAndUpload-Certificate.ps1`을 실행합니다.

| 매개 변수 | 값 |
| --- | --- |
| KeyVaultLocation | 모든 값 이 매개 변수는 클러스터를 만들려는 위치와 일치해야 합니다. |
| CertificateSecretName | 모든 값 |
| CertificateDnsName | 클러스터의 DNS 이름과 일치해야 합니다. 예제: `mycluster.westus.cloudapp.azure.com` |
| SecureCertificatePassword | 모든 값 이 매개 변수는 빌드 컴퓨터에 인증서를 가져올 때 사용됩니다. |
| KeyVaultName | 모든 값 |
| KeyVaultResourceGroupName | 모든 값 클러스터에 사용하려는 리소스 그룹 이름은 사용하지 않습니다. |
| PfxFileOutputPath| 모든 값 이 파일은 빌드 컴퓨터에 인증서를 가져오는 데 사용됩니다. |

스크립트가 완료되면 다음의 세 값을 출력합니다. 이 값은 빌드 변수로 사용되므로 기록해 둡니다.

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## 빌드 컴퓨터 설정

### Visual Studio 2015 설치

이미 컴퓨터를 프로비전한 경우(또는 자체 제공할 계획인 경우) 선택한 컴퓨터에 [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)를 설치합니다.

컴퓨터가 아직 없는 경우 미리 설치된 Visual Studio 2015를 통해 Azure VM(가상 컴퓨터)을 신속하게 프로비전할 수 있습니다. 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 화면의 왼쪽 맨 위 모퉁이에서 **New** 명령을 선택합니다.

3. **마켓플레이스**를 선택합니다.

4. **Visual Studio 2015**를 검색합니다.

5. **계산** > **가상 컴퓨터** > **갤러리에서**를 선택합니다.

6. **Windows Server 2012 R2의 Azure SDK 2.9와 유니버설 Windows 도구가 있는 Visual Studio Enterprise 2015 업데이트 2** 이미지를 선택합니다.

    >[AZURE.NOTE] Azure SDK는 필수 구성 요소가 아니지만 현재 Visual Studio 2015만 설치하는 이미지는 제공되지 않습니다.

7.	대화 상자의 지침에 따라 VM을 만듭니다.

### 서비스 패브릭 SDK 설치

컴퓨터에 [서비스 패브릭 SDK](service-fabric-get-started.md#install-the-runtime-sdk-and-tools)를 설치합니다.

### Azure PowerShell 설치

Azure PowerShell을 설치하려면 이전 섹션 "Azure PowerShell 설치 및 로그인"의 단계를 따릅니다. "Azure PowerShell 로그인" 단계는 건너뜁니다.

### Azure PowerShell 모듈을 네트워크 서비스 계정에 등록

>[AZURE.NOTE] 빌드 에이전트를 시작하기 *전에* 이 작업을 수행합니다. 그러지 않으면 새 환경 변수가 선택되지 않습니다.

1. Windows 로고 키 + R을 누르고 **regedit**를 입력하고 Enter 키를 누릅니다.

2. `HKEY_Users\.Default\Environment` 노드를 마우스 오른쪽 단추로 클릭한 다음 **새로 만들기** > **확장 가능한 문자열 값**을 선택합니다.

3. 이름에 `PSModulePath`, 값에 `%PROGRAMFILES%\WindowsPowerShell\Modules`를 입력합니다. `%PROGRAMFILES%`를 `PROGRAMFILES` 환경 변수의 값으로 대체합니다.

### 자동화 인증서 가져오기

1.	빌드 컴퓨터에 인증서를 가져옵니다. 다음을 수행합니다.

    a. CreateAndUpload-Certificate.ps1 스크립트를 통해 만든 PFX 파일을 빌드 컴퓨터에 복사합니다.

    b. 관리자 PowerShell 프롬프트를 열고 앞서 `CreateAndUpload-Certificate.ps1`에 전달한 암호를 사용하여 다음 명령을 실행합니다.

    ```powershell
    $password = Read-Host -AsSecureString
    Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
    ```

2.	인증서 관리자를 실행합니다.

    a. Windows에서 제어판을 엽니다. 시작 단추를 마우스 오른쪽 단추로 클릭한 다음 **제어판**을 선택합니다.

    b. **인증서**를 검색합니다.

    c. **관리 도구** > **컴퓨터 인증서 관리**를 선택합니다.

3.	네트워크 서비스 계정에 자동화 인증서 사용을 위한 권한을 부여합니다.

    a. **인증서 - 로컬 컴퓨터**에서 **개인**을 확장한 다음 **인증서**를 선택합니다.

    b. 목록에서 해당하는 인증서를 찾습니다.

    c. 인증서를 마우스 오른쪽 단추로 클릭한 다음 **모든 작업** > **개인 키 관리**를 선택합니다.

    d. **추가** 단추를 선택하고 **네트워크 서비스**를 입력한 다음 **이름 확인**을 선택합니다.

    e. **확인**을 선택합니다.

    ![로컬 서비스 계정 권한 부여에 대한 단계의 스크린샷](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  `Trusted People` 폴더에 인증서를 복사합니다.

    a. 인증서를 **개인/인증서**에 가져오지만 **신뢰할 수 있는 사용자**에 추가해야 합니다. 인증서를 마우스 오른쪽 단추로 클릭하고 **복사**를 선택합니다. 그런 다음 **신뢰할 수 있는 사용자** 폴더를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

### 빌드 에이전트 등록

1.	agent.zip을 다운로드합니다. 다음을 수행합니다.

    a. 팀 프로젝트(예: ****https://[your-VSTS-account-name].visualstudio.com**)에 로그인합니다.

    b. 화면 오른쪽 위 모퉁이에서 기어 아이콘을 선택합니다.

    c. **에이전트 풀** 탭을 선택합니다.

    d. **에이전트 다운로드**를 선택하여 agent.zip 파일을 다운로드합니다.

    >[AZURE.NOTE] 다운로드가 시작되지 않으면 팝업 차단기를 확인합니다.

    e. 앞서 만든 빌드 컴퓨터에 agent.zip을 복사합니다.

    f. 빌드 컴퓨터에서 `C:\agent`(또는 짧은 경로의 위치)에 agent.zip의 압축을 풉니다.

    >[AZURE.NOTE] ASP.NET 5 웹 서비스를 사용할 계획이라면 배포 중 **PathTooLongExceptions** 오류가 발생하지 않게 이 폴더에 가능한 가장 짧은 이름을 선택하는 것이 좋습니다. ASP.NET Core가 릴리스되면 이 문제가 완화됩니다.

2.	관리자 명령 프롬프트에서 `C:\agent\ConfigureAgent.cmd`을 실행합니다. 스크립트가 다음 매개 변수를 입력하라는 메시지를 표시합니다.

|매개 변수|값|
|---|---|
|에이전트 이름|기본값(`Agent-[machine name]`)을 적용합니다.|
|TFS Url|팀 프로젝트에 대한 URL(예: `https://[your-VSTS-account-name].visualstudio.com`)을 입력합니다.|
|에이전트 풀|에이전트 풀의 이름을 입력합니다. 에이전트 풀을 만들지 않은 경우 기본 값을 적용합니다.|
|작업 폴더|기본값을 적용합니다. 빌드 에이전트가 실제로 응용 프로그램을 빌드하는 폴더입니다. ASP.NET 5 웹 서비스를 사용할 계획이라면 배포 중 PathTooLongExceptions 오류가 발생하지 않게 이 폴더에 가능한 가장 짧은 이름을 선택하는 것이 좋습니다.|
|Windows 서비스로 설치?|기본값은 N입니다. 이 값을 **Y**로 변경합니다.|
|서비스를 실행하는 사용자 계정|기본값은 `NT AUTHORITY\LOCAL SERVICE`입니다. 기본값을 `NT AUTHORITY\NetworkService`로 변경합니다.|
|`NT AUTHORITY\Network Service`에 대한 암호|네트워크 서비스 계정에는 암호가 없지만 빈 암호를 거부합니다. 암호에 비어 있지 않은 문자열을 입력합니다(무엇을 입력하든 무시됨).|
|기존 에이전트 구성 취소?|기본값(**N**)을 적용합니다.|

3.  자격 증명을 묻는 메시지가 표시될 경우 팀 프로젝트에 대한 권한이 있는 Microsoft 계정의 자격 증명을 입력합니다.

4.  빌드 에이전트를 등록했는지 확인하고 그 기능을 구성합니다. 다음을 수행합니다.

    a. 웹 브라우저로 돌아가(`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) 페이지를 새로 고칩니다.

    b. 이전에 ConfigureAgent.ps1을 실행할 때 선택한 에이전트 풀을 선택합니다.

    c. 빌드 에이전트가 목록에 있고 녹색 상태가 강조 표시되었는지 확인합니다. 빨강으로 강조 표시된 경우 빌드 에이전트가 Team Services에 연결하는 데 문제가 있는 것입니다.

    ![빌드 에이전트의 상태를 보여 주는 스크린샷](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d. 빌드 에이전트를 선택한 다음 **기능** 탭을 선택합니다.

    e. 값이 있는 **azureps**라는 기능을 추가합니다. VSTS가 제공한 빌드 태스크 중 일부를 사용하는 데 필요한 Azure PowerShell을 이 컴퓨터에 설치했음을 VSTS에 나타냅니다.


## 빌드 정의 만들기

>[AZURE.NOTE] 이 명령을 통해 만드는 빌드 정의는 다중 동시 빌드를 지원하지 않으며 별도의 컴퓨터에서도 마찬가지입니다. 각 빌드가 동일한 리소스 그룹/클러스터를 경쟁하게 되기 때문입니다. 여러 빌드 에이전트를 실행할 경우 이러한 간섭을 방지하기 위해 다음 명령/스크립트를 수정해야 합니다.

### 응용 프로그램에 서비스 패브릭 Azure Resource Manager 템플릿 추가

1. [이 샘플](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)에서 `azuredeploy.json` 및 `azuredeploy.parameters.json`을 다운로드합니다.

2. `azuredeploy.parameters.json`을 열고 다음 매개 변수를 편집합니다.

    |매개 변수|값|
    |---|---|
    |clusterLocation|사용자 키 자격 증명 모음의 위치와 일치해야 합니다. 예제: `westus`|
    |clusterName|인증서의 DNS 이름과 일치해야 합니다. 예를 들어 인증서의 DNS 이름이 `mycluster.westus.cloudapp.net`인 경우 `clusterName`은 `mycluster`여야 합니다.|
    |adminPassword|대문자, 소문자, 숫자, 특수 문자와 같은 문자 형식 중에서 3가지 이상의 문자를 사용한 8-123자입니다.|
    |certificateThumbprint|`CreateAndUpload-Certificate.ps1` 출력 파일에서|
    |sourceVaultValue|`CreateAndUpload-Certificate.ps1` 출력 파일에서|
    |certificateUrlvalue|`CreateAndUpload-Certificate.ps1` 출력 파일에서|

3. 소스 제어에 새 파일을 추가하고 VSTS에 푸시합니다.

### 빌드 정의 만들기

1.	빈 빌드 정의를 만듭니다. 다음을 수행합니다.

    a. Visual Studio Team Services에서 프로젝트를 엽니다.

    b. **빌드** 탭을 선택합니다.

    c. 녹색 **+** 기호를 선택하여 새 빌드 정의를 만듭니다.

    d. **비어 있음**을 선택하고 **다음**을 선택합니다.

    e. 오른쪽 리포지토리 및 분기가 선택되었는지 확인합니다.

    f. **연속 통합** 확인란을 선택하여 분기가 업데이트될 때마다 이 빌드가 트리거되는지 확인합니다.

    g. 빌드 에이전트를 등록한 에이전트 큐를 선택합니다.

2.	**변수** 탭에서 해당 값을 갖는 다음 변수를 만듭니다.

    |변수|값|Secret|큐 시간에 허용|
    |---|---|---|---|
    |BuildConfiguration|릴리스||X|
    |BuildPlatform|x64||||

3.  빌드 정의를 저장하고 이름을 지정합니다. 나중에 원하면 이 이름을 변경할 수 있습니다.

### "NuGet 패키지 복원" 단계를 추가합니다.

1. **빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.

2. **패키지** > **NuGet 설치 관리자**를 선택합니다.

3. 빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **NuGet 패키지 복원**으로 변경합니다.

4. **솔루션** 단추 옆의 **...** 버튼을 선택한 다음 .sln 파일을 선택합니다.

5. 빌드 정의를 저장합니다.

### "빌드" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.

2.	**빌드** > **MSBuild**를 선택합니다.

3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **빌드**로 변경합니다.

4. 다음 값을 선택합니다.

    |설정 이름|값|
    |---|---|
    |해결 방법|**...** 단추를 클릭하고 솔루션에 대한 `.sln` 파일을 선택합니다.|
    |플랫폼|`$(BuildPlatform)`|
    |구성|`$(BuildConfiguration)`|

5.	빌드 정의를 저장합니다.

### "패키지" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.

2.	**빌드** > **MSBuild**를 선택합니다.

3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **패키지**로 변경합니다.

4. 다음 값을 선택합니다.

    |설정 이름|값|
    |---|---|
    |해결 방법|**…** 단추를 클릭하고 응용 프로그램 프로젝트의 `.sfproj` 파일을 선택합니다.|
    |플랫폼|`$(BuildPlatform)`|
    |구성|`$(BuildConfiguration)`|
    |MSBuild 인수|`/t:Package`|

5.	빌드 정의를 저장합니다.

### <a name="RemoveClusterResourceGroup"></a> "클러스터 리소스 그룹 제거" 단계 추가

이전 빌드에서 자체 정리되지 않은 경우(예: 정리되기 전에 빌드가 취소됨) 새 리소스 그룹과 충돌하는 기존 리소스 그룹이 있을 수 있습니다. 충돌을 방지하기 위해 새 리소스 그룹을 만들기 전에 잔여 리소스 그룹(및 관련 리소스)을 모두 정리해야 합니다.

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.

2.	**배포** > **Azure 리소스 그룹 배포**를 선택합니다.

3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **클러스터 리소스 그룹 제거**로 변경합니다.

4. 다음 값을 선택합니다.

    |설정 이름|값|
    |---|---|
    |AzureConnectionType|**Azure 리소스 관리자**|
    |Azure RM 구독|**서비스 주체 만들기** 섹션에서 만든 연결 끝점을 선택합니다.|
    |작업|**리소스 그룹 삭제**|
    |리소스 그룹|사용되지 않은 이름을 입력합니다. 다음 단계에서 동일한 이름을 사용해야 합니다.|
    |오류 발생 시 계속|리소스 그룹이 없는 경우 이 단계가 실패합니다. 이러한 현상을 방지하려면 **제어 옵션** 섹션에서 **오류 발생 시 계속**을 설정합니다.|

5.	빌드 정의를 저장합니다.

### "보안 클러스터 프로비전" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.

2.	**배포** > **Azure 리소스 그룹 배포**를 선택합니다.

3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **프로비전 보안 클러스터**로 변경합니다.

4. 다음 값을 선택합니다.

    |설정 이름|값|
    |---|---|
    |AzureConnectionType|**Azure 리소스 관리자**|
    |Azure RM 구독|**서비스 주체 만들기** 섹션에서 만든 연결 끝점을 선택합니다.|
    |작업|**리소스 그룹 만들기 또는 업데이트**|
    |리소스 그룹|이전 단계에서 사용한 이름과 같아야 합니다.|
    |위치|사용자 키 자격 증명 모음의 위치와 일치해야 합니다.|
    |Template|**…** 단추를 클릭하고 `azuredeploy.json`를 선택합니다.|
    |템플릿 매개 변수|**…** 단추를 클릭하고 `azuredeploy.parameters.json`를 선택합니다.|

5.	빌드 정의를 저장합니다.

### "배포" 단계 추가

1.	**빌드** 탭에서 **빌드 단계 추가...** 명령을 선택합니다.

2.	**유틸리티** > **PowerShell**을 선택합니다.

3.	빌드 단계 이름 옆의 연필 아이콘을 선택하고 이름을 **배포**로 변경합니다.

4. 다음 값을 선택합니다.

    |설정 이름|값|
    |---|---|
    |형식|**File Path(파일 경로)**|
    |스크립트 파일 이름|**...** 단추를 클릭하여 응용 프로그램 프로젝트 내 **스크립트** 디렉터리로 이동합니다. `Deploy-FabricApplication.ps1`을 선택합니다.|
    |인수|`-PublishProfileFile path/to/MySolution/MyApplicationProject/PublishProfiles/MyPublishProfile.xml -ApplicationPackagePath path/to/MySolution/MyApplicationProject/pkg/$(BuildConfiguration)`|

5.	빌드 정의를 저장합니다.

### "확인" 단계 추가

1. 이 단계는 구성된 빌드 정의를 처음 가져올 때 선택 사항입니다. 하지만 빌드를 성공적으로 실행했고 다른 빌드 단계의 정확성을 확인했으면 여기에 사용자 고유의 확인 빌드 단계를 삽입할 수 있습니다. 이 내용은 응용 프로그램에 특정적이며 클러스터에 배포된 응용 프로그램의 정확성을 확인하기 위한 것입니다.
  
### 마지막 "정리" 단계 추가

1. ["클러스터 리소스 그룹 제거" 단계 추가](#RemoveClusterResourceGroup)의 동일한 지침을 따릅니다. 빌드 중에 만들어진 프로비전된 Azure 리소스를 모두 정리합니다.

### 시도

**빌드 큐 대기**를 선택하여 빌드를 시작합니다. 또한 빌드는 푸시 또는 체크인할 때 트리거됩니다.

## 대체 솔루션

위의 지침은 각각의 빌드에 대해 새 클러스터를 만들며 빌드 마지막에 해당 클러스터를 제거합니다. 그 대신 각 빌드가 응용 프로그램 업그레이드를 수행하게 하려면(기존 클러스터에 대해) 다음 단계를 수행합니다.

1.	[이러한 지침](service-fabric-cluster-creation-via-portal.md)을 따라 Azure 포털 또는 Azure PowerShell을 통해 수동으로 테스트 클러스터를 만듭니다.

2.	[이 지침](service-fabric-visualstudio-configure-upgrade.md)에 따라 응용 프로그램 업그레이드를 지원하는 게시 프로필을 구성합니다.

4.	빌드 정의에서 **클러스터 리소스 그룹 제거** 및 **프로비전 클러스터** 빌드 단계를 모두 제거합니다.

## 다음 단계

서비스 패브릭 응용 프로그램으로 연속 통합에 대한 자세한 내용은 다음 문서를 참조하세요.

 - [빌드 설명서 홈](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [빌드 에이전트 배포](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [빌드 정의 만들기 및 구성](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0518_2016-->