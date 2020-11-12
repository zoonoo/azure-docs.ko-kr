---
title: Microsoft.applicationinsights.snapshotcollector NuGet 패키지에 대 한 릴리스 정보-Application Insights
description: Application Insights 스냅숏 디버거에서 사용 하는 Microsoft.applicationinsights.snapshotcollector NuGet 패키지에 대 한 릴리스 정보입니다.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 89c13566c3710e56a4cd737d9aa03c6fb57edc93
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542731"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Microsoft.applicationinsights.snapshotcollector에 대 한 릴리스 정보입니다.

이 문서에는 Application Insights 스냅숏 디버거에서 사용 하는 .NET 응용 프로그램에 대 한 Microsoft.applicationinsights.snapshotcollector NuGet 패키지에 대 한 릴리스 정보가 포함 되어 있습니다.

.NET 응용 프로그램에 대 한 Application Insights 스냅숏 디버거에 대해 [자세히 알아보세요](./snapshot-debugger.md) .

버그 보고서 및 피드백의 경우 GitHub에서 문제를 엽니다. https://github.com/microsoft/ApplicationInsights-dotnet

## <a name="release-notes"></a>릴리스 정보

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
여러 가지 심각한 문제를 해결 하기 위한 지점 릴리스입니다.
### <a name="bug-fixes"></a>버그 수정
- 1.3.6에서 기호 검색 알고리즘을 변경할 때 손상 된 wwwroot/bin 폴더의 PDB 검색을 수정 했습니다.
- 원격 분석에서 잡음이 ExtractWasCalledMultipleTimesException 수정 되었습니다.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>변경
Microsoft.applicationinsights.snapshotcollector의 netcoreapp1.0 2.0 대상은 AspNetCore >= 2.1.1 (다시)에 따라 달라 집니다. 이렇게 하면 1.3.5 전의 동작으로 되돌아갑니다. 1.3.6에서 업그레이드 하려고 했지만 일부 Azure App Service 시나리오는 중단 되었습니다.
### <a name="new-features"></a>새 기능
Snapshot Collector는 APPLICATIONINSIGHTS_CONNECTION_STRING 환경 변수 또는 TelemetryConfiguration에서 ConnectionString을 읽고 구문 분석 합니다. 주로이는 스냅숏 서비스에 연결 하기 위한 끝점을 설정 하는 데 사용 됩니다. 자세한 내용은 [연결 문자열 설명서](./sdk-connection-string.md)를 참조 하세요.
### <a name="bug-fixes"></a>버그 수정
- 호환 되지 않는 SecurityProtocol (TLS 1.2 필요)으로 인해 일부 환경에서 WebRequest가 실패 했기 때문에 net45를 제외한 모든 대상에 대해 HttpClient를 사용 하도록 전환 되었습니다.

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>변경
- 이제 Microsoft.applicationinsights.snapshotcollector는 모든 대상 프레임 워크에 대 한 Microsoft ApplicationInsights >= 2.5.1에 종속 됩니다. 응용 프로그램이 이전 버전의 Microsoft ApplicationInsights SDK에 종속 된 경우이는 주요 변경 사항입니다.
- 스냅숏 업 로더에서 TLS 1.0 및 1.1에 대 한 지원을 제거 합니다.
- 이제 PDB 검색 기간에는 15 분이 아닌 24 시간이 기본값으로 설정 됩니다. SnapshotCollectorConfiguration의 PdbRescanInterval를 통해 구성할 수 있습니다.
- PDB 검색은 재귀적이 아닌 최상위 폴더만 검색 합니다. 기호가 이진 폴더의 하위 폴더에 있는 경우에는이 변경 내용이 크게 바뀔 수 있습니다.
### <a name="new-features"></a>새 기능
- 로그 폴더를 오래 된 파일로 채우지 않도록 SnapshotUploader의 로그 회전
- .NET Core 3.0 응용 프로그램에 대 한 최적화 지원 (ReJIT on attach를 통해)
- NuGet 패키지에 기호를 추가 합니다.
- 미니 덤프를 업로드할 때 추가 메타 데이터를 설정 합니다.
- SnapshotCollectorTelemetryProcessor에 초기화 된 속성을 추가 했습니다. Snapshot Collector 완전히 초기화 되 고 서비스 끝점에 연결 될 때 취소 되는 CancellationToken입니다.
- 이제 동적으로 생성 된 메서드의 예외에 대 한 스냅숏을 캡처할 수 있습니다. 예를 들어 Entity Framework 쿼리를 통해 생성 된 컴파일된 식 트리가 있습니다.
### <a name="bug-fixes"></a>버그 수정
- 상태 모니터 때문에 AmbiguousMatchException를 로드 하는 Snapshot Collector 중입니다.
- GetSnapshotCollector 확장 메서드는 이제 모든 TelemetrySinks를 검색 합니다.
- 지원 되지 않는 플랫폼에서 스냅숏 업 로더를 시작 하지 마세요.
- 동적 메서드를 최적화할 때 InvalidOperationException를 처리 합니다 (예: Entity Framework).

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- 소 버린 클라우드에 대 한 지원 추가 (이전 버전은 소 버린 클라우드에서 작동 하지 않음)
- AddSnapshotCollector ()를 사용 하 여 더 쉽게 스냅숏 수집기를 추가 했습니다. 자세한 내용은 [여기](./snapshot-debugger-appservice.md)를 참조하세요.
- Blob 블록을 확인 하려면 FISMA MD5 설정을 사용 합니다. 이렇게 하면 OS가 FIPS 규격 모드로 설정 된 경우 사용할 수 없는 기본 .NET MD5 암호화 알고리즘이 방지 됩니다.
- 함수 호출을 최적화할 때 .NET Framework 프레임을 무시 합니다. 이 동작은 DeoptimizeIgnoredModules 구성 설정으로 제어할 수 있습니다.
- 둘 `DeoptimizeMethodCount` 이상의 함수 호출을 최적화할 수 있도록 하는 구성 설정을 추가 합니다. 자세한 내용은 여기를 참조 하세요.

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- 구조화 된 계측 키를 허용 합니다.
- SnapshotUploader 견고성 강화-이전 업 로더 로그를 이동할 수 없는 경우에도 계속 시작 합니다.
- 1.3.3에서 사용 하지 않도록 설정 된 SnapshotUploader.exe 즉시 종료 될 때 추가 원격 분석 보고를 다시 사용 하도록 설정 합니다.
- 내부 원격 분석을 간소화 합니다.
- _실험적 기능_ : Snappoint 수집 계획: "snapshotOnFirstOccurence"를 추가 합니다. 자세한 내용은 [여기](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)를 참조하세요.

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- SnapshotUploader.exe 응답을 중지 하 고 .NET Core 앱에 대 한 스냅숏을 업로드 하지 않는 버그가 수정 되었습니다.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _실험적 기능_ : Snappoint collection 계획. 자세한 내용은 [여기](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)를 참조하세요.
- SnapshotUploader.exe 프로세스가 종료 될 때까지 기다리지 않고 Microsoft.applicationinsights.snapshotcollector가 로드 된 AppDomain을 언로드할 때 종료 됩니다. 이렇게 하면 IIS에서 호스팅될 때 수집기 안정성이 향상 됩니다.
- 동일한 계측 키를 사용 하는 여러 Microsoft.applicationinsights.snapshotcollector 인스턴스가 동일한 SnapshotUploader 프로세스를 공유 하도록 허용 하는 구성을 추가 합니다. ShareUploaderProcess (기본값 `true` ).
- SnapshotUploader.exe 즉시 종료 될 때 추가 원격 분석을 보고 합니다.
- 디스크에 써야 하는 SnapshotUploader.exe 지원 파일 수를 줄였습니다.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- RtlCloneUserProcess API를 사용 하 여 스냅숏 수집에 대 한 지원을 제거 하 고 PssCaptureSnapshots API만 지원 합니다.
- 1 ~ 3에서 10 분 이내에 캡처할 수 있는 스냅숏 수에 대 한 기본 제한을 늘립니다.
- SnapshotUploader.exe TLS 1.1 및 1.2를 협상할 수 있도록 허용
- SnapshotUploader가 경고 또는 오류를 기록 하는 경우 추가 원격 분석 보고
- 백 엔드 서비스에서 일일 할당량에 도달한 경우 스냅숏 생성 중지 (일별 50 스냅숏)
- 두 인스턴스가 동시에 실행 되는 것을 허용 하지 않도록 SnapshotUploader.exe에 추가 검사를 추가 합니다.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>변경
- .NET Framework를 대상으로 하는 응용 프로그램의 경우 Snapshot Collector 이제 Microsoft ApplicationInsights 버전 2.3.0 이상에 의존 합니다.
2.2.0 이상 사용 됩니다.
이는 대부분의 응용 프로그램에 문제가 되지 않는다고 생각 하지만,이 변경으로 인해 최신 Snapshot Collector를 사용 하지 못하게 되는 경우 알려주세요.
- 실패 한 업로드를 다시 시도할 때 스냅숏 업 로더에서 지 수 백오프 지연을 사용 합니다.
- ServerTelemetryChannel (사용 가능한 경우)를 사용 하 여 원격 분석을 더욱 안정적으로 보고 합니다.
- 종속성 추적에서 무시 되도록 스냅숏 디버거 서비스에 대 한 초기 연결에서 ' SdkInternalOperationsMonitor '를 사용 합니다.
- 스냅숏 디버거 서비스에 대 한 초기 연결에 대 한 원격 분석을 향상 시킵니다.
- 다음에 대 한 추가 원격 분석을 보고 합니다.
  - Azure App Service 버전입니다.
  - Azure 계산 인스턴스.
  - 컨테이너.
  - Azure 함수 앱.
