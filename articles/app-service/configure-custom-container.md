---
title: 사용자 지정 컨테이너 구성
description: Azure App Service에서 사용자 지정 컨테이너를 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5b1bf9b205fc1eb90c6eeae3a101def764381213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91264578"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Azure App Service에 대 한 사용자 지정 컨테이너 구성

이 문서에서는 Azure App Service에서 실행 되도록 사용자 지정 컨테이너를 구성 하는 방법을 보여 줍니다.

::: zone pivot="container-windows"

이 가이드에서는 App Service의 Windows 앱 컨테이너 화에 대 한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [사용자 지정 컨테이너 빠른](quickstart-custom-container.md) 시작 및 [자습서](tutorial-custom-container.md) 를 따르세요.

::: zone-end

::: zone pivot="container-linux"

이 가이드에서는 App Service의 Linux 앱 컨테이너 화에 대 한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [사용자 지정 컨테이너 빠른](quickstart-custom-container.md) 시작 및 [자습서](tutorial-custom-container.md) 를 따르세요. [다중 컨테이너 앱 빠른](quickstart-multi-container.md) 시작 및 [자습서](tutorial-multi-container-app.md)도 있습니다.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>지원 되는 부모 이미지

사용자 지정 Windows 이미지의 경우 원하는 프레임 워크에 적합 한 [부모 이미지 (기본 이미지)](https://docs.docker.com/develop/develop-images/baseimages/) 를 선택 해야 합니다.

- .NET Framework 앱을 배포 하려면 Windows Server Core [LTSC (장기 서비스 채널)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 릴리스를 기반으로 하는 부모 이미지를 사용 합니다. 
- .NET Core 앱을 배포 하려면 Windows Server Nano [세미 연간 서비스 채널 (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 릴리스를 기반으로 하는 부모 이미지를 사용 합니다. 

앱을 시작하는 동안 부모 이미지를 다운로드하는 데 다소 시간이 걸립니다. 그러나 Azure App Service에서 이미 캐시된 다음 부모 이미지 중 하나를 사용하여 시작 시간을 줄일 수 있습니다.

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>사용자 지정 컨테이너의 Docker 이미지 변경

기존 사용자 지정 컨테이너 앱을 현재 Docker 이미지에서 새 이미지로 변경 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>개인 레지스트리의 이미지 사용

Azure Container Registry와 같은 개인 레지스트리의 이미지를 사용 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

및의 경우 *\<username>* *\<password>* 개인 레지스트리 계정에 대 한 로그인 자격 증명을 제공 합니다.

## <a name="i-dont-see-the-updated-container"></a>업데이트 된 컨테이너가 표시 되지 않습니다.

새 컨테이너를 가리키도록 Docker 컨테이너 설정을 변경 하는 경우 앱이 새 컨테이너의 HTTP 요청을 처리 하기까지 몇 분 정도 걸릴 수 있습니다. 새 컨테이너가 끌어오고 시작 되는 동안 App Service는 이전 컨테이너의 요청을 계속 처리 합니다. 새 컨테이너가 시작 되 고 요청을 받을 준비가 된 경우에만 요청을 보내기 시작 App Service 합니다.

## <a name="how-container-images-are-stored"></a>컨테이너 이미지를 저장 하는 방법

App Service에서 사용자 지정 Docker 이미지를 처음 실행 하는 경우 App Service을 수행 하 `docker pull` 고 모든 이미지 계층을 가져옵니다. 이러한 레이어는 Docker 온-프레미스를 사용 하는 경우 처럼 디스크에 저장 됩니다. 앱이 다시 시작 될 때마다는 App Service `docker pull` 하지만 변경 된 레이어만 가져옵니다. 변경 내용이 없는 경우 App Service는 로컬 디스크에서 기존 계층을 사용 합니다.

앱이 가격 책정 계층의 규모를 확대/축소 하는 등의 이유로 계산 인스턴스를 변경 하는 경우에는 모든 계층을 다시 풀 App Service 해야 합니다. 인스턴스를 더 추가 하도록 확장 하는 경우에도 마찬가지입니다. 또한 크기 조정 작업 없이 앱 인스턴스가 변경 될 수 있는 드문 경우도 있습니다.

## <a name="configure-port-number"></a>포트 번호 구성

기본적으로 App Service는 사용자 지정 컨테이너가 포트 80에서 수신 대기 하는 것으로 가정 합니다. 컨테이너가 다른 포트를 수신 대기 하는 경우 `WEBSITES_PORT` App Service 앱에서 앱 설정을 설정 합니다. [Cloud Shell](https://shell.azure.com)를 통해 설정할 수 있습니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

현재 App Service 컨테이너에서 HTTP 요청에 대해 하나의 포트만 노출할 수 있습니다. 

## <a name="configure-environment-variables"></a>환경 변수 구성

사용자 지정 컨테이너는 외부에서 제공 해야 하는 환경 변수를 사용할 수 있습니다. [Cloud Shell](https://shell.azure.com)를 통해에 전달할 수 있습니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

앱이 실행 되 면 App Service 앱 설정이 자동으로 환경 변수로 프로세스에 삽입 됩니다. 

::: zone pivot="container-windows"
IIS 또는 .NET Framework (4.0 이상) 기반 컨테이너의 경우 `System.ConfigurationManager` App Service에 의해 자동으로 .net 앱 설정 및 연결 문자열로 삽입 됩니다. 다른 모든 언어 또는 프레임 워크의 경우에는 다음의 해당 접두사 중 하 나와 함께 프로세스에 대 한 환경 변수로 제공 됩니다.

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

이 메서드는 단일 컨테이너 앱 또는 다중 컨테이너 앱에 대해 모두 작동 하며 환경 변수는 *docker-compose.ci.build.yml* 파일에 지정 됩니다.

::: zone-end

## <a name="use-persistent-shared-storage"></a>영구 공유 저장소 사용

::: zone pivot="container-windows"

앱의 파일 시스템에서 *C:\home* 디렉터리를 사용 하 여 다시 시작할 때마다 파일을 유지 하 고 인스턴스 간에 공유할 수 있습니다. `C:\home`컨테이너 앱이 영구 저장소에 액세스할 수 있도록 앱의가 제공 됩니다.

영구 저장소를 사용 하지 않도록 설정 하면 디렉터리에 대 한 쓰기는 `C:\home` 지속 되지 않습니다. [Docker 호스트 로그 및 컨테이너 로그](#access-diagnostic-logs) 는 컨테이너에 연결 되지 않은 기본 영구 공유 저장소에 저장 됩니다. 영구 저장소를 사용 하도록 설정 하면 디렉터리에 대 한 모든 쓰기 `C:\home` 는 유지 되 고 확장 된 앱의 모든 인스턴스에서 액세스할 수 있으며 로그는에서 액세스할 수 있습니다 `C:\home\LogFiles` .

기본적으로 영구 저장소는 *사용 하지 않도록* 설정 되 고 설정은 응용 프로그램 설정에 표시 되지 않습니다. 이 기능을 사용 하도록 설정 하려면 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Cloud Shell](https://shell.azure.com)을 통해 앱 설정을 설정 합니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

::: zone-end

::: zone pivot="container-linux"

앱의 파일 시스템에서 */home* 디렉터리를 사용 하 여 다시 시작할 때마다 파일을 유지 하 고 인스턴스 간에 공유할 수 있습니다. `/home`컨테이너 앱이 영구 저장소에 액세스할 수 있도록 앱의가 제공 됩니다.

영구 저장소를 사용 하지 않도록 설정 하면 디렉터리에 대 한 쓰기가 `/home` 앱을 다시 시작 하거나 여러 인스턴스에 걸쳐 지속 되지 않습니다. 유일한 예외는 `/home/LogFiles` Docker 및 컨테이너 로그를 저장 하는 데 사용 되는 디렉터리입니다. 영구 저장소를 사용 하도록 설정 하면 디렉터리에 대 한 모든 쓰기 `/home` 는 유지 되며 확장 된 앱의 모든 인스턴스에서 액세스할 수 있습니다.

기본적으로 영구 저장소는 *사용 하도록* 설정 되어 있으며 설정은 응용 프로그램 설정에 표시 되지 않습니다. 이 기능을 사용 하지 않도록 설정 하려면 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Cloud Shell](https://shell.azure.com)을 통해 앱 설정을 설정 합니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=false}
```

::: zone-end

> [!NOTE]
> 사용자 [고유의 영구 저장소를 구성할](configure-connect-to-azure-storage.md)수도 있습니다.

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service는 프런트 엔드에서 TLS/SSL을 종료 합니다. 이는 TLS/SSL 요청이 앱에 도달할 수 없음을 의미 합니다. 필요 하지 않으며 앱에 대 한 TLS/SSL에 대 한 지원을 구현 하지 않아야 합니다. 

프런트 엔드는 Azure 데이터 센터 내에 있습니다. 앱에서 TLS/SSL을 사용 하는 경우 인터넷을 통한 트래픽은 항상 안전 하 게 암호화 됩니다.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>ASP.NET 컴퓨터 키 주입 사용자 지정

 컨테이너를 시작 하는 동안 자동으로 생성 된 키는 ASP.NET 암호화 루틴에 대 한 컴퓨터 키로 컨테이너에 삽입 됩니다. 다음 환경 변수를 검색 하 여 [컨테이너에서 이러한 키를 찾을](#connect-to-the-container) 수 있습니다.,, `MACHINEKEY_Decryption` `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

앱이이에 종속 되는 경우 각 다시 시작 시 새 키가 ASP.NET 폼 인증 및 뷰 상태를 다시 설정할 수 있습니다. 키 자동 생성을 방지 하려면 [앱 설정 App Service으로 수동으로 설정](#configure-environment-variables)합니다. 

## <a name="connect-to-the-container"></a>컨테이너에 연결

로 이동 하 여 진단 작업에 대 한 직접 Windows 컨테이너에 연결할 수 있습니다 `https://<app-name>.scm.azurewebsites.net/DebugConsole` . 작동 방식은 다음과 같습니다.

- 디버그 콘솔에서 PowerShell 세션 시작, 레지스트리 키 검사, 전체 컨테이너 파일 시스템 탐색 등의 대화형 명령을 실행할 수 있습니다.
- 위의 그래픽 브라우저와는 별도로 작동 하며, [공유 저장소](#use-persistent-shared-storage)의 파일만 표시 합니다.
- 확장 된 앱에서 디버그 콘솔은 컨테이너 인스턴스 중 하나에 연결 됩니다. 최상위 메뉴의 **인스턴스** 드롭다운에서 다른 인스턴스를 선택할 수 있습니다.
- 콘솔 내에서 컨테이너에 대 한 모든 변경 내용은 Docker 이미지의 일부가 아니므로 앱을 다시 시작 하는 경우에는 유지 *되지* 않습니다 (공유 저장소의 변경 내용 제외). 레지스트리 설정 및 소프트웨어 설치와 같은 변경 내용을 유지 하려면 Dockerfile의 일부로 만듭니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

App Service는 Docker 호스트 및 컨테이너 내 작업에의 한 작업을 기록 합니다. Docker 호스트 (플랫폼 로그)의 로그는 기본적으로 제공 되지만 컨테이너 내에서 응용 프로그램 로그 나 웹 서버 로그를 수동으로 사용 하도록 설정 해야 합니다. 자세한 내용은 [응용 프로그램 로깅 사용](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) 및 [웹 서버 로깅 사용](troubleshoot-diagnostic-logs.md#enable-web-server-logging)을 참조 하세요. 

Docker 로그에 액세스 하는 방법에는 여러 가지가 있습니다.

- [Azure Portal에서](#in-azure-portal)
- [Kudu 콘솔에서](#from-the-kudu-console)
- [Kudu API 사용](#with-the-kudu-api)
- [Azure Monitor로 로그 보내기](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Azure Portal에서

Docker 로그는 포털의 앱 **컨테이너 설정** 페이지에 표시 됩니다. 로그가 잘리고 **다운로드**를 클릭 하 여 모든 로그를 다운로드할 수 있습니다. 

### <a name="from-the-kudu-console"></a>Kudu 콘솔에서

으로 이동 하 여 `https://<app-name>.scm.azurewebsites.net/DebugConsole` **LogFiles** 폴더를 클릭 하면 개별 로그 파일이 표시 됩니다. 전체 **LogFiles** 디렉터리를 다운로드 하려면 디렉터리 이름 왼쪽에 있는 **다운로드** 아이콘을 클릭 합니다. FTP 클라이언트를 사용 하 여이 폴더에 액세스할 수도 있습니다.

`C:\home\LogFiles`영구 공유 저장소가 사용 하도록 설정 되어 있지 않으므로 콘솔 터미널에서 기본적으로 폴더에 액세스할 수 없습니다. 콘솔 터미널에서이 동작을 사용 하도록 설정 하려면 [영구 공유 저장소를 사용 하도록 설정](#use-persistent-shared-storage)합니다.

FTP 클라이언트를 사용 하 여 현재 사용 중인 Docker 로그를 다운로드 하려고 하면 파일 잠금으로 인해 오류가 발생할 수 있습니다.

### <a name="with-the-kudu-api"></a>Kudu API 사용

로 직접 이동 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 하 여 Docker 로그에 대 한 메타 데이터를 확인 합니다. 로그 파일이 두 개 이상 표시 될 수 있으며, 속성을 통해 `href` 로그 파일을 직접 다운로드할 수 있습니다. 

모든 로그를 하나의 ZIP 파일에 함께 다운로드 하려면 액세스를 선택 `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` 합니다.

## <a name="customize-container-memory"></a>컨테이너 메모리 사용자 지정

기본적으로 Azure App Service에 배포 된 모든 Windows 컨테이너는 1gb RAM으로 제한 됩니다. `WEBSITE_MEMORY_LIMIT_MB` [Cloud Shell](https://shell.azure.com)을 통해 앱 설정을 제공 하 여이 값을 변경할 수 있습니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

값은 MB 단위로 정의 되며 호스트의 총 실제 메모리 보다 작거나 같아야 합니다. 예를 들어, RAM이 8gb 인 App Service 계획에서 `WEBSITE_MEMORY_LIMIT_MB` 모든 앱에 대 한 누적 합계는 8gb를 초과 해서는 안 됩니다. 각 가격 책정 계층에 사용할 수 있는 메모리의 양에 대 한 정보는 **프리미엄 컨테이너 (Windows) 계획** 섹션에서 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/windows/)을 참조 하세요.

## <a name="customize-the-number-of-compute-cores"></a>계산 코어 수를 사용자 지정 합니다.

기본적으로 Windows 컨테이너는 선택한 가격 책정 계층에 대해 사용 가능한 모든 코어를 사용 하 여 실행 됩니다. 예를 들어 스테이징 슬롯에서 사용 하는 코어 수를 줄일 수 있습니다. 컨테이너에서 사용 하는 코어 수를 줄이려면 `WEBSITE_CPU_CORES_LIMIT` 앱 설정을 기본 코어 수로 설정 합니다. [Cloud Shell](https://shell.azure.com)를 통해 설정할 수 있습니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> 앱 설정을 업데이트 하면 자동으로 다시 시작 되어 가동 중지 시간을 최소화 합니다. 프로덕션 앱의 경우 스테이징 슬롯으로 교환 하 고 스테이징 슬롯에서 앱 설정을 변경한 다음 다시 프로덕션으로 교체 하는 것이 좋습니다.

Kudu Console ()로 이동 하 `https://<app-name>.scm.azurewebsites.net` 고 PowerShell을 사용 하 여 다음 명령을 입력 하 여 조정 된 번호를 확인 합니다. 각 명령은 숫자를 출력 합니다.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

프로세서는 다중 코어 또는 하이퍼스레딩 프로세서 일 수 있습니다. 각 가격 책정 계층에 사용할 수 있는 코어 수에 대 한 정보는 **프리미엄 컨테이너 (Windows) 계획** 섹션에서 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/windows/)을 참조 하세요.

## <a name="customize-health-ping-behavior"></a>상태 ping 동작 사용자 지정

App Service 컨테이너를 시작 하 고 HTTP ping에 응답할 때 컨테이너가 성공적으로 시작 되는 것으로 간주 합니다. 상태 ping 요청 컨테이너 헤더 `User-Agent= "App Service Hyper-V Container Availability Check"` 입니다. 컨테이너가 시작 되지만 특정 시간 후에 ping에 응답 하지 않는 경우 App Service는 Docker 로그에 이벤트를 기록 하 여 컨테이너를 시작 하지 않았다는 것을 알려 주는 것입니다. 

응용 프로그램이 리소스를 많이 사용 하는 경우 컨테이너는 시간에 HTTP ping에 응답 하지 않을 수 있습니다. HTTP ping이 실패할 때의 작업을 제어 하려면 `CONTAINER_AVAILABILITY_CHECK_MODE` 앱 설정을 설정 합니다. [Cloud Shell](https://shell.azure.com)를 통해 설정할 수 있습니다. Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

다음 표에서는 가능한 값을 보여 줍니다.

| 값 | 설명 |
| - | - |
| **복구한** | 3 회 연속 가용성 검사 후 컨테이너를 다시 시작 합니다. |
| **ReportOnly** | 기본값입니다. 3 개의 연속 가용성 검사 후 컨테이너를 다시 시작 하지 않고 컨테이너의 Docker 로그에 보고 합니다. |
| 해제 | 가용성을 확인 하지 않습니다. |

## <a name="support-for-group-managed-service-accounts"></a>그룹 관리 서비스 계정 지원

GMSAs (그룹 관리 서비스 계정)는 현재 App Service Windows 컨테이너에서 지원 되지 않습니다.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 사용자 지정 컨테이너가 SSH를 지원 하도록 하려면 Dockerfile 자체에 추가 해야 합니다.

> [!TIP]
> 모든 기본 제공 Linux 컨테이너는 해당 이미지 리포지토리에 SSH 명령을 추가 했습니다. [Node.js 10.14 리포지토리](https://github.com/Azure-App-Service/node/blob/master/10.14) 를 사용 하 여 다음 지침을 수행 하 여 어떻게 활성화 되어 있는지 확인할 수 있습니다.

- [실행](https://docs.docker.com/engine/reference/builder/#run) 명령을 사용 하 여 SSH 서버를 설치 하 고 루트 계정에 대 한 암호를로 설정 `"Docker!"` 합니다. 예를 들어 [알파인 Linux](https://hub.docker.com/_/alpine)를 기반으로 하는 이미지의 경우 다음 명령이 필요 합니다.

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    이 구성은 컨테이너에 대한 외부 연결을 허용하지 않습니다. SSH는를 통해서만 사용할 수 `https://<app-name>.scm.azurewebsites.net` 있으며 게시 자격 증명으로 인증 됩니다.

- [이 sshd_config 파일](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 을 이미지 리포지토리에 추가 하 고 [복사](https://docs.docker.com/engine/reference/builder/#copy) 명령을 사용 하 여 파일을 */etc/ssh/* 디렉터리에 복사 합니다. *Sshd_config* 파일에 대 한 자세한 내용은 [openbsd 설명서](https://man.openbsd.org/sshd_config)를 참조 하세요.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* 파일에 다음이 항목되어야 합니다.
    > - `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > - `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

- 표시 명령을 [사용 하 여 컨테이너](https://docs.docker.com/engine/reference/builder/#expose) 에서 포트 2222를 엽니다. 루트 암호는 알려져 있지만 포트 2222은 인터넷에서 액세스할 수 없습니다. 개인 가상 네트워크의 브리지 네트워크 내 컨테이너 에서만 액세스할 수 있습니다.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 컨테이너의 시작 스크립트에서 SSH 서버를 시작 합니다.

    ```bash
    /usr/sbin/sshd
    ```

    예제는 기본 [Node.js 10.14 컨테이너가](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SSH 서버를 시작 하는 방법을 참조 하세요.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>다중 컨테이너 앱 구성

- [Docker Compose에서 영구적 저장소 사용](#use-persistent-storage-in-docker-compose)
- [미리 보기 제한 사항](#preview-limitations)
- [Docker Compose 옵션](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose에서 영구적 저장소 사용

WordPress 같은 다중 컨테이너 앱은 영구적 저장소가 제대로 작동 해야 합니다. 이 기능을 사용 하도록 설정 하려면 Docker Compose 구성이 컨테이너 *외부* 의 저장소 위치를 가리켜야 합니다. 컨테이너 내부의 저장소 위치는 앱을 다시 시작 하는 이후 변경 내용을 유지 하지 않습니다.

`WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Cloud Shell](https://shell.azure.com)에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용 하 여 앱 설정을 설정 하 여 영구 저장소를 사용 하도록 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*Docker-compose.ci.build.yml* 파일에서 `volumes` 옵션을에 매핑합니다 `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME`은 앱의 영구 스토리지에 매핑되는 App Service의 환경 변수입니다. 예를 들면 다음과 같습니다.

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>미리 보기 제한 사항

다중 컨테이너는 현재 미리 보기로 제공 됩니다. 다음 App Service 플랫폼 기능은 지원 되지 않습니다.

- 인증/권한 부여
- 관리 ID
- CORS

### <a name="docker-compose-options"></a>Docker Compose 옵션

다음 목록에서는 지원 되거나 지원 되지 않는 Docker Compose 구성 옵션을 보여 줍니다.

#### <a name="supported-options"></a>지원되는 옵션

- 명령을 사용합니다.
- entrypoint
- environment
- 이미지
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>지원되지 않는 옵션

- build(허용 안 함)
- depends_on(무시됨)
- networks(무시됨)
- secrets(무시됨)
- 포트 80 및 8080 이외의 포트(무시됨)

> [!NOTE]
> 명시적으로 호출 되지 않는 다른 모든 옵션은 공개 미리 보기에서 무시 됩니다.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 사용자 지정 컨테이너를 사용 하 여 Azure App Service 사용자 지정 소프트웨어 마이그레이션](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)

::: zone-end

또는 추가 리소스를 참조 하세요.

[Windows/Linux 컨테이너에서 인증서 로드](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)