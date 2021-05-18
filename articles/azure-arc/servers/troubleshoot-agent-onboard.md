---
title: Azure Arc 지원 서버 에이전트 연결 이슈 해결
description: 이 문서에서는 서비스에 연결할 때 Azure Arc 지원 서버에서 발생하는 Connected Machine 에이전트 관련 이슈를 해결하는 방법을 설명합니다.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900676"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Connected Machine 에이전트 연결 이슈 해결

이 문서에서는 Windows 또는 Linux용 Azure Arc 지원 서버 Connected Machine 에이전트를 구성하는 동안 발생할 수 있는 이슈를 해결하는 방법에 대한 정보를 제공합니다. 서비스 연결을 구성하는 경우의 대화형 및 대규모 설치 방법이 모두 포함되어 있습니다. 일반적인 내용은 [Arc 지원 서버 개요](./overview.md)를 참조하세요.

## <a name="agent-verbose-log"></a>에이전트 자세한 정보 로그

이 문서의 뒷부분에 설명된 문제 해결 단계를 수행하기 전에 필요한 최소 정보는 자세한 정보 로그입니다. 이 로그에는 자세한 정보(-v) 인수를 사용한 경우의 **azcmagent** 도구 명령 출력이 포함됩니다. 로그 파일은 Windows의 경우 `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log`, Linux의 경우 `/var/opt/azcmagent/log/azcmagent.log`에 기록됩니다.

### <a name="windows"></a>Windows

다음은 대화형 설치를 수행할 때 Windows용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용하여 대규모 설치를 수행할 때 Windows용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

```console
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

다음은 대화형 설치를 수행할 때 Linux용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

>[!NOTE]
>**azcmagent** 를 실행하려면 Linux 머신에 대한 ‘루트’ 액세스 권한이 있어야 합니다.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용하여 대규모 설치를 수행할 때 Linux용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>서비스에 대한 에이전트 연결 이슈

다음 표에는 몇 가지 알려진 오류 및 문제 해결 방법에 대한 제안 사항이 나와 있습니다.

|메시지 |Error |가능한 원인: |솔루션 |
|--------|------|---------------|---------|
|권한 부여 토큰 디바이스 흐름을 가져오지 못함 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |`login.windows.net` 엔드포인트에 연결할 수 없음 | 엔드포인트 연결을 확인합니다. |
|권한 부여 토큰 디바이스 흐름을 가져오지 못함 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |프록시 또는 방화벽에서 `login.windows.net` 엔드포인트에 대한 액세스를 차단하고 있습니다. | 엔드포인트 연결을 확인하고 방화벽 또는 프록시 서버에서 차단되지 않았는지 확인합니다. |
|권한 부여 토큰 디바이스 흐름을 가져오지 못함  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | 그룹 정책 개체 ‘컴퓨터 구성\ 관리 템플릿\ 사용자 프로필\ 시스템 다시 시작 시 지정된 기간(일)보다 오래된 사용자 프로필 삭제’가 사용하도록 설정되었습니다. | GPO가 사용하도록 설정되었으며 영향을 받는 머신을 대상으로 하는지 확인합니다. 자세한 내용은 각주 <sup>[1](#footnote1)</sup>을 참조하세요. |
|SPN에서 권한 부여 토큰을 가져오지 못함 |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |프록시 또는 방화벽에서 `login.windows.net` 엔드포인트에 대한 액세스를 차단하고 있습니다. |엔드포인트 연결을 확인하고 방화벽 또는 프록시 서버에서 차단되지 않았는지 확인합니다. |
|SPN에서 권한 부여 토큰을 가져오지 못함 |`Invalid client secret is provided` |서비스 주체 암호가 잘못되었거나 유효하지 않습니다. |서비스 주체 암호를 확인합니다. |
| SPN에서 권한 부여 토큰을 가져오지 못함 |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |서비스 주체 및/또는 테넌트 ID가 잘못되었습니다. |서비스 주체 및/또는 테넌트 ID를 확인합니다.|
|ARM 리소스 응답 가져오기 |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |자격 증명 및/또는 사용 권한이 잘못되었습니다. |사용자 또는 서비스 주체가 **Azure Connected Machine 온보딩** 역할의 멤버인지 확인합니다. |
|ARM 리소스를 AzcmagentConnect하지 못함 |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure 리소스 공급자가 등록되어 있지 않습니다. |[리소스 공급자](./agent-overview.md#register-azure-resource-providers)를 등록합니다. |
|ARM 리소스를 AzcmagentConnect하지 못함 |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |프록시 서버 또는 방화벽에서 `management.azure.com` 엔드포인트에 대한 액세스를 차단하고 있습니다. |엔드포인트 연결을 확인하고 방화벽 또는 프록시 서버에서 차단되지 않았는지 확인합니다. |

<a name="footnote1"></a><sup>1</sup>이 GPO가 사용하도록 설정되었으며 Connected Machine 에이전트가 있는 머신에 적용되는 경우 *himds* 서비스에 대해 지정된 기본 제공 계정과 연결된 사용자 프로필이 삭제됩니다. 따라서 서비스와 통신하는 데 사용되며 30일 동안 로컬 인증서 저장소에 캐시된 인증 인증서도 삭제됩니다. 30일 한도 전에 인증서 갱신을 시도합니다. 이슈를 해결하려면 [머신의 등록을 취소](manage-agent.md#unregister-machine)하는 단계를 수행한 다음, `azcmagent connect`를 실행하여 서비스에 다시 등록합니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Microsoft Q&A](/answers/topics/azure-arc.html)를 통해 Azure 전문가의 답변을 받습니다.

* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.

* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.