### <a name="bug-fixes"></a>버그 수정
- 문제 카운터 다시 설정 간격이 24 일로 설정 된 경우 24 시간으로 해석 합니다.
- 스냅숏을 삭제 하는 동안 예외가 발생 한 경우 스냅숏 업 로더가 새 스냅숏 처리를 중지 하는 버그를 수정 했습니다.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- 스냅숏 업 로더 이진을 사용 하 여 강력한 이름 서명을 수정 합니다.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>변경
- SnapshotUploader (64) .exe에 필요한 파일은 이제 주 DLL에 리소스로 포함 됩니다. 즉, SnapshotCollectorFiles 폴더는 더 이상 생성 되지 않으며, 빌드 및 배포를 간소화 하 고, 솔루션 탐색기의 혼란을 줄여 줍니다. 업그레이드 시 파일의 변경 내용을 검토 합니다 `.csproj` . `Microsoft.ApplicationInsights.SnapshotCollector.targets`파일이 더 이상 필요 하지 않습니다.
- ProvideAnonymousTelemetry가 false로 설정 된 경우에도 원격 분석은 Application Insights 리소스에 기록 됩니다. 따라서 Azure Portal에서 상태 검사 기능을 구현할 수 있습니다. ProvideAnonymousTelemetry는 제품 지원 및 개선을 위해 Microsoft로 전송 되는 원격 분석에만 영향을 줍니다.
- TempFolder 또는 ShadowCopyFolder가 환경 변수로 리디렉션되는 경우 해당 환경 변수가 설정 될 때까지 수집기가 유휴 상태로 유지 됩니다.
- 프록시 서버를 통해 인터넷에 연결 하는 응용 프로그램의 경우 Snapshot Collector 이제 프록시 설정을 자동으로 검색 하 여 SnapshotUploader.exe에 전달 합니다.
- SnapshotUplaoder 프로세스의 우선 순위를 낮춥니다 (가능한 경우). 이 우선 순위는 IsLowPrioirtySnapshotUploader 옵션을 통해 재정의할 수 있습니다.
- Snapshot Collector를 프로그래밍 방식으로 구성 하려는 시나리오에 대 한 TelemetryConfiguration에 GetSnapshotCollector 확장 메서드를 추가 했습니다.
- 고객 지향 원격 분석에서 응용 프로그램 버전 대신 Application Insights SDK 버전을 설정 합니다.
- 2 분 후에 첫 번째 하트 비트 이벤트를 보냅니다.
### <a name="bug-fixes"></a>버그 수정
- 예외에 null 또는 변경할 수 없는 데이터 사전이 있는 경우 NullReferenceException을 수정 합니다.
- 업 로더에서 공유 위반이 발생 하는 경우 PDB를 몇 번 다시 시도 합니다.
- 시작할 때 두 개 이상의 스레드가 원격 분석 파이프라인을 호출 하는 경우 중복 된 원격 분석을 수정 합니다.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>변경
- XML 문서 주석 파일은 이제 NuGet 패키지에 포함 됩니다.
- `System.Exception`잡음이 있는 예외가 있고이에 대 한 스냅숏을 만들지 않으려면 시나리오에 대 한 ExcludeFromSnapshotting 확장 메서드를에 추가 했습니다.
- IsEnabledWhenProfiling 구성 속성을 추가 했습니다. 기본값은 true입니다. 이는 Application Insights Profiler에서 자세한 컬렉션을 수행 하는 경우 스냅숏 만들기를 일시적으로 사용 하지 않도록 설정 된 이전 버전에서 변경 된 내용입니다. 이전 동작은이 속성을 false로 설정 하 여 복구할 수 있습니다.
### <a name="bug-fixes"></a>버그 수정
- SnapshotUploader64.exe 올바르게 서명 합니다.
- 원격 분석 프로세서의 이중 초기화를 방지 합니다.
- 여러 파이프라인이 있는 앱에서 원격 분석의 이중 로깅을 방지 합니다.
- 수집 계획의 만료 시간으로 버그를 수정 하 여 24 시간 후에 스냅숏을 방지할 수 있습니다.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
이 버전의 가장 큰 변경 내용 (따라서 새 부 버전 번호로 이동)은 스냅숏 만들기 및 처리 파이프라인을 다시 작성 하는 것입니다. 이전 버전에서는이 기능이 네이티브 코드 (ProductionBreakpoints *및 SnapshotHolder* )에서 구현 되었습니다. 새 구현은 P/Invoke를 사용 하는 모든 관리 코드입니다. 새 파이프라인을 사용 하는이 첫 번째 버전에서는 원래 동작에서 멀리 strayed 하지 않았습니다. 새 구현에서는 더 나은 오류 보고를 허용 하 고 향후 개선 사항에 대해 설정 합니다.

