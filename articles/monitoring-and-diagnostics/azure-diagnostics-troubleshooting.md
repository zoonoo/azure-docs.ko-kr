---
title: "Azure 진단 문제 해결 | Microsoft Docs"
description: "Azure Virtual Machines, Service Fabric 또는 Cloud Services에서 Azure 진단을 사용할 때 문제를 해결합니다."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 81f9a5a8e5ce8389c12a40eb02dd554fc140e009
ms.contentlocale: ko-kr
ms.lasthandoff: 03/22/2017


---
# <a name="azure-diagnostics-troubleshooting"></a>Azure 진단 문제 해결
Azure 진단 사용과 관련된 문제 해결 정보입니다. Azure 진단에 대한 자세한 내용은 [Azure 진단 개요](azure-diagnostics.md)를 참조하세요.

## <a name="azure-diagnostics-is-not-starting"></a>Azure 진단이 시작되지 않음
진단은 게스트 에이전트 플러그 인과 모니터링 에이전트라는 두 개의 구성 요소로 이루어져 있습니다. 진단 시작에 실패한 이유에 대한 내용은 **DiagnosticsPluginLauncher.log** 및 **DiagnosticsPlugin.log** 로그 파일을 확인하면 됩니다.  

클라우드 서비스 역할에서 게스트 에이전트 플러그 인의 로그 파일은 다음에 위치합니다.

```
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\
```

Azure Virtual Machine에서 게스트 에이전트 플러그 인의 로그 파일은 다음에 위치합니다.

```
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.7.4.0\Logs\
```

로그 파일의 마지막 줄에는 종료 코드가 포함됩니다.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```

플러그 인은 다음 종료 코드를 반환합니다.

| 종료 코드 | 설명 |
| --- | --- |
| 0 |성공. |
| -1 |일반 오류. |
| -2 |rcf 파일을 로드할 수 없습니다.<p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -3 |진단 구성 파일을 로드할 수 없습니다.<p><p>해결 방법: 구성 파일이 스키마 유효성 검사를 통과하지 못한 것이 원인입니다. 해결 방법은 스키마를 준수하는 구성 파일을 제공하는 것입니다. |
| -4 |진단에서 이미 로컬 리소스 디렉터리를 사용하고 있는 모니터링 에이전트의 또 다른 인스턴스입니다.<p><p>해결 방법: **LocalResourceDirectory**를 참조하세요. |
| -6 |게스트 에이전트 플러그 인 시작 관리자가 잘못된 명령줄로 진단을 실행하려고 했습니다.<p><p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -10 |진단 플러그 인이 처리되지 않은 예외와 함께 종료되었습니다. |
| -11 |게스트 에이전트는 모니터링 에이전트를 시작하고 모니터링하는 일을 담당하는 프로세스를 만들 수 없습니다.<p><p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다.<p> |
| -101 |진단 플러그 인을 호출할 때 인수가 잘못되었습니다.<p><p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -102 |플러그인 프로세스 자체를 초기화할 수 없습니다.<p><p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다. |
| -103 |플러그인 프로세스 자체를 초기화할 수 없습니다. 특히 로거 자체를 만들 수 없습니다.<p><p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다. |
| -104 |게스트 에이전트가 제공하는 rcf 파일을 로드할 수 없습니다.<p><p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -105 |진단 플러그 인이 진단 구성 파일을 열 수 없습니다.<p><p>이는 진단 플러그 인을 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -106 |진단 구성 파일을 읽을 수 없습니다.<p><p>해결 방법: 구성 파일이 스키마 유효성 검사를 통과하지 못한 것이 원인입니다. 따라서 해결 방법은 스키마를 준수하는 구성 파일을 제공하는 것입니다. Azure Virtual Machine의 경우 진단 확장으로 전달되는 구성은 VM의 *%SystemDrive%\WindowsAzure\Config* 폴더에서 확인할 수 있습니다. 해당하는 XML 파일을 열고 **Microsoft.Azure.Diagnostics**를 검색한 후 **xmlCfg** 또는 **WadCfg** 필드를 검색합니다. WadCfg 필드가 있는 경우 구성은 JSON 형태입니다. xmlCfg 필드가 있는 경우 구성은 XML 형태이고 base64로 인코딩됩니다. 진단에서 로드한 XML을 보려면 디코드해야 합니다. 클라우드 서비스 역할의 경우 진단 확장으로 전달되는 구성은 VM의 C:\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\config.txt에서 찾을 수 있습니다. 데이터는 base64로 인코딩되므로 진단을 통해 로드된 XML을 보려면 [디코딩](http://www.bing.com/search?q=base64+decoder)해야 합니다.<p> |
| -107 |모니터링 에이전트로의 리소스 디렉터리 전달이 유효하지 않습니다.<p><p>이는 모니터링 에이전트를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p> |
| -108 |진단 구성 파일을 모니터링 에이전트 구성 파일로 변환할 수 없습니다.<p><p>이는 진단 플러그 인을 유효하지 않은 구성 파일과 함께 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -110 |일반 진단 구성 오류.<p><p>이는 진단 플러그 인을 유효하지 않은 구성 파일과 함께 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -111 |모니터링 에이전트를 시작할 수 없습니다.<p><p>해결 방법: 사용 가능한 시스템 리소스가 충분한지 확인합니다. |
| -112 |일반 오류 |

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>진단 데이터가 Azure 저장소에 기록되지 않음
Azure 진단은 Azure 저장소의 모든 데이터를 저장합니다.

이벤트 데이터 누락의 가장 일반적인 원인은 잘못 정의된 저장소 계정 정보입니다.

해결 방법: 진단 구성 파일을 수정하고 진단을 다시 설치합니다.
진단 확장을 다시 설치한 후 문제가 지속되는 경우 모니터링 에이전트 오류를 조사하여 추가로 디버그해야 할 수 있습니다. 이벤트 데이터는 저장소 계정에 업로드되기 전에 LocalResourceDirectory에 저장됩니다.
이 디렉터리 경로는 다양합니다.

클라우드 서비스 역할:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Virtual Machines:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

LocalResourceDirectory 폴더에 파일이 없는 경우 모니터링 에이전트를 실행할 수 없습니다. 이 문제는 일반적으로 잘못된 구성 파일 때문에 발생하고 CommandExecution.log에 보고되어야 하는 이벤트입니다.

모니터링 에이전트가 이벤트 데이터를 제대로 수집하고 있으면 구성 파일에 정의된 각 이벤트에 대한 .tsf 파일이 표시됩니다. 모니터링 에이전트가 MaEventTable.tsf 파일에 해당 오류를 기록합니다. 이 파일의 내용을 검사하려면 tabel2csv 응용 프로그램을 사용하여 .tsf 파일을 쉼표로 구분된(.csv) 값 파일로 변환합니다.

클라우드 서비스 역할:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* 는 일반적으로 D입니다.

가상 컴퓨터에서:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

이전 명령은 열어서 오류 메시지를 검사할 수 있는 *maeventtable.csv* 로그 파일을 생성합니다.    

## <a name="diagnostics-data-tables-not-found"></a>진단 데이터 테이블을 찾을 수 없음
Azure 진단 데이터를 포함하는 Azure 저장소의 테이블은 다음 코드를 사용하여 이름이 지정됩니다.

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

다음은 예제입니다.

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```

4개의 테이블을 생성합니다.

| 이벤트 | 테이블 이름 |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

