<properties
	pageTitle="Azure 진단 버전 기록"
	description="여러 Microsoft Azure SDK 버전과 함께 제공된 여러 버전의 Azure 진단에 대한 변경 내용을 설명합니다."
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="robb"/>


# Microsoft Azure 진단 버전 기록

Azure 진단을 처음 사용하나요? [Azure 진단 개요](azure-diagnostics.md)를 참조하세요.

Azure SDK의 각 버전은 일반적으로 새 버전의 Azure 진단과 함께 제공됩니다. 아래 표에서 SDK 릴리스와 관련된 Azure SDK 및 Azure 진단 버전을 설명합니다.



Azure SDK 버전 | Azure 진단 버전 | 모델
--- | --- | ---
1\.x | 1\.0 | 플러그 인
2\.0~2.4| 1\.0 | "
2\.5 | 1\.2 | 확장
2\.6 | 1\.3 | "
2\.7 | 1\.4 | "
2\.8 | 1\.5 | "


최신 버전은 1.5이며, Azure SDK 2.8과 함께 제공됩니다. SDK와 함께 제공되는 Azure 진단 확장 버전은 로컬 에뮬레이터 시나리오에만 사용됩니다. 배포된 모든 응용 프로그램은 Azure에서 실행할 때 응용 프로그램을 빌드한 SDK의 버전과 관계없이 자동으로 최신 버전을 사용합니다. 그러나 최신 Azure SDK를 설치하지 않으면 새로운 기능을 활용하는 데 도움이 되는 모든 도구를 사용하지 못할 수 있습니다.

다양한 기능 및 변경 내용은 아래에서 설명합니다.

## Azure SDK 2.8
Azure SDK 2.8에는 [Application Insights](./application-insights/app-insights-cloudservices.md)에 진단 데이터를 전송하는 기능이 추가되어 시스템 및 인프라 수준뿐만 아니라 응용 프로그램에서 문제를 쉽게 진단할 수 있습니다.

## Azure 2.6 진단 변경

Visual Studio에서 Azure SDK 2.6 클라우드 서비스 프로젝트의 경우 다음 사항이 변경되었습니다. (이러한 변경 사항은 이후 버전의 Azure SDK에도 적용됩니다.)

- 이제 로컬 에뮬레이터에서 진단을 지원합니다. 따라서 Visual Studio에서 개발 및 테스트하는 동안 진단 데이터를 수집하고 응용 프로그램에서 올바른 추적을 생성하고 있는지 확인할 수 있습니다. 연결 문자열 `UseDevelopmentStorage=true`는 Azure 저장소 에뮬레이터를 사용하여 Visual Studio에서 클라우드 서비스 프로젝트를 실행하는 동안 진단 데이터 수집을 사용하도록 설정합니다. 모든 진단 데이터는 (개발 저장소) 저장소 계정에 수집됩니다.

- 진단 저장소 계정 연결 문자열(Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString)은 서비스 구성(.cscfg) 파일에 다시 저장됩니다. Azure SDK 2.5에서는 진단 저장소 계정이 diagnostics.wadcfgx 파일에 지정되었습니다.

Azure SDK 2.4 이하와 Azure SDK 2.6 이상 버전에서 연결 문자열이 작동하는 방식 간에 몇 가지 주목할 만한 차이점이 있습니다.

- Azure SDK 2.4 이하 버전에서는 진단 로그를 전송하기 위한 저장소 계정 정보를 가져오기 위해 진단 플러그인에 의해 런타임으로 연결 문자열이 사용되었습니다.

- Azure SDK 2.6 이상 버전에서는 게시하는 동안 적절한 저장소 계정 정보로 진단 확장을 구성하기 위해 Visual Studio에 의해 연결 문자열이 사용됩니다. 연결 문자열을 통해 사용자는 Visual Studio에서 게시하는 동안 사용할 다양한 서비스 구성에 대해 서로 다른 저장소 계정을 정의할 수 있습니다. 그러나 진단 플러그인을 더 이상 사용할 수 없으므로(Azure SDK 2.5 이후) .cscfg 파일 자체만으로 진단 확장을 활성화할 수 없습니다. Visual Studio 또는 PowerShell과 같은 도구를 통해 별도로 확장을 사용하도록 설정해야 합니다.

- PowerShell을 사용한 진단 확장 구성 프로세스를 단순화하기 위해 Visual Studio의 패키지 출력에도 각 역할에 대한 진단 확장의 공용 구성 XML이 포함됩니다. Visual Studio에서는 진단 연결 문자열을 사용하여 공용 구성에 있는 저장소 계정 정보를 채웁니다. 공용 config 파일이 Extensions 폴더에서 생성되고 PaaSDiagnostics.<RoleName>.PubConfig.xml 패턴을 따릅니다. 모든 PowerShell 기반 배포에서 이 패턴을 사용하여 각 구성을 역할에 매핑합니다.