### <a name="other-changes-in-this-version"></a>이 버전의 기타 변경 내용
- MinidumpUploader.exe SnapshotUploader.exe (또는 SnapshotUploader64.exe)로 이름이 변경 되었습니다.
- 요청을 최적화/다시 최적화할 수 있는 타이밍 원격 분석이 추가 되었습니다.
- 미니 덤프 업로드를 위해 gzip 압축을 추가 했습니다.
- Pdb가 잠겨 사이트 업그레이드를 방지 하는 문제를 해결 했습니다.
- 섀도 복사할 때 원래 폴더 이름 (SnapshotCollectorFiles)을 기록 합니다.
- OOM으로 인 한 사이트 다시 시작을 방지 하기 위해 64 비트 프로세스에 대 한 메모리 제한을 조정 합니다.
- 사용 하지 않도록 설정한 후에도 스냅숏이 아직 수집 된 문제를 해결 합니다.
- 고객의 AI 리소스에 하트 비트 이벤트를 기록 합니다.
- 문제 ID에서 "원본"을 제거 하 여 스냅숏 속도를 향상 시킵니다.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>변경
확대 사용 원격 분석
- .NET 버전 및 OS 검색 및 보고
- 추가 Azure 환경 검색 및 보고 (클라우드 서비스, Service Fabric)
- 하트 비트 원격 분석에서 예외 메트릭 (첫 번째 확률 예외 및 매수 예외 호출 수)을 기록 하 고 보고 합니다.
### <a name="bug-fixes"></a>버그 수정
- 내부 예외 (System.componentmodel.win32exception)가 throw 되지 않는 SqlException를 올바르게 처리 합니다.
- 기호 폴더에서 후행 공백을 잘라 Minidumpuploader.exe에 대 한 명령줄 인수를 잘못 구문 분석 했습니다.
- 스냅숏 디버거 에이전트의 끝점에 대 한 실패 한 연결의 무한 재시도를 방지 합니다.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>변경
- 호스트 메모리 보호를 추가 했습니다. 이 기능을 통해 호스트 컴퓨터의 메모리에 대 한 영향을 줄일 수가 줄어듭니다.
- Azure Portal 스냅숏 보기 환경을 개선 합니다.