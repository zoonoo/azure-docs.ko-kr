---
title: 업데이트 관리 오류 문제 해결
description: 업데이트 관리 문제 해결 방법 살펴보기
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064600"
---
# <a name="troubleshooting-issues-with-update-management"></a>업데이트 관리 문제 해결

이 문서에서는 업데이트 관리 사용 중에 발생할 수 있는 문제 해결을 위한 솔루션을 살펴봅니다.

## <a name="windows"></a>Windows

가상 머신에 솔루션을 온보드할 때 문제가 발생한 경우 로컬 컴퓨터에서 이벤트 ID가 **4502**인 이벤트에 대해 **응용 프로그램 및 서비스 로그**의 **작업 관리자** 이벤트 로그와, **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**를 포함하고 있는 이벤트 메시지를 확인합니다.

다음 섹션에는 특정 오류 메시지 및 각각의 해결 방법이 설명되어 있습니다. 다른 온보드 문제는 [온보드 문제 해결 솔루션](onboarding.md)을 참조하세요.

### <a name="machine-already-registered"></a>시나리오: 컴퓨터가 이미 다른 계정에 등록되어 있음

#### <a name="issue"></a>문제

다음과 같은 오류 메시지가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>원인

컴퓨터가 이미 업데이트 관리를 위한 다른 작업 영역에 등록되었습니다.

#### <a name="resolution"></a>해결 방법

컴퓨터에서 [Hybrid Runbook 그룹을 삭제](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)하여 오래된 아티팩트를 정리하고 다시 시도합니다.

### <a name="machine-unable-to-communicate"></a>시나리오: 컴퓨터가 서비스와 통신할 수 없음

#### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>원인

네트워크 통신을 차단하는 프록시, 게이트웨이 또는 방화벽이 있을 수 있습니다.

#### <a name="resolution"></a>해결 방법

네트워크를 검토하고 적합한 포트 및 주소가 허용되었는지 확인합니다. 업데이트 관리 및 Hybrid Runbook Worker에 필요한 포트 및 주소 목록은 [네트워크 요구 사항](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="unable-to-create-selfsigned-cert"></a>시나리오: 자체 서명된 인증서를 만들 수 없음

#### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>원인

Hybrid Runbook Worker가 자체 서명 인증서를 만들지 못함

#### <a name="resolution"></a>해결 방법

시스템 계정에 **C:\ProgramData\Microsoft\Crypto\RSA** 폴더에 대한 읽기 액세스 권한이 있는지 확인하고 다시 시도합니다.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>시나리오: 업데이트 실행 시작 실패

#### <a name="issue"></a>문제

Linux 컴퓨터에서 업데이트 실행을 시작하지 못합니다.

#### <a name="cause"></a>원인

Linux Hybrid Worker가 비정상 상태입니다.

#### <a name="resolution"></a>해결 방법

다음 로그 파일의 사본을 작성하여 문제 해결 목적으로 보존합니다.

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>시나리오: 업데이트 실행이 시작되나 오류 발생

#### <a name="issue"></a>문제

업데이트 실행은 시작되지만 실행 중 오류가 발생합니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 패키지 관리자가 비정상 상태임
* 특정 패키지가 클라우드 기반 패치를 방해할 수 있음
* 기타 원인

#### <a name="resolution"></a>해결 방법

Linux에서 업데이트 실행이 제대로 시작된 뒤 업데이트 실행 중에 실패할 경우 실행에서 해당 컴퓨터의 ㅈ가업 출력을 확인합니다. 컴퓨터의 패키지 관리자에서 특정 오류 메시지를 찾아 조치를 취할 수 있습니다. 업데이트 관리에서 업데이트 배포에 성공하려면 패키지 관리자가 정상 상태여야 합니다.

일부 경우 패키지 업데이트가 업데이트 관리를 차단하여 업데이트 배포가 완료되지 못할 수 있습니다. 이 경우 이 패키지를 향후 업데이트 실행에서 제외하거나 직접 수동으로 설치합니다.

패치 문제를 해결할 수 없는 경우 문제 해결을 위해 다음 업데이트 배포가 시작되기 **전에** 다음 로그 파일의 사본을 작성하여 보관합니다.

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우, 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.