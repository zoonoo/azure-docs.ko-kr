---
title: 포털-Azure App Service에서에서 앱 구성
description: Azure portal에서 App Service 앱에 대 한 일반 설정을 구성 하는 방법을 알아봅니다.
keywords: azure 앱 서비스, 웹 앱, 앱 설정, 환경 변수
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957912"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Azure portal에서 App Service 앱을 구성 합니다.

이 항목에서는 웹 앱, 모바일 백 엔드 또는 API 앱 사용에 대 한 일반 설정을 구성 하는 방법에 설명 합니다 [Azure portal]합니다.

## <a name="configure-app-settings"></a>앱 설정 구성

App Service에서 앱 설정 환경 변수 처럼 사용할 수 있습니다. 에 [Azure portal], 앱의 관리 페이지로 이동 합니다. 앱의 왼쪽된 메뉴에서 클릭 **Configuration** > **응용 프로그램 설정**합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET Core 개발자를 위한 App Service에서 설정을 앱 설정 설정 같습니다 `<appSettings>` 에 *Web.config*, App Service에서 값을 재정의 하지만 *Web.config*합니다. 개발 설정 (예: 로컬 MySQL 암호)을 유지할 수 있습니다 *Web.config*, 하지만 프로덕션 비밀 (예: Azure MySQL 데이터베이스 암호) App Service에서 안전 하 게 합니다. 동일한 코드를 로컬로 디버깅할 때 Azure에 배포 하는 경우 프로덕션 비밀을 사용 하 여 개발 설정을 사용 합니다.

