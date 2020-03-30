---
title: 포털에서 앱 구성
description: Azure 포털에서 앱 서비스 앱에 대한 공통 설정을 구성하는 방법을 알아봅니다. 앱 설정, 연결 문자열, 플랫폼, 언어 스택, 컨테이너 등
keywords: azure 앱 서비스, 웹 앱, 앱 설정, 환경 변수
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: ce0a170a629f347e2687a2e9f63fb3438fe2bd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280171"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Azure 포털에서 앱 서비스 앱 구성

이 항목에서는 [Azure 포털을]사용하여 웹 앱, 모바일 백 엔드 또는 API 앱에 대한 공통 설정을 구성하는 방법을 설명합니다.

## <a name="configure-app-settings"></a>앱 설정 구성

앱 서비스에서 앱 설정은 응용 프로그램 코드에 환경 변수로 전달되는 변수입니다. Linux 앱 및 사용자 지정 컨테이너의 경우 App `--env` Service는 플래그를 사용하여 컨테이너에 앱 설정을 전달하여 컨테이너의 환경 변수를 설정합니다.

Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 

![앱 서비스 검색](./media/configure-common/search-for-app-services.png)

앱의 왼쪽 메뉴에서 **구성** > **응용 프로그램 설정을**선택합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET 핵심 개발자의 경우 앱 서비스에서 앱 `<appSettings>` 설정을 설정하는 것은 *Web.config* 또는 *appsettings.json에서*설정하는 것과 같지만 앱 서비스의 값은 *Web.config* 또는 *appsettings.json의*값을 재정의합니다. *Web.config* 또는 *appsettings.json에서*개발 설정(예: 로컬 MySQL 암호)을 유지할 수 있지만 앱 서비스에서는 프로덕션 비밀(예: Azure MySQL 데이터베이스 암호)을 안전하게 유지할 수 있습니다. 동일한 코드는 로컬로 디버깅할 때 개발 설정을 사용하며 Azure에 배포할 때 프로덕션 암호를 사용합니다.

