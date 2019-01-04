---
title: 컨테이너 구성
titlesuffix: Face - Azure Cognitive Services
description: 컨테이너에 대한 구성 설정입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 30546d31e96d7d7fa1009f16a50fe8fda12ead67
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105107"
---
# <a name="configure-containers"></a>컨테이너 구성

Face 컨테이너는 공통 구성 프레임워크를 사용하므로 컨테이너에 대한 스토리지, 로깅 및 원격 분석, 보안 설정을 쉽게 구성하고 관리할 수 있습니다.

## <a name="configuration-settings"></a>구성 설정

Face 컨테이너의 구성 설정은 계층적이며, 모든 컨테이너는 다음과 같은 최상위 구조에 기반한 공유 계층 구조를 사용합니다.

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [인증](#authentication-configuration-settings)
* [결제](#billing-configuration-setting)
* [CloudAI](#cloudai-configuration-settings)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [로깅](#logging-configuration-settings)
* [탑재](#mounts-configuration-settings)

Face 컨테이너를 인스턴스화할 때 [환경 변수](#configuration-settings-as-environment-variables) 또는 [명령줄 인수](#configuration-settings-as-command-line-arguments)를 사용하여 구성 설정을 지정할 수 있습니다.

환경 변수 값은 명령줄 인수 값을 재정의합니다. 그러면 컨테이너 이미지에 대한 기본값이 재정의됩니다. 즉, 동일한 구성 설정(예: `Logging:Disk:LogLevel`)에 대한 환경 변수와 명령줄 인수에 다른 값을 지정한 다음, 컨테이너를 인스턴스화할 경우 해당 환경 변수의 값은 인스턴스화된 컨테이너에서 사용됩니다.

### <a name="configuration-settings-as-environment-variables"></a>환경 변수를 사용한 구성 설정

[ASP.NET Core 환경 변수 구문](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment)을 사용하여 구성 설정을 지정할 수 있습니다.

컨테이너는 컨테이너가 인스턴스화될 때 사용자 환경 변수를 읽습니다. 환경 변수가 있을 경우 환경 변수의 값은 지정된 구성 설정에 대한 기본값을 재지정합니다. 환경 변수를 사용하면 컨테이너를 인스턴스화하기 전에 여러 구성 설정을 지정할 수 있고, 여러 컨테이너에서 동일한 구성 설정 세트를 자동으로 사용할 수 있습니다.

예를 들어 다음 명령은 환경 변수를 사용하여 콘솔 로깅 수준을 [LogLevel.Information](https://msdn.microsoft.com)으로 구성한 다음, Face 컨테이너 이미지에서 컨테이너를 인스턴스화합니다. 환경 변수의 값은 기본 구성 설정을 재정의합니다.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>명령줄 인수를 사용한 구성 설정

[ASP.NET Core 명령줄 인수 구문](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments)을 사용하여 구성 설정을 지정할 수 있습니다.

다운로드된 컨테이너 이미지로부터 컨테이너를 인스턴스화하기 위해 사용하는 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령의 선택적 `ARGS` 매개 변수에서 구성 설정을 지정할 수 있습니다. 명령줄 인수를 사용하면 각 컨테이너에서 다른 사용자 지정 구성 설정 세트를 사용할 수 있다는 이점이 있습니다.

예를 들어 다음 명령은 Face 컨테이너 이미지에서 컨테이너를 인스턴스화하고, 콘솔 로깅 수준을 LogLevel.Information으로 구성하여 기본 구성 설정을 재정의합니다.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 구성 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure의 Face 리소스에 대한 구성 키를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 지정된 Face 리소스에 대한 유효한 구성 키여야 합니다.

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-configuration-setting) 구성 설정은 함께 사용되므로 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](face-how-to-install-containers.md#billing)를 참조하세요.

## <a name="applicationinsights-configuration-settings"></a>ApplicationInsights 구성 설정

`ApplicationInsights` 섹션의 구성 설정을 통해 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다. Application Insights는 코드 수준까지 컨테이너의 심층 모니터링을 제공합니다. 컨테이너의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 사용자가 보고할 때까지 기다리지 않고 컨테이너의 오류를 빠르게 식별하고 진단할 수 있습니다.

다음 표에서는 `ApplicationInsights` 섹션에서 지원되는 구성 설정을 설명합니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `InstrumentationKey` | 문자열 | 컨테이너에 대한 원격 분석 데이터가 전송되는 Application Insights 인스턴스의 계측 키입니다. 자세한 내용은 [ASP.NET Core용 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)를 참조하세요. |

## <a name="authentication-configuration-settings"></a>인증 구성 설정

`Authentication` 구성 설정은 컨테이너에 대한 Azure 보안 옵션을 제공합니다. 이 섹션의 구성 설정은 사용할 수 있지만, Face 컨테이너는 이 섹션을 사용하지 않습니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Storage` | 문자열 | 컨테이너에 대한 원격 분석 데이터가 전송되는 Application Insights 인스턴스의 계측 키입니다. 자세한 내용은 [ASP.NET Core용 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)를 참조하세요. |

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 구성 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 Face 리소스에 대한 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure의 Face 리소스에 대한 유효한 엔드포인트 URI여야 합니다.

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-configuration-setting) 구성 설정은 함께 사용되므로 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [Billing](face-how-to-install-containers.md#billing)을 참조하세요.

## <a name="cloudai-configuration-settings"></a>CloudAI 구성 설정

`CloudAI` 섹션의 구성 설정은 컨테이너에 고유한 컨테이너 관련 옵션을 제공합니다. `CloudAI` 섹션에서 Face 컨테이너에 대해 지원되는 설정과 개체는 다음과 같습니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Storage` | Object | Face 컨테이너에서 사용되는 스토리지 시나리오입니다. `Storage` 개체에 대한 스토리지 시나리오 및 관련 설정에 대한 자세한 내용은 [스토리지 시나리오 설정](#storage-scenario-settings)을 참조하세요. |

### <a name="storage-scenario-settings"></a>스토리지 시나리오 설정

Face 컨테이너는 저장되는 콘텐츠에 따라 Blob, 캐시, 메타데이터 및 큐 데이터를 저장합니다. 예를 들어 대규모 사용자 그룹에 대한 학습 인덱스 및 결과는 Blob 데이터로 저장됩니다. Face 컨테이너에서 이러한 형식의 데이터와 상호 작용하고 저장할 때 제공하는 별도의 두 가지 스토리지 시나리오는 다음과 같습니다.

* 메모리  
  네 가지 형식의 데이터는 모두 메모리에 저장됩니다. 이러한 데이터는 배포되지도 않고 유지되지도 않습니다. Face 컨테이너가 중지되거나 제거되면 해당 컨테이너에 대한 스토리지에 있는 모든 데이터가 삭제됩니다.  
  이는 Face 컨테이너에 대한 기본 스토리지 시나리오입니다.
* Azure  
  Face 컨테이너는 Azure Storage와 Azure Cosmos DB를 사용하여 이러한 네 가지 형식의 데이터를 영구 스토리지에 배포합니다. Blob 및 큐 데이터는 Azure Storage에서 처리됩니다. 메타데이터 및 캐시 데이터는 MongoDB API를 사용하여 Azure Cosmos DB에서 처리됩니다. Face 컨테이너가 중지되거나 제거되면 해당 컨테이너에 대한 스토리지에 있는 모든 데이터가 Azure Storage 및 Azure Cosmos DB에 저장되어 유지됩니다.  
  Azure Storage 시나리오에서 사용되는 리소스에는 다음과 같은 추가 요구 사항이 있습니다.
  * Azure Storage 리소스는 StorageV2 계정 종류를 사용해야 합니다.
  * Azure Cosmos DB 리소스는 MongoDB API를 사용해야 합니다.

스토리지 시나리오 및 관련 구성 설정은 `CloudAI` 구성 섹션의 `Storage` 개체에서 관리됩니다. `Storage` 개체에서 사용할 수 있는 구성 설정은 다음과 같습니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `StorageScenario` | 문자열 | 컨테이너에서 지원되는 스토리지 시나리오입니다. 사용할 수 있는 값은 다음과 같습니다.<br/>`Memory` - 기본값입니다. 일시적인 단일 노드 사용을 위해 컨테이너에서 비영구, 비분산 및 메모리 내 스토리지를 사용합니다. 컨테이너가 중지되거나 제거되면 해당 컨테이너의 스토리지가 삭제됩니다.<br/>`Azure` - 컨테이너에서 스토리지에 대한 Azure 리소스를 사용합니다. 컨테이너가 중지되거나 제거되더라도 해당 컨테이너의 스토리지가 유지됩니다.|
| `ConnectionStringOfAzureStorage` | 문자열 | 컨테이너에서 사용되는 Azure Storage 리소스에 대한 연결 문자열입니다.<br/>`Azure`가 `StorageScenario` 구성 설정으로 지정된 경우에만 이 설정이 적용됩니다. |
| `ConnectionStringOfCosmosMongo` | 문자열 | 컨테이너에서 사용되는 Azure Cosmos DB 리소스에 대한 MongoDB 연결 문자열입니다.<br/>`Azure`가 `StorageScenario` 구성 설정으로 지정된 경우에만 이 설정이 적용됩니다. |

예를 들어 다음 명령은 Azure Storage 시나리오를 지정하고, Face 컨테이너에 대한 데이터를 저장하는 데 사용되는 Azure Storage 및 Cosmos DB 리소스에 대한 연결 문자열 샘플을 제공합니다.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

스토리지 시나리오는 입력 탑재 및 출력 탑재와는 별도로 처리됩니다. 단일 컨테이너에 대해 이러한 기능의 조합을 지정할 수 있습니다. 예를 들어 다음 명령은 호스트 머신의 `D:\Output` 폴더에 대한 Docker 바인딩 탑재를 출력 탑재로 정의한 다음, Face 컨테이너 이미지에서 컨테이너를 인스턴스화하여 JSON 형식의 로그 파일을 출력 탑재에 저장합니다. 또한 이 명령은 Azure Storage 시나리오를 지정하고, Face 컨테이너에 대한 데이터를 저장하는 데 사용되는 Azure Storage 및 Cosmos DB 리소스에 대한 연결 문자열 샘플을 제공합니다.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-configuration-setting"></a>Eula 구성 설정

`Eula` 컨테이너 설정은 컨테이너에 대한 라이선스를 수락했음을 나타냅니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 `accept`로 설정해야 합니다.

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-configuration-setting) 구성 설정은 함께 사용됩니다. 따라서 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](face-how-to-install-containers.md#billing)를 참조하세요.

Cognitive Services 컨테이너는 Azure 사용에 관한 [계약](https://go.microsoft.com/fwlink/?linkid=2018657)에 따라 사용 허가됩니다. Azure 사용에 관한 기존 계약이 없으면 Azure 사용에 관한 계약은 [온라인 서비스 사용 약관](https://go.microsoft.com/fwlink/?linkid=2018760)을 포함한 [Microsoft 온라인 정기가입 계약](https://go.microsoft.com/fwlink/?linkid=2018755)임에 동의합니다. 미리 보기의 경우 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://go.microsoft.com/fwlink/?linkid=2018815)에도 동의합니다. 컨테이너를 사용하여 이 사용 약관에 동의합니다.

## <a name="fluentd-configuration-settings"></a>Fluentd 구성 설정

`Fluentd` 섹션은 통합 로깅을 위한 오픈 소스 데이터 수집기인 [Fluentd](https://www.fluentd.org)에 대한 구성 설정을 관리합니다. Face 컨테이너에는 Fluentd 로깅 공급자가 포함되어 있어 컨테이너에서 로그 및 선택적으로 메트릭 데이터를 Fluentd 서버에 쓸 수 있습니다.

다음 표에서는 `Fluentd` 섹션에서 지원되는 구성 설정을 설명합니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Host` | 문자열 | Fluentd 서버의 IP 주소 또는 DNS 호스트 이름입니다. |
| `Port` | 정수  | Fluentd 서버의 포트입니다.<br/> 기본값은 24224입니다. |
| `HeartbeatMs` | 정수  | 하트비트 간격(밀리초)입니다. 이 간격이 만료되기 전에 전송된 이벤트 트래픽이 없을 경우 하트 비트는 Fluentd 서버로 전송됩니다. 기본값은 60000밀리초(1분)입니다. |
| `SendBufferSize` | 정수  | 전송 작업에 할당된 네트워크 버퍼 공간(바이트)입니다. 기본값은 32768바이트(32킬로바이트)입니다. |
| `TlsConnectionEstablishmentTimeoutMs` | 정수  | Fluentd 서버와 SSL/TLS 연결을 설정하기 위한 시간 제한(밀리초)입니다. 기본값은 10000밀리초(10초)입니다.<br/> `UseTLS`가 false로 설정되어 있는 경우 이 값은 무시됩니다. |
| `UseTLS` | BOOLEAN | 컨테이너가 Fluentd 서버와 통신하기 위해 SSL/TLS를 사용해야 하는지 여부를 나타냅니다. 기본값은 False입니다. |

## <a name="logging-configuration-settings"></a>로깅 구성 설정

`Logging` 구성 설정은 컨테이너에 대한 ASP.NET Core 로깅 지원을 관리합니다. ASP.NET Core 애플리케이션에 사용할 수 있는 컨테이너에 대해 동일한 구성 설정 및 값을 사용할 수 있습니다. Face 컨테이너에서 지원되는 로깅 공급자는 다음과 같습니다.

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.
* [디버그](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.
* 디스크  
  JSON 로깅 공급자입니다. 이 로깅 공급자는 출력 탑재에 로그 데이터를 씁니다.  
  `Disk` 로깅 공급자는 다음과 같은 구성 설정을 지원합니다.  

  | 이름 | 데이터 형식 | 설명 |
  |------|-----------|-------------|
  | `Format` | 문자열 | 로그 파일에 대한 출력 형식입니다.<br/> **참고:** 이 값은 `json`으로 설정해야 로깅 공급자를 사용할 수 있습니다. 또한 컨테이너를 인스턴스화하는 동안 출력 탑재를 지정하지 않고 이 값이 지정된 경우에는 오류가 발생합니다. |
  | `MaxFileSize` | 정수  | 로그 파일의 최대 크기(MB)입니다. 현재 로그 파일의 크기가 이 값을 충족하거나 초과할 경우 로깅 공급자가 새 로그 파일을 시작합니다. -1을 지정하면 로그 파일의 크기는 출력 탑재(있는 경우)에 대한 최대 파일 크기에 의해서만 제한됩니다. 기본값은 1입니다. |

ASP.NET Core 로깅 지원 구성에 대한 자세한 내용은 [설정 파일 구성](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)을 참조하세요.

## <a name="mounts-configuration-settings"></a>Mounts 구성 설정

Face에서 제공하는 Docker 컨테이너는 상태를 저장하지 않으며 변경할 수 없도록 설계되었습니다. 즉, 컨테이너 내부에서 만들어진 파일은 쓰기 가능한 컨테이너 계층에 저장되어 컨테이너가 실행되는 동안만 유지되며 쉽게 액세스할 수 없습니다. 해당 컨테이너가 중지 또는 제거되면 해당 컨테이너 내부에 만들어진 파일은 소멸됩니다.

그러나 이들은 Docker 컨테이너이므로 볼륨 및 바인드 탑재 등의 Docker 스토리지 옵션을 사용하여 컨테이너 외부의 지속된 데이터를 읽고 쓸 수 있습니다(해당 컨테이너가 지원하는 경우). Docker 스토리지 옵션을 지정 및 관리하는 방법에 대한 자세한 내용은 [Docker에서 데이터 관리](https://docs.docker.com/storage/)를 참조하세요.

> [!NOTE]
> 일반적으로 이러한 구성 설정에 대한 값은 변경할 필요가 없습니다. 대신 컨테이너에 대한 입력 및 출력 탑재 지정 시 대상으로 이러한 구성 설정에서 지정한 값을 사용하게 됩니다. 입력 및 출력 탑재 지정 방법에 대한 자세한 내용은 [입력 및 출력 탑재](#input-and-output-mounts)를 참조하세요.

다음 표에서는 `Mounts` 섹션에서 지원되는 구성 설정을 설명합니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Input` | 문자열 | 입력 탑재의 대상입니다. 기본값은 `/input`입니다. |
| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. |

### <a name="input-and-output-mounts"></a>입력 및 출력 탑재

기본적으로 각 컨테이너는 컨테이너에서 데이터를 읽을 수 있는 *입력 탑재* 및 컨테이너에서 데이터를 쓸 수 있는 *출력 탑재*를 지원할 수 있습니다. 그러나 컨테이너는 입력 또는 출력 탑재를 지원할 필요가 없으며, 각 컨테이너는 Face 컨테이너에서 지원하는 로깅 옵션 외에도 컨테이너 특정 목적에 맞게 입력 및 출력 탑재를 모두 사용할 수 있습니다.

Face 컨테이너는 필요에 따라 출력 탑재를 지원하며 입력 탑재는 지원하지 않습니다.

다운로드한 컨테이너 이미지에서 컨테이너를 인스턴스화하는 데 사용되는 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령에서 `--mount` 옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다. 기본적으로 입력 탑재는 대상으로 `/input`을 사용하며 출력 탑재는 대상으로 `/output`을 사용합니다. Docker 컨테이너 호스트에서 사용할 수 있는 모든 Docker 스토리지 옵션은 `--mount` 옵션에서 지정할 수 있습니다.

예를 들어 다음 명령은 호스트 머신의 `D:\Output` 폴더에 대한 Docker 바인딩 탑재를 출력 탑재로 정의한 다음, Face 컨테이너 이미지에서 컨테이너를 인스턴스화하여 JSON 형식의 로그 파일을 출력 탑재에 저장합니다.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```

Face 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 데이터베이스 데이터를 저장하지 않습니다. 대신, Face 컨테이너에서 스토리지 시나리오를 제공하여 학습 및 데이터베이스 데이터를 관리합니다. 스토리지 시나리오를 사용하는 방법에 대한 자세한 내용은 [스토리지 시나리오 설정](#storage-scenario-settings)을 참조하세요.