마찬가지로, 다른 언어 스택, 런타임 시 앱 설정을 환경 변수로 가져옵니다. 언어 스택에 대 한 특정 단계를 참조 하세요.

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](containers/configure-custom-container.md#configure-environment-variables)

앱 설정은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> 앱 설정에서 확인할 수도 있습니다 [Key Vault](/azure/key-vault/) 사용 하 여 [Key Vault 참조](app-service-key-vault-references.md)합니다.

### <a name="show-hidden-values"></a>숨겨진된 값 표시

기본적으로 앱 설정에 대 한 값 보안에 대 한 포털에서 숨겨집니다. 앱 설정의 숨겨진된 값을 보려면 클릭 합니다 **값** 해당 설정의 필드입니다. 모든 앱 설정의 값을 보려면 클릭 합니다 **값이 표시** 단추입니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 앱 설정을 추가 하려면 클릭 **새 응용 프로그램 설정을**합니다. 대화 상자에서 수행할 수 있습니다 [설정을 현재 슬롯에 맞도록](deploy-staging-slots.md#which-settings-are-swapped)합니다.

설정을 편집 하려면 클릭 합니다 **편집** 오른쪽에 있는 단추입니다.

완료 되 면, 클릭 **업데이트**합니다. 을 클릭 **저장할** 년대 합니다 **구성** 페이지.

> [!NOTE]
> 기본 Linux 컨테이너 또는 사용자 지정 Linux 컨테이너에서 앱 설정 이름에서 모든 중첩 된 JSON 키 구조와 같은 `ApplicationInsights:InstrumentationKey` 으로 App Service에서 구성 해야 `ApplicationInsights__InstrumentationKey` 키 이름입니다. 즉, 모든 `:` 으로 대체 해야 `__` (이중 밑줄).
>

### <a name="edit-in-bulk"></a>대량 편집

를 추가 하거나 대량에서 앱 설정을 편집 하려면 클릭 합니다 **고급 편집** 단추입니다. 완료 되 면, 클릭 **업데이트**합니다. 을 클릭 **저장할** 년대 합니다 **구성** 페이지.

앱 설정에는 다음 JSON 형식 지정

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>연결 문자열 구성

에 [Azure portal], 앱의 관리 페이지로 이동 합니다. 앱의 왼쪽된 메뉴에서 클릭 **Configuration** > **응용 프로그램 설정**합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET Core 개발자를 위한 App service에서 설정 연결 문자열 설정 같습니다 `<connectionStrings>` 에 *Web.config*, App Service에서 설정 된 값에서 재정의 하지만 *Web.config*. 개발 설정 (예: 데이터베이스 파일)를 유지할 수 있습니다 *Web.config* 및 App Service에서 안전 하 게 프로덕션 비밀 (예: SQL Database 자격 증명)입니다. 동일한 코드를 로컬로 디버깅할 때 Azure에 배포 하는 경우 프로덕션 비밀을 사용 하 여 개발 설정을 사용 합니다.

다른 언어 스택에 대 한 것이 좋습니다는 데 [앱 설정](#configure-app-settings) 값에 액세스 하려면 연결 문자열에 변수 키에 특수 형식이 필요 하기 때문에 정렬 하는 대신 합니다. 그러나 단, 다음과 같습니다: Azure database 형식 특정 백업 앱과 함께 앱에서 해당 연결 문자열을 구성 하는 경우. 자세한 내용은 [백업 대상](manage-backup.md#what-gets-backed-up)합니다. 이 자동화 된 백업을 사용 하지 않아도 앱 설정을 사용 합니다.

런타임에 연결 문자열은 다음의 연결 유형을 접두사로 환경 변수로 사용할 수 있습니다.

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* 사용자 지정: `CUSTOMCONNSTR_`

예를 들어 MySql 연결 문자열 이름은 *connectionstring1* 환경 변수로 액세스할 수 있습니다 `MYSQLCONNSTR_connectionString1`합니다. 언어 스택에 대 한 특정 단계를 참조 하세요.

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](containers/configure-custom-container.md#configure-environment-variables)

연결 문자열은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> 연결 문자열에서 확인할 수도 있습니다 [Key Vault](/azure/key-vault/) 사용 하 여 [Key Vault 참조](app-service-key-vault-references.md)합니다.

### <a name="show-hidden-values"></a>숨겨진된 값 표시

기본적으로 연결 문자열에 대 한 값 보안에 대 한 포털에서 숨겨집니다. 연결 문자열의 숨겨진된 값을 보려면 클릭 합니다 **값** 문자열 필드입니다. 모든 연결 문자열의 값을 보려면 클릭 합니다 **값이 표시** 단추입니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 연결 문자열을 추가 하려면 클릭 **새 연결 문자열**합니다. 대화 상자에서 수행할 수 있습니다 [연결 문자열을 현재 슬롯에 맞도록](deploy-staging-slots.md#which-settings-are-swapped)합니다.

설정을 편집 하려면 클릭 합니다 **편집** 오른쪽에 있는 단추입니다.

완료 되 면, 클릭 **업데이트**합니다. 을 클릭 **저장할** 년대 합니다 **구성** 페이지.

### <a name="edit-in-bulk"></a>대량 편집

를 추가 하거나 대량의 연결 문자열을 편집 하려면 클릭 합니다 **고급 편집** 단추입니다. 완료 되 면, 클릭 **업데이트**합니다. 을 클릭 **저장할** 년대 합니다 **구성** 페이지.

연결 문자열에는 다음 JSON 형식 지정

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>일반 설정 구성

에 [Azure portal], 앱의 관리 페이지로 이동 합니다. 앱의 왼쪽된 메뉴에서 클릭 **Configuration** > **응용 프로그램 설정**합니다.

![일반 설정](./media/configure-common/open-general.png)

여기에서 앱에 대 한 몇 가지 일반적인 설정을 구성할 수 있습니다. 일부 설정은 필요가 [높은 가격 책정 계층으로 강화](web-sites-scale.md)합니다.

- **설정을 스택**: 언어 및 SDK 버전을 포함 하 여 앱을 실행 소프트웨어 스택. Linux 앱 및 사용자 지정 컨테이너 앱에 대 한 선택적 시작 명령을 또는 파일을 설정할 수 있습니다.
- **플랫폼 설정**: 호스팅 플랫폼을 위한 설정을 구성할 수 있습니다 포함:
    - **비트**: 32 비트 또는 64 비트입니다.
    - **WebSocket 프로토콜**: 에 대 한 [ASP.NET SignalR] 하거나 [socket.io](https://socket.io/)예를 들어 있습니다.
    - **Always On**: 트래픽이 없는 경우에 로드 앱을 유지 합니다. 연속 webjob 또는 CRON 식을 사용 하 여 트리거되는 WebJobs를 사용 하도록 설정 해야 합니다.
    - **관리 되는 파이프라인 버전**: IIS [파이프라인 모드]합니다. 설정할 **클래식** 이전 버전의 IIS 필요로 하는 레거시 앱이 있는 경우.
    - **HTTP 버전**: [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 프로토콜에 대한 지원을 사용하도록 **2.0**으로 설정합니다.
    > [!NOTE]
    > 대부분의 최신 브라우저는 TLS를 통해서만 HTTP/2 프로토콜을 지원하는 반면에 암호화되지 않은 트래픽은 HTTP/1.1을 계속 사용합니다. 클라이언트 브라우저에 연결 HTTP/2를 사용 하 여 앱 중 하나 [App Service Certificate 구입](web-sites-purchase-ssl-web-site.md) 앱의 사용자 지정 도메인에 대 한 또는 [타사 인증서 바인딩](app-service-web-tutorial-custom-ssl.md)합니다.
    - **ARR 선호도**: 다중 인스턴스 배포의 경우 클라이언트 세션의 수명 동안 동일한 인스턴스에 라우팅되는지 확인 합니다. 이 옵션을 설정할 수 있습니다 **해제** 상태 비저장 응용 프로그램에 대 한 합니다.
- **디버깅**: 에 대 한 원격 디버깅을 사용 [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)하십시오 [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), 또는 [Node.js](containers/configure-language-nodejs.md#debug-remotely) 앱. 이 옵션은 48 시간 후 자동으로 해제 합니다.
- **들어오는 클라이언트 인증서**:에서 클라이언트 인증서가 필요 [상호 인증](app-service-web-configure-tls-mutual-auth.md)합니다.

## <a name="configure-default-documents"></a>기본 문서 구성

이 설정은 Windows 앱에 대해서만입니다.

에 [Azure portal], 앱의 관리 페이지로 이동 합니다. 앱의 왼쪽된 메뉴에서 클릭 **Configuration** > **기본 문서**합니다.

![일반 설정](./media/configure-common/open-documents.png)

기본 문서에는 웹 사이트의 루트 URL에 표시 되는 웹 페이지입니다. 목록에서 첫 번째로 일치되는 파일이 사용됩니다. 새 기본 문서를 추가 하려면 클릭 **새 문서**합니다. 클릭 잊지 **저장할**합니다.

앱에서 정적 콘텐츠를 제공 하는 대신 URL 기반 라우팅 모듈을 사용 하는 경우 기본 문서에 대 한 않아도가 됩니다.

## <a name="configure-path-mappings"></a>경로 매핑 구성

에 [Azure portal], 앱의 관리 페이지로 이동 합니다. 앱의 왼쪽된 메뉴에서 클릭 **Configuration** > **경로 매핑**합니다.

![일반 설정](./media/configure-common/open-path.png)

합니다 **경로 매핑** 페이지 표시 OS 유형에 따라 다릅니다.

### <a name="windows-apps-uncontainerized"></a>Windows 앱 (uncontainerized)

Windows 앱에 대 한 IIS 처리기 매핑 및 가상 응용 프로그램 및 디렉터리를 지정할 수 있습니다.

처리기 매핑에서는 특정 파일 확장명에 대 한 요청을 처리 하도록 사용자 지정 스크립트 프로세서를 추가할 수 있습니다. 사용자 지정 처리기를 추가 하려면 클릭 **새 처리기**합니다. 처리기를 다음과 같이 구성 합니다.

- **확장명**. 와 같은 처리 하려면 파일 확장명  *\*.php* 하거나 *handler.fcgi*합니다.
- **스크립트 프로세서**합니다. 스크립트 프로세서의 절대 경로입니다. 파일 확장명과 일치 하는 파일에는 요청이 스크립트 프로세서에 의해 처리 됩니다. 경로 `D:\home\site\wwwroot` 를 사용하여 앱의 루트 디렉터리를 참조합니다.
- **인수**합니다. 스크립트 프로세서에 대 한 선택적 명령줄 인수입니다.

각 앱에 기본 루트 경로 (`/`)에 매핑된 `D:\home\site\wwwroot`, 기본적으로 코드가 배포 되는 위치입니다. 앱 루트에 다른 폴더에 되었거나 리포지토리에 둘 이상의 응용 프로그램에 있는 경우에 편집 하거나 가상 응용 프로그램 및 여기에 디렉터리를 추가할 수 있습니다. 클릭 **새 가상 응용 프로그램 또는 디렉터리**합니다.

가상 응용 프로그램 및 디렉터리를 구성 하려면 각 가상 디렉터리 및 웹 사이트 루트에 상대적인 실제 경로 지정 (`D:\home`). 경우에 따라 **애플리케이션** 확인란을 선택하여 가상 디렉터리를 애플리케이션으로 표시할 수 있습니다.

### <a name="containerized-apps"></a>컨테이너 화 된 앱

할 수 있습니다 [컨테이너 화 된 앱에 대 한 사용자 지정 저장소 추가](containers/how-to-serve-content-from-azure-storage.md)합니다. 컨테이너 화 된 앱에는 모든 Linux 앱 및 App Service에서 실행 중인 Windows 및 Linux 사용자 지정 컨테이너 포함 됩니다. 클릭 **새 Azure Storage를 탑재** 및 사용자 지정 저장소를 다음과 같이 구성 합니다.

- **이름**: 표시 이름입니다.
- **구성 옵션**: **기본적인** 나 **고급**합니다.
- **Storage 계정**: 컨테이너를 사용 하 여 원하는 저장소 계정입니다.
- **스토리지 유형**: **Azure Blob** 나 **Azure Files**합니다.
  > [!NOTE]
  > Windows 컨테이너 앱만 Azure Files를 지원합니다.
- **스토리지 컨테이너**: 기본 구성에서 컨테이너입니다.
- **공유 이름**: 고급 구성에 대 한 파일 이름을 공유 합니다.
- **액세스 키**: 고급 구성에서 액세스 키입니다.
- **탑재 경로**: 사용자 지정 저장소를 탑재 하기 위해 컨테이너의 절대 경로입니다.

자세한 내용은 [Linux의 App Service에서 Azure Storage에서 콘텐츠 제공](containers/how-to-serve-content-from-azure-storage.md)합니다.

## <a name="configure-language-stack-settings"></a>언어 스택 설정 구성

Linux 앱을 참조 하세요.

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.JS](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>사용자 지정 컨테이너를 구성 합니다.

참조 [Azure App Service에 대 한 사용자 지정 Linux 컨테이너 구성](containers/configure-custom-container.md)

## <a name="next-steps"></a>다음 단계

- [Azure App Service에서 사용자 지정 도메인 이름 구성]
- [Azure App Service에서 스테이징 환경 설정]
- [Azure App Service에서 앱에 대한 HTTPS를 사용하도록 설정]
- [진단 로그 사용](troubleshoot-diagnostic-logs.md)
- [Azure App Service에서 앱 크기 조정]
- [Azure App Service의 기본 사항 모니터링]
- [ApplicationHost.xdt 사용 하 여 applicationHost.config 설정 변경](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Azure App Service에서 사용자 지정 도메인 이름 구성]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service에서 스테이징 환경 설정]: ./deploy-staging-slots.md
[Azure App Service에서 앱에 대한 HTTPS를 사용하도록 설정]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service의 기본 사항 모니터링]: ./web-sites-monitor.md
[파이프라인 모드]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service에서 앱 크기 조정]: ./web-sites-scale.md