다른 언어 스택도 마찬가지로 런타임에 환경 변수로 앱 설정을 가져옵니다. 언어 스택 특정 단계는 다음을 참조하십시오.

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [루비](containers/configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](containers/configure-custom-container.md#configure-environment-variables)

앱 설정은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> 키 볼트 참조를 [사용하여](app-service-key-vault-references.md) [키 볼트에서](/azure/key-vault/) 앱 설정을 확인할 수도 있습니다.

### <a name="show-hidden-values"></a>숨겨진 값 표시

기본적으로 앱 설정값은 보안을 위해 포털에 숨김이 있습니다. 앱 설정의 숨겨진 값을 보려면 해당 설정의 **값** 필드를 클릭합니다. 모든 앱 설정의 값을 보려면 **값 표시** 버튼을 클릭합니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 앱 설정을 추가하려면 **새 응용 프로그램 설정을**클릭합니다. 대화 상자에서 설정을 [현재 슬롯에 고정할](deploy-staging-slots.md#which-settings-are-swapped)수 있습니다.

설정을 편집하려면 오른쪽에 있는 **편집** 단추를 클릭합니다.

완료되면 **업데이트를**클릭합니다. **구성** 페이지에서 다시 **저장을** 클릭하는 것을 잊지 마십시오.

> [!NOTE]
> 기본 Linux 컨테이너 또는 사용자 지정 Linux 컨테이너에서 앱 설정 이름에 중첩된 JSON 키 구조는 키 이름과 같이 `ApplicationInsights:InstrumentationKey` `ApplicationInsights__InstrumentationKey` 앱 서비스에서 구성해야 합니다. 즉, 모든 `:` 것을 (이중 `__` 밑줄)으로 대체해야합니다.
>

### <a name="edit-in-bulk"></a>일괄 편집

앱 설정을 일괄추가하거나 편집하려면 **고급 편집** 버튼을 클릭합니다. 완료되면 **업데이트를**클릭합니다. **구성** 페이지에서 다시 **저장을** 클릭하는 것을 잊지 마십시오.

앱 설정에는 다음과 같은 JSON 서식이 있습니다.

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

Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **응용 프로그램 설정을**선택합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET 핵심 개발자의 경우 앱 서비스에서 연결 문자열을 설정하는 것은 `<connectionStrings>` *Web.config에서*설정하지만 앱 서비스에서 설정한 값은 *Web.config*에서 설정합니다. *Web.config* 및 프로덕션 암호(예: SQL Database 자격 증명)에 개발 설정(예: 데이터베이스 파일)을 앱 서비스에서 안전하게 유지할 수 있습니다. 동일한 코드는 로컬로 디버깅할 때 개발 설정을 사용하며 Azure에 배포할 때 프로덕션 암호를 사용합니다.

다른 언어 스택의 경우 연결 문자열은 값에 액세스하기 위해 변수 키에 특별한 서식이 필요하므로 [앱 설정을](#configure-app-settings) 대신 사용하는 것이 좋습니다. 그러나 한 가지 예외는 앱에서 연결 문자열을 구성하는 경우 특정 Azure 데이터베이스 유형이 앱과 함께 백업됩니다. 자세한 내용은 [백업되는 내용을](manage-backup.md#what-gets-backed-up)참조하십시오. 이 자동 백업이 필요하지 않은 경우 앱 설정을 사용합니다.

런타임시 연결 문자열은 다음 연결 유형과 함께 접두사에 있는 환경 변수로 사용할 수 있습니다.

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* 사용자 지정: `CUSTOMCONNSTR_`

예를 *들어, 연결 string1라는* MySql 연결 문자열은 환경 `MYSQLCONNSTR_connectionString1`변수로 액세스할 수 있습니다. 언어 스택 특정 단계는 다음을 참조하십시오.

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [루비](containers/configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](containers/configure-custom-container.md#configure-environment-variables)

연결 문자열은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> 연결 문자열은 키 볼트 [참조를](app-service-key-vault-references.md)사용하여 [키 볼트에서](/azure/key-vault/) 확인할 수도 있습니다.

### <a name="show-hidden-values"></a>숨겨진 값 표시

기본적으로 연결 문자열의 값은 보안을 위해 포털에 숨김이 있습니다. 연결 문자열의 숨겨진 값을 보려면 해당 문자열의 **값** 필드를 클릭합니다. 모든 연결 문자열의 값을 보려면 **값 표시** 단추를 클릭합니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 연결 문자열을 추가하려면 **새 연결 문자열을**클릭합니다. 대화 상자에서 연결 [문자열을 현재 슬롯에 고정할](deploy-staging-slots.md#which-settings-are-swapped)수 있습니다.

설정을 편집하려면 오른쪽에 있는 **편집** 단추를 클릭합니다.

완료되면 **업데이트를**클릭합니다. **구성** 페이지에서 다시 **저장을** 클릭하는 것을 잊지 마십시오.

### <a name="edit-in-bulk"></a>일괄 편집

연결 문자열을 일괄으로 추가하거나 편집하려면 **고급 편집** 단추를 클릭합니다. 완료되면 **업데이트를**클릭합니다. **구성** 페이지에서 다시 **저장을** 클릭하는 것을 잊지 마십시오.

연결 문자열에는 다음과 같은 JSON 서식이 있습니다.

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

Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **일반 설정을**선택합니다.

![일반 설정](./media/configure-common/open-general.png)

여기에서 앱에 대한 몇 가지 일반적인 설정을 구성할 수 있습니다. 일부 설정에서는 [더 높은 가격 책정 계층으로 확장해야 합니다.](manage-scale-up.md)

- **스택 설정**: 언어 및 SDK 버전을 포함하여 응용 프로그램을 실행하는 소프트웨어 스택입니다. Linux 앱 및 사용자 지정 컨테이너 앱의 경우 선택적 시작 명령 또는 파일을 설정할 수도 있습니다.
- **플랫폼 설정**: 다음을 포함하여 호스팅 플랫폼에 대한 설정을 구성할 수 있습니다.
    - **음이 :** 32 비트 또는 64 비트.
    - **WebSocket 프로토콜**: 예를 들어 [ASP.NET 시그널R] 또는 [socket.io.](https://socket.io/)
    - **항상 켜짐**: 트래픽이 없는 경우에도 앱을 로드된 상태로 유지합니다. 연속 WebJobs 또는 CRON 식을 사용하여 트리거되는 WebJobs에 필요합니다.
      > [!NOTE]
      > Always On 기능을 사용하면 끝점을 제어할 수 없습니다. 항상 응용 프로그램 루트에 요청을 보냅니다.
    - **관리되는 파이프라인 버전**: IIS [파이프라인 모드]. 이전 버전의 IIS가 필요한 레거시 앱이 있는 경우 **클래식으로** 설정합니다.
    - **HTTP 버전**: [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 프로토콜을 지원하려면 **2.0으로** 설정합니다.
    > [!NOTE]
    > 대부분의 최신 브라우저는 TLS를 통해서만 HTTP/2 프로토콜을 지원하는 반면에 암호화되지 않은 트래픽은 HTTP/1.1을 계속 사용합니다. 클라이언트 브라우저가 HTTP/2로 앱에 연결되도록 하려면 [Azure 앱 서비스에서 SSL 바인딩으로 사용자 지정 DNS 이름을 보호합니다.](configure-ssl-bindings.md)
    - **ARR 선호도**: 다중 인스턴스 배포에서 클라이언트가 세션 수명 동안 동일한 인스턴스로 라우팅되도록 합니다. 상태 비해제 응용 프로그램의 경우 이 옵션을 **Off로** 설정할 수 있습니다.
- **디버깅**: [ASP.NET,](troubleshoot-dotnet-visual-studio.md#remotedebug) [ASP.NET 코어](/visualstudio/debugger/remote-debugging-azure)또는 Node.js 앱에 대한 원격 디버깅을 [활성화합니다.](containers/configure-language-nodejs.md#debug-remotely) 이 옵션은 48시간 후에 자동으로 꺼집니다.
- **들어오는 클라이언트 인증서**: [상호 인증에서](app-service-web-configure-tls-mutual-auth.md)클라이언트 인증서가 필요합니다.

## <a name="configure-default-documents"></a>기본 문서 구성

이 설정은 Windows 앱에만 해당됩니다.

Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 앱의 왼쪽 메뉴에서**기본 문서** **구성을** > 선택합니다.

![기본 문서](./media/configure-common/open-documents.png)

기본 문서는 웹 사이트의 루트 URL에 표시되는 웹 페이지입니다. 목록에서 첫 번째로 일치되는 파일이 사용됩니다. 새 기본 문서를 추가하려면 **새 문서를**클릭합니다. **저장을**클릭하는 것을 잊지 마십시오.

앱에서 정적 콘텐츠를 제공하는 대신 URL을 기반으로 라우팅하는 모듈을 사용하는 경우 기본 문서가 필요하지 않습니다.

## <a name="configure-path-mappings"></a>경로 매핑 구성

Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **경로 매핑을 선택합니다.**

![경로 매핑](./media/configure-common/open-path.png)

**경로 매핑** 페이지에는 OS 유형에 따라 다른 사항이 표시됩니다.

### <a name="windows-apps-uncontainerized"></a>Windows 앱(컨테이너화되지 않은)

Windows 앱의 경우 IIS 처리기 매핑 및 가상 응용 프로그램 및 디렉터리를 사용자 지정할 수 있습니다.

처리기 매핑을 사용하면 특정 파일 확장에 대한 요청을 처리하기 위해 사용자 지정 스크립트 프로세서를 추가할 수 있습니다. 사용자 지정 처리기를 추가하려면 **새 처리기를 클릭합니다.** 다음과 같이 처리기를 구성합니다.

- **확장**. * \*.php* 또는 *handler.fcgi*와 같이 처리하려는 파일 확장명 .
- **스크립트 프로세서**. 스크립트 프로세서의 절대 경로입니다. 파일 확장과 일치하는 파일에 대한 요청은 스크립트 프로세서에서 처리됩니다. 경로 `D:\home\site\wwwroot` 를 사용하여 앱의 루트 디렉터리를 참조합니다.
- **인수**. 스크립트 프로세서에 대한 선택적 명령줄 인수입니다.

각 앱에는 코드가 기본적으로`/`배포되는 `D:\home\site\wwwroot`기본 루트 경로() 가 매핑됩니다. 앱 루트가 다른 폴더에 있거나 리포지토리에 두 개 이상의 응용 프로그램이 있는 경우 여기에서 가상 응용 프로그램 및 디렉터리를 편집하거나 추가할 수 있습니다. **새 가상 응용 프로그램 또는 디렉터리**를 클릭합니다.

가상 응용 프로그램 및 디렉터리를 구성하려면 각 가상 디렉터리와 웹`D:\home`사이트 루트()를 기준으로 해당 물리적 경로를 지정합니다. 경우에 따라 **애플리케이션** 확인란을 선택하여 가상 디렉터리를 애플리케이션으로 표시할 수 있습니다.

### <a name="containerized-apps"></a>컨테이너화된 앱

[컨테이너화된 앱에 대한 사용자 지정 저장소를 추가할 수 있습니다.](containers/how-to-serve-content-from-azure-storage.md) 컨테이너화된 앱에는 모든 Linux 앱과 앱 서비스에서 실행되는 Windows 및 Linux 사용자 지정 컨테이너가 포함됩니다. **새 Azure 저장소 마운트를** 클릭하고 다음과 같이 사용자 지정 저장소를 구성합니다.

- **이름**: 표시 이름입니다.
- **구성 옵션**: **기본** 또는 **고급**.
- **저장소 계정**: 원하는 컨테이너가 있는 저장소 계정입니다.
- **저장소 유형**: **Azure Blob** 또는 **Azure 파일**.
  > [!NOTE]
  > Windows 컨테이너 앱은 Azure 파일만 지원합니다.
- **저장소 컨테이너**: 기본 구성의 경우 원하는 컨테이너입니다.
- **이름 공유**: 고급 구성의 경우 파일 공유 이름입니다.
- **액세스 키**: 고급 구성의 경우 액세스 키입니다.
- **마운트 경로**: 사용자 지정 저장소를 마운트하는 컨테이너의 절대 경로입니다.

자세한 내용은 [App Service on Linux에 있는 Azure Storage의 콘텐츠 제공](containers/how-to-serve-content-from-azure-storage.md)을 참조하세요.

## <a name="configure-language-stack-settings"></a>언어 스택 설정 구성

Linux 앱의 경우 다음을 참조하십시오.

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [Php](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [루비](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>사용자 지정 컨테이너 구성

[Azure 앱 서비스에 대한 사용자 지정 Linux 컨테이너 구성](containers/configure-custom-container.md) 을 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 앱 서비스에서 사용자 지정 도메인 이름 구성]
- [Azure App Service에서 스테이징 환경 설정]
- [Azure App Service에서 SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
- [진단 로그 활성화](troubleshoot-diagnostic-logs.md)
- [Azure App Service에서 앱 크기 조정]
- [Azure App Service의 기본 사항 모니터링]
- [응용 프로그램Host.xdt를 사용 하 여 응용 프로그램Host.config 설정 변경](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 포털]: https://portal.azure.com/
[Azure 앱 서비스에서 사용자 지정 도메인 이름 구성]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service에서 스테이징 환경 설정]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service의 기본 사항 모니터링]: ./web-sites-monitor.md
[파이프라인 모드]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service에서 앱 크기 조정]: ./manage-scale-up.md