- .cscfg 파일의 연결 문자열은 Azure 포털에서 진단 데이터에 액세스하는 데도 사용되므로 **모니터링** 탭에 나타날 수 있습니다. 이 연결 문자열은 포털에서 자세한 모니터링 데이터를 표시하도록 서비스를 구성하는 데 필요 합니다.

### Azure SDK 2.6 이상으로 프로젝트 마이그레이션

Azure SDK 2.5에서 Azure SDK 2.6 이상으로 마이그레이션하는 경우 .wadcfgx 파일에 진단 저장소 계정이 지정된 경우 값이 계속 유지됩니다. 서로 다른 저장소 구성에 대해 서로 다른 저장소 계정을 유연성 있게 사용하려면 프로젝트에 연결 문자열을 수동으로 추가해야 합니다. Azure SDK 2.4 또는 이전 버전에서 Azure SDK 2.6으로 프로젝트를 마이그레이션하는 경우 진단 연결 문자열이 유지됩니다. 그러나 이전 섹션에서 지정된 것처럼 Azure SDK 2.6에서 연결 문자열을 처리하는 방식이 변경되었음을 유의하세요.

### Visual Studio에서 진단 저장소 계정을 결정하는 방법

- 진단 연결 문자열이.cscfg 파일에 지정된 경우 Visual Studio에서는 게시할 때와 패키징 중 공용 구성 xml 파일을 생성할 때 이 문자열을 사용하여 진단 확장을 구성합니다.

- 진단 연결 문자열이.cscfg 파일에 지정되지 않은 경우 Visual Studio에서는 게시할 때와 패키징 중 공용 구성 xml 파일을 생성할 때 .wadcfgx 파일에 지정된 저장소 계정을 대신 사용하여 진단 확장을 구성합니다.

- .cscfg 파일의 진단 연결 문자열은 .wadcfgx 파일의 저장소 계정보다 우선합니다. 진단 연결 문자열이.cscfg 파일에 지정된 경우, Visual Studio에서는 이 문자열을 사용하고 .wadcfgx의 저장소 계정은 무시합니다.

### "개발 저장소 연결 문자열 업데이트..." 확인란의 기능은 무엇입니까?

**Microsoft Azure에 게시할 때 Microsoft Azure 저장소 계정 자격 증명을 사용하여 진단 및 캐싱을 위한 개발 저장소 연결 문자열 업데이트** 확인란은 게시하는 동안 지정된 Azure 저장소 계정으로 개발 저장소 계정 연결 문자열을 업데이트하는 편리한 방법을 제공합니다.

예를 들어, 사용자가 이 확인란을 선택하고 진단 연결 문자열에서 `UseDevelopmentStorage=true`를 지정한다고 가정합니다. Azure에 프로젝트를 게시할 때 Visual Studio는 사용자가 게시 마법사에 지정한 저장소 계정을 사용하여 진단 연결 문자열을 자동으로 업데이트합니다. 그러나 실제 저장소 계정을 진단 연결 문자열로 지정한 경우 해당 계정이 대신 사용됩니다.

## Azure SDK 2.4 이하 및 Azure SDK 2.5 이상 간의 진단 기능 차이점

Azure SDK 2.4에서 Azure SDK 2.5 이상으로 업그레이드하는 경우 다음 진단 기능 차이점을 명심해야 합니다.

- **구성 API가 더 이상 사용되지 않음** – 진단의 프로그래밍 방식 구성은 Azure SDK 2.4 이하 버전에서는 사용할 수 있지만 Azure SDK 2.5 이상 버전에서는 더 이상 사용되지 않습니다. 코드에 진단 구성이 현재 정의된 경우 계속해서 진단하려면 마이그레이션된 프로젝트에서 해당 설정을 처음부터 다시 구성해야 합니다. Azure SDK 2.4에 대한 진단 구성 파일은 diagnostics.wadcfg이고 Azure SDK 2.5 이상에서는 diagnostics.wadcfgx입니다.

- **클라우드 서비스 응용 프로그램에 대한 진단은 인스턴스 수준이 아닌 역할 수준에서만 구성할 수 있습니다.**

- **앱을 배포할 때마다 진단 구성이 업데이트됨** – 이로 인해 서버 탐색기에서 진단 구성을 변경한 후 앱을 다시 배포하는 경우 패리티 문제가 발생할 수 있습니다.

- **Azure SDK 2.5 이상에서 코드가 아닌 진단 구성 파일에 크래시 덤프가 구성됨** – 코드에 크래시 덤프가 구성된 경우, Azure SDK 2.6으로 마이그레이션하는 동안 크래시 덤프가 전송되지 않으므로 해당 구성을 코드에서 구성 파일로 수동으로 전송해야 합니다.

<!---HONumber=AcomDC_0302_2016-->