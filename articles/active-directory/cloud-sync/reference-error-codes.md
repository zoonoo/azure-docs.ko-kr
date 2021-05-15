---
title: Azure AD Connect 클라우드 동기화 오류 코드 및 설명
description: 클라우드 동기화 오류 코드에 대한 참조 문서
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0aeb2e9649a4bbbb2520eac683836ebbf7dfd0a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075052"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect 클라우드 동기화 오류 코드 및 설명
다음은 오류 코드 목록 및 해당 설명입니다.


## <a name="error-codes"></a>오류 코드

|오류 코드|세부 정보|시나리오|해결 방법|
|-----|-----|-----|-----|
|TimeOut|오류 메시지: 온-프레미스 에이전트에 연결하고 구성을 동기화 할 때 요청 시간 초과 오류가 검색되었습니다. 클라우드 동기화 에이전트와 관련된 추가 문제는 문제 해결 참고 자료를 참조하세요.|HIS 요청 시간이 초과되었습니다. 현재 시간 제한 값은 10분입니다.|[문제 해결 참고 자료](how-to-troubleshoot.md)를 참조하십시오.|
|HybridSynchronizationActiveDirectoryInternalServerError|오류 메시지: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면 지원 담당자에게 연락한 후 작업 식별자를 제공하세요(작업 식별자: AD2AADProvisioning.30b500eaf9c643b2b78804e80c1421fe.5c291d3c-d29f-4570-9d6b-f0c2fa3d5926). 추가 정보: HTTP 요청을 처리하는 동안 예외가 발생했습니다. |검색 요청에 대해 SCIM 요청에서 받은 매개 변수를 처리할 수 없습니다.|자세한 내용은 이 예외의 'Response' 속성에서 반환된 HTTP 응답을 참조하세요.|
|HybridIdentityServiceNoAgentsAssigned|오류 메시지: 동기화하려는 도메인의 활성 에이전트를 찾을 수 없습니다. 에이전트가 제거되었는지 확인하세요. 제거되었다면, 해당 에이전트를 다시 설치하십시오.|실행 중인 에이전트가 없습니다. 에이전트가 제거된 것 같습니다. 새 에이전트 등록|"이 경우 포털에서 도메인에 할당된 에이전트는 표시되지 않습니다.|
|HybridIdentityServiceNoActiveAgents|오류 메시지: 동기화하려는 도메인의 활성 에이전트를 찾을 수 없습니다. 에이전트가 설치된 서버로 이동하여 에이전트가 실행되고 있는지 확인하고, 서비스에서 "Microsoft Azure AD Cloud Sync Agent"가 실행되고 있는지 확인하십시오.|"에이전트가 ServiceBus 엔드포인트를 수신 대기하지 않습니다. [에이전트가 Service Bus에 대한 연결을 허용하지 않는 방화벽 뒤에 있습니다.](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|오류 메시지: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면 지원 담당자에게 연락한 후 작업 식별자를 제공하세요(작업 식별자: AD2AADProvisioning.3a2a0d8418f34f54a03da5b70b1f7b0c.d583d090-9cd3-4d0a-aee6-8d666658c3e9). 추가 정보: 클라우드 동기화를 설정하는 데 문제가 있는 것 같습니다. 온-프레미스 AD 도메인에 클라우드 동기화 에이전트를 다시 등록하고 Azure Portal에서 구성을 다시 시작하세요.|리소스 이름을 설정하여 HIS가 연락할 담당 에이전트를 알도록 해야 합니다.|온-프레미스 AD 도메인에 클라우드 동기화 에이전트를 다시 등록하고 Azure Portal에서 구성을 다시 시작하세요.|
|HybridIdentityServiceAgentSignalingError|오류 메시지: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면 지원 담당자에게 연락한 후 작업 식별자를 제공하세요(작업 식별자: AD2AADProvisioning.92d2e8750f37407fa2301c9e52ad7e9b.efb835ef-62e8-42e3-b495-18d5272eb3f9). 추가 세부 정보: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요.|Service Bus가 에이전트에 메시지를 보낼 수 없습니다. Service Bus의 작동이 중단되었거나 에이전트가 응답하지 않을 수 있습니다.|이 문제가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요.|
|AzureDirectoryServiceServerBusy|오류 메시지: 오류가 발생했습니다. 오류 코드: 81. 오류 설명: Azure Active Directory 현재 사용 중입니다. 이 작업은 자동으로 다시 시도됩니다. 이 문제가 24시간 넘게 지속되면 기술 지원 서비스에 문의하세요. 추적 ID: 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f 서버 이름:|Azure Active Directory 현재 사용 중입니다.|이 문제가 24시간 넘게 지속되면 기술 지원 서비스에 문의하세요.|
|AzureActiveDirectoryInvalidCredential|오류 메시지: Azure AD Connect Cloud Sync를 실행하는 데 사용되는 서비스 계정에서 문제를 발견했습니다. [여기](./how-to-troubleshoot.md)의 지침에 따라 클라우드 서비스 계정을 복구할 수 있습니다. 이 오류가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요. 추가 오류 세부 정보: CredentialsInvalid AADSTS50034: 사용자 계정 {EmailHidden}이 (가) skydrive365.onmicrosoft.com 디렉터리에 없습니다. 이 애플리케이션에 로그인하려면 디렉터리에 계정을 추가해야 합니다. 추적 ID: 14b63033-3bc9-4bd4-b871-5eb4b3500200 상관 관계 ID: 57d93ed1-be4d-483c-997c-a3b6f03deb00 타임스탬프: 2021-01-12 21:08:29Z |이 오류는 동기화 서비스 계정 ADToAADSyncServiceAccount가 테넌트에 존재하지 않는 경우에 throw됩니다. 계정을 실수로 삭제하는 것이 원인일 수 있습니다.|[Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount)를 사용하여 서비스 계정을 수정합니다.|
|AzureActiveDirectoryExpiredCredentials|오류 메시지: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요. 추가 오류 세부 정보: CredentialsExpired AADSTS50055: 암호가 만료되었습니다. 추적 ID: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 상관 관계 ID: 1c69b196-1c3a-4381-9187-c84747807155 타임스탬프: 2021-01-12 20:59:31Z | 응답 상태 코드가 성공을 나타내지 않습니다(401, 권한 없음).<br> AAD Sync 서비스 계정 자격 증명이 만료되었습니다.|https://go.microsoft.com/fwlink/?linkid=2150988 의 지침에 따라 클라우드 서비스 계정을 복구할 수 있습니다. 이 오류가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요.  추가 오류 세부 정보: 관리 Azure Active Directory 테넌트 자격 증명이 만료된 OAuth 토큰과 교환되었습니다."|
|AzureActiveDirectoryAuthenticationFailed|오류 메시지: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면 지원 담당자에게 연락한 후 작업 식별자를 제공하세요(작업 식별자: AD2AADProvisioning.60b943e88f234db2b887f8cb91dee87c.707be0d2-c6a9-405d-a3b9-de87761dc3ac). 추가 세부 정보: 이 지점에서 이 요청을 처리할 수 없습니다. 이 문제가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요. 추가 오류 세부 정보: UnexpectedError.|알 수 없는 오류입니다.|이 문제가 지속되면, 구성의 상태 창에서 작업 ID로 지원 담당자에게 문의하세요.|

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
