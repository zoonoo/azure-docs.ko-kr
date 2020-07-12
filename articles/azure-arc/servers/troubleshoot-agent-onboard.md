---
title: 서버 에이전트 연결 문제에 대 한 Azure Arc 문제 해결
description: 이 문서에서는 서비스에 연결 하려고 할 때 서버 (미리 보기) 용 Azure Arc에서 발생 하는 연결 된 컴퓨터 에이전트의 문제를 해결 하 고 문제를 해결 하는 방법을 설명 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262021"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>연결 된 컴퓨터 에이전트 연결 문제 해결

이 문서에서는 Windows 또는 Linux 용 Azure Arc 서버 (미리 보기) 연결 된 컴퓨터 에이전트를 구성 하는 동안 발생할 수 있는 문제를 해결 하 고 해결 하는 방법에 대 한 정보를 제공 합니다. 서비스에 대 한 연결을 구성 하는 경우 대화형 및 확장 설치 방법 모두 포함 됩니다. 일반 정보는 [서버에 대 한 원호 개요](./overview.md)를 참조 하세요.

## <a name="agent-verbose-log"></a>에이전트 자세한 정보 표시 로그

이 문서의 뒷부분에 설명 된 문제 해결 단계를 수행 하기 전에 필요한 최소 정보는 자세한 로그입니다. Verbose (-v) 인수를 사용 하는 경우 **azcmagent** tool 명령의 출력을 포함 합니다. 로그 파일은 `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` Windows 및 Linux 용에 기록 됩니다 `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

다음은 대화형 설치를 수행할 때 Windows 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용 하 여 스케일 아웃 설치를 수행할 때 Windows 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

다음은 대화형 설치를 수행할 때 Linux 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용 하 여 확장 설치를 수행할 때 Linux 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>서비스에 대 한 에이전트 연결 문제

다음 표에는 알려진 오류 및 문제 해결 방법에 대 한 제안 사항이 정리 되어 있습니다.

|메시지 |오류 |가능한 원인: |솔루션 |
|--------|------|---------------|---------|
|권한 부여 토큰 장치 흐름을 획득 하지 못했습니다. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |끝점에 연결할 수 없음 `login.windows.net` | 끝점에 대 한 연결을 확인 합니다. |
|권한 부여 토큰 장치 흐름을 획득 하지 못했습니다. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |프록시 또는 방화벽에서 끝점에 대 한 액세스를 차단 하 고 `login.windows.net` 있습니다. | 끝점에 대 한 연결을 확인 하 고 방화벽이 나 프록시 서버에 의해 차단 되지 않습니다. |
|SPN에서 권한 부여 토큰을 획득 하지 못했습니다. |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |프록시 또는 방화벽에서 끝점에 대 한 액세스를 차단 하 고 `login.windows.net` 있습니다. |끝점에 대 한 연결을 확인 하 고 방화벽이 나 프록시 서버에 의해 차단 되지 않습니다. |
|SPN에서 권한 부여 토큰을 획득 하지 못했습니다. |`Invalid client secret is provided` |잘못 되었거나 잘못 된 서비스 주체 암호입니다. |서비스 주체 암호를 확인 합니다. |
| SPN에서 권한 부여 토큰을 획득 하지 못했습니다. |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |서비스 주체 및/또는 테 넌 트 ID가 잘못 되었습니다. |서비스 주체 및/또는 테 넌 트 ID를 확인 합니다.|
|ARM 리소스 응답 가져오기 |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |잘못 된 자격 증명 및/또는 사용 권한 |사용자 또는 서비스 주체가 **Azure 연결 된 컴퓨터** 등록 역할의 멤버 인지 확인 합니다. |
|ARM 리소스를 AzcmagentConnect 못했습니다. |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure 리소스 공급자가 등록 되지 않았습니다. |[리소스 공급자](./agent-overview.md#register-azure-resource-providers)를 등록 합니다. |
|ARM 리소스를 AzcmagentConnect 못했습니다. |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |프록시 서버 또는 방화벽에서 끝점에 대 한 액세스를 차단 하 고 `management.azure.com` 있습니다. |끝점에 대 한 연결을 확인 하 고 방화벽이 나 프록시 서버에 의해 차단 되지 않습니다. |

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html)를 통해 Azure 전문가 로부터 답변을 받으세요.

* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.

* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기**를 선택합니다.