---
title: 진단 로깅 사용
description: 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법에 대해 알아봅니다.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769996"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service에서 앱에 대한 진단 로깅 사용
## <a name="overview"></a>개요
Azure는 [App Service 앱](overview.md)을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. 이 문서에서는 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법을 설명합니다.

이 문서에서는 진단 로그 작업에 [Azure Portal](https://portal.azure.com)과 Azure CLI를 사용합니다. Visual Studio를 사용하여 진단 로그로 작업하는 방법에 대한 자세한 내용은 [Visual Studio에서 Azure 문제 해결](troubleshoot-dotnet-visual-studio.md)을 참조하세요.

> [!NOTE]
> 이 문서의 로깅 지침 외에도 Azure 모니터링을 사용하는 새로운 통합 로깅 기능이 있습니다. 이 기능에 대한 자세한 내용은 [Azure 모니터로 로그 보내기(미리 보기)](#send-logs-to-azure-monitor-preview) 섹션에서 확인할 수 있습니다. 
>
>

|Type|플랫폼|위치|Description|
|-|-|-|-|
| 애플리케이션 로깅 | Windows, Linux | 앱 서비스 파일 시스템 및/또는 Azure 저장소 Blob | 응용 프로그램 코드에서 생성된 메시지를 기록합니다. 선택한 웹 프레임워크또는 언어의 표준 로깅 패턴을 사용하여 응용 프로그램 코드에서 직접 메시지를 생성할 수 있습니다. 각 메시지는 **중요,** **오류,** **경고,** **정보,** **디버그**및 **추적**의 범주 중 하나를 할당합니다. 응용 프로그램 로깅을 활성화할 때 심각도 수준을 설정하여 로깅을 원하는 세부 수준을 선택할 수 있습니다.|
| 웹 서버 로깅| Windows | 앱 서비스 파일 시스템 또는 Azure 저장소 Blob| [W3C 확장 로그 파일 형식의](/windows/desktop/Http/w3c-logging)원시 HTTP 요청 데이터 . 각 로그 메시지에는 HTTP 메서드, 리소스 URI, 클라이언트 IP, 클라이언트 포트, 사용자 에이전트, 응답 코드 등과 같은 데이터가 포함됩니다. |
| 자세한 오류 메시지| Windows | 앱 서비스 파일 시스템 | 클라이언트 브라우저로 전송되었을 *.htm* 오류 페이지의 복사본입니다. 보안상의 이유로 자세한 오류 페이지를 프로덕션 환경에서 클라이언트로 전송해서는 안 되지만, App Service는 HTTP 코드 400 이상인 응용 프로그램 오류가 발생할 때마다 오류 페이지를 저장할 수 있습니다. 페이지에는 서버가 오류 코드를 반환하는 이유를 확인하는 데 도움이 되는 정보가 포함될 수 있습니다. |
| 실패한 요청 추적 | Windows | 앱 서비스 파일 시스템 | 요청을 처리하는 데 사용되는 IIS 구성 요소의 추적 및 각 구성 요소에서 걸린 시간을 포함하여 실패한 요청에 대한 자세한 추적 정보입니다. 사이트 성능을 향상시키거나 특정 HTTP 오류를 분리하려는 경우에 유용합니다. 실패한 각 요청에 대해 XML 로그 파일과 로그 파일을 볼 XSL 스타일시트가 포함된 하나의 폴더가 생성됩니다. |
| 배포 로깅 | Windows, Linux | 앱 서비스 파일 시스템 | 앱에 콘텐츠를 게시할 때 로그합니다. 배포 로깅은 자동으로 발생하며 배포 로깅에 대한 구성 가능한 설정이 없습니다. 배포에 실패한 이유를 확인하는 데 도움이 됩니다. 예를 들어 [사용자 지정 배포 스크립트를](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)사용하는 경우 배포 로깅을 사용하여 스크립트가 실패하는 이유를 확인할 수 있습니다. |

> [!NOTE]
> App Service는 응용 프로그램 문제를 해결하는 데 도움이 되는 전용 대화형 진단 도구를 제공합니다. 자세한 내용은 [Azure 앱 서비스 진단 개요를](overview-diagnostics.md)참조하십시오.
>
> 또한 다른 Azure 서비스를 사용하여 [Azure Monitor](../azure-monitor/app/azure-web-apps.md)와 같은 앱의 로깅 및 모니터링 기능을 향상시킬 수 있습니다.
>

## <a name="enable-application-logging-windows"></a>응용 프로그램 로깅 사용(Windows)

> [!NOTE]
> Blob 저장소에 대한 응용 프로그램 로깅은 앱 서비스와 동일한 지역의 저장소 계정만 사용할 수 있습니다.

[Azure 포털에서](https://portal.azure.com)Windows 앱에 대한 응용 프로그램 로깅을 사용하려면 앱을 탐색하고 **앱 서비스 로그를 선택합니다.**

응용 프로그램 **로깅(파일 시스템)** 또는 **응용 프로그램 로깅(Blob)** 또는 둘 다에 **대해 켜기(켜기)를** 선택합니다. 

**Filesystem** 옵션은 임시 디버깅을 위한 것이며 12시간 만에 꺼집니다. **Blob** 옵션은 장기 로깅용이며 로그를 작성하려면 Blob 저장소 컨테이너가 필요합니다.  **Blob** 옵션에는 로그 메시지의 원본 VM`InstanceId`인스턴스(), 스레드 ID()`Tid`및 보다 세분화된 타임스탬프()와[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)같은 추가 정보도 로그 메시지에 포함됩니다.

> [!NOTE]
> 현재 .NET 애플리케이션 로그만 Blob Storage에 쓸 수 있습니다. Java, PHP, Node.js, Python 응용 프로그램 로그는 앱 서비스 파일 시스템에만 저장할 수 있습니다(외부 저장소에 로그를 작성하는 코드 수정 없이).
>
> 또한 저장소 [계정의 액세스 키를 다시 생성하는](../storage/common/storage-create-storage-account.md)경우 업데이트된 액세스 키를 사용하려면 각 로깅 구성을 재설정해야 합니다. 다음을 수행합니다.
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기**로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

**로그할 수준**또는 세부 정보 수준을 선택합니다. 다음 표에서는 각 수준에 포함된 로그 범주를 보여 주며 있습니다.

| Level | 포함된 카테고리 |
|-|-|
|**사용 안 함** | None |
|**오류** | 오류, 위험 |
|**Warning** | 경고, 오류, 위험|
|**정보** | 정보, 경고, 오류, 위험|
|**자세한** | 추적, 디버그, 정보, 경고, 오류, 위험(모든 범주) |

완료되면 **저장을**선택합니다.

## <a name="enable-application-logging-linuxcontainer"></a>응용 프로그램 로깅 사용(Linux/컨테이너)

[Azure 포털에서](https://portal.azure.com)Linux 앱 또는 사용자 지정 컨테이너 앱에 대한 응용 프로그램 로깅을 사용하려면 앱을 탐색하고 **앱 서비스 로그를 선택합니다.**

**응용 프로그램 로깅에서** **파일 시스템을**선택합니다.

**할당량(MB)에서**응용 프로그램 로그에 대한 디스크 할당량을 지정합니다. **보존 기간(일)에서**로그를 보존할 일 수를 설정합니다.

완료되면 **저장을**선택합니다.

## <a name="enable-web-server-logging"></a>웹 서버 로깅 사용

[Azure 포털에서](https://portal.azure.com)Windows 앱에 대한 웹 서버 로깅을 사용하려면 앱을 탐색하고 **앱 서비스 로그를 선택합니다.**

**웹 서버 로깅의**경우 **저장소를** 선택하여 Blob 저장소에 로그를 저장하거나 **파일 시스템을** 선택하여 앱 서비스 파일 시스템에 로그를 저장합니다. 

**보존 기간(일)에서**로그를 보존할 일 수를 설정합니다.

> [!NOTE]
> [스토리지 계정의 선택키를 다시 생성](../storage/common/storage-create-storage-account.md)하는 경우 해당 로깅 구성을 다시 설정하여 업데이트한 키를 사용해야 합니다. 다음을 수행합니다.
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기**로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

완료되면 **저장을**선택합니다.

## <a name="log-detailed-errors"></a>자세한 오류 로그

[Azure 포털에서](https://portal.azure.com)Windows 앱에 대한 오류 페이지 또는 실패한 요청 추적을 저장하려면 앱을 탐색하고 **앱 서비스 로그를 선택합니다.**

**자세한 오류 로깅** 또는 **실패한 요청 추적에서** **를**선택한 다음 **저장을**선택합니다.

두 유형의 로그는 모두 앱 서비스 파일 시스템에 저장됩니다. 최대 50개의 오류(파일/폴더)가 유지됩니다. HTML 파일 수가 50을 초과하면 가장 오래된 26개의 오류가 자동으로 삭제됩니다.

## <a name="add-log-messages-in-code"></a>코드에 로그 메시지 추가

응용 프로그램 코드에서 일반적인 로깅 프로그램을 사용하여 응용 프로그램 로그에 로그 메시지를 보냅니다. 예를 들어:

- ASP.NET 애플리케이션은 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 클래스를 사용하여 애플리케이션 진단 로그에 정보를 로깅할 수 있습니다. 예를 들어:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 기본적으로 ASP.NET [코어는 Microsoft.Extension.Loggings.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 로깅 공급자를 사용합니다. 자세한 내용은 [Azure의 ASP.NET Core 로깅](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)을 참조하세요.

## <a name="stream-logs"></a>로그 스트리밍

실시간으로 로그를 스트리밍하기 전에 원하는 로그 유형을 사용하도록 설정합니다. .txt, .log 또는 .htm으로 끝나는 파일에 기록된 모든 정보는 */LogFiles* 디렉터리(d:/home/logfiles)에 저장되어 앱 서비스에서 스트리밍됩니다.

> [!NOTE]
> 일부 유형의 로깅 버퍼는 로그 파일에 기록하고 이로 인해 스크림에서 이벤트가 작동하지 않을 수 있습니다. 예를 들어 사용자가 페이지를 방문할 때 발생한 애플리케이션 로그 항목이 페이지 요청에 대한 해당 HTTP 로그 항목보다 먼저 스트림에 표시될 수 있습니다.
>

### <a name="in-azure-portal"></a>Azure Portal에서

[Azure 포털에서](https://portal.azure.com)로그를 스트리밍하려면 앱으로 이동하여 **로그 스트림을**선택합니다. 

### <a name="in-cloud-shell"></a>클라우드 셸에서

[Cloud Shell에서](../cloud-shell/overview.md)로그를 스트리밍하려면 다음 명령을 사용합니다.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

오류와 같은 특정 이벤트를 필터링하려면 **--Filter** 매개 변수를 사용합니다. 예를 들어:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
HTTP와 같은 특정 로그 유형을 필터링하려면 **-Path** 매개 변수를 사용합니다. 예를 들어:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>로컬 터미널에서

로컬 콘솔에서 로그를 스트리밍하려면 [Azure CLI를 설치하고](https://docs.microsoft.com/cli/azure/install-azure-cli) [계정에 로그인합니다.](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) 로그인한 후 [클라우드 셸에 대한 지침을](#in-cloud-shell) 따랐습니다.

## <a name="access-log-files"></a>로그 파일 액세스

로그 유형에 대해 Azure Storage Blob 옵션을 구성하는 경우 Azure Storage에서 작동하는 클라이언트 도구가 필요합니다. 자세한 내용은 [Azure 저장소 클라이언트 도구를](../storage/common/storage-explorers.md)참조하십시오.

앱 서비스 파일 시스템에 저장된 로그의 경우 가장 쉬운 방법은 브라우저에서 ZIP 파일을 다운로드하는 것입니다.

- 리눅스 / 컨테이너 애플 리케이션 :`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- 윈도우 애플 리 케이 션:`https://<app-name>.scm.azurewebsites.net/api/dump`

Linux/컨테이너 앱의 경우 ZIP 파일에는 도커 호스트와 도커 컨테이너 모두에 대한 콘솔 출력 로그가 포함되어 있습니다. 확장된 앱의 경우 ZIP 파일에는 각 인스턴스에 대해 하나의 로그 집합이 포함되어 있습니다. 앱 서비스 파일 시스템에서 이러한 로그 파일은 */home/LogFiles* 디렉터리의 내용입니다.

Windows 앱의 경우 ZIP 파일에는 앱 서비스 파일 시스템에서 *D:\Home\LogFiles* 디렉터리의 내용이 포함되어 있습니다. 구조는 다음과 같습니다.

| 로그 형식 | 디렉터리 | 설명 |
|-|-|-|
| **응용 프로그램 로그** |*/로그파일/어플리케이션/* | 하나 이상의 텍스트 파일이 포함되어 있습니다. 로그 메시지의 형식은 사용하는 로깅 공급자에 따라 다릅니다. |
| **실패한 요청 추적** | */로그파일/W3SVC##########//* | XML 파일 및 XSL 파일이 포함됩니다. 브라우저에서 형식이 지정된 XML 파일을 볼 수 있습니다. |
| **자세한 오류 로그** | */로그파일/상세 오류/* | HTM 오류 파일이 포함되어 있습니다. 브라우저에서 HTM 파일을 볼 수 있습니다.<br/>실패한 요청 추적을 보는 또 다른 방법은 포털에서 앱 페이지로 이동하는 것입니다. 왼쪽 메뉴에서 **문제 진단 및 해결을**선택한 다음 실패한 요청 추적 **로그를**검색한 다음 아이콘을 클릭하여 원하는 추적을 찾아보고 봅니다. |
| **웹 서버 로그** | */로그파일/http/로로그/* | [W3C 확장 로그](/windows/desktop/Http/w3c-logging)파일 형식을 사용하여 서식이 지정된 텍스트 파일이 포함되어 있습니다. 이 정보는 텍스트 편집기 또는 Log [Parser](https://go.microsoft.com/fwlink/?LinkId=246619)와 같은 유틸리티를 사용하여 읽을 수 있습니다.<br/>앱 서비스는 `s-computername`의 를 `s-ip` `cs-version` 지원하지 않습니다. |
| **배포 로그** | */로그파일/Git//배포/* */deployments/* | 내부 배포 프로세스에서 생성된 로그와 Git 배포에 대한 로그를 포함합니다. |

## <a name="send-logs-to-azure-monitor-preview"></a>Azure 모니터로 로그 보내기(미리 보기)

새 [Azure Monitor 통합을](https://aka.ms/appsvcblog-azmon)사용하면 [진단 설정(미리 보기)을 만들어](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) 저장소 계정, 이벤트 허브 및 로그 분석으로 로그를 보낼 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![진단 설정(미리 보기)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>지원되는 로그 유형

다음 표에서는 지원되는 로그 유형 및 설명을 보여 주며 있습니다. 

| 로그 형식 | Windows 지원 | 리눅스 (도커) 지원 | 설명 |
|-|-|-|
| 앱서비스콘솔로그 | TBA | 예 | 표준 출력 및 표준 오류 |
| 앱서비스HTTP로그 | 예 | 예 | 웹 서버 로그 |
| 앱 서비스환경플랫폼로그 | 예 | 예 | 앱 서비스 환경: 크기 조정, 구성 변경 및 상태 로그|
| 앱서비스감사로그 | 예 | 예 | FTP 및 쿠두를 통한 로그인 활동 |
| 앱서비스파일감사로그 | 예 | TBD | FTP 및 쿠두를 통한 파일 변경 |
| 앱 서비스 앱로그 | TBA | 자바 SE & 톰캣 | 애플리케이션 로그 전송 사용 |

## <a name="next-steps"></a><a name="nextsteps"></a> 다음 단계
* [Azure 모니터를 가진 쿼리 로그](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service에서 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
