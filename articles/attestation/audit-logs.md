---
title: Azure Attestation 감사 로그
description: Azure Attestation에 대해 수집되는 전체 감사 로그에 대해 설명합니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95758992"
---
# <a name="audit-logs-for-azure-attestation"></a>Azure Attestation에 대한 감사 로그

감사 로그는 시간 경과에 따라 발생하는 안전하고 변경할 수 없는 타임스탬프가 지정된 불연속 이벤트 레코드입니다. 이러한 로그는 증명 인스턴스의 기능에 영향을 줄 수 있는 중요한 이벤트를 캡처합니다.

Azure Attestation은 증명 인스턴스 및 관련 정책을 관리합니다. 인스턴스 관리 및 정책 변경과 관련된 작업을 감사하고 기록합니다.

이 문서에는 기록된 이벤트, 수집된 정보 및 이러한 로그의 위치에 대한 정보가 포함되어 있습니다.

## <a name="about-audit-logs"></a>감사 로그 정보

Azure Attestation은 코드를 사용하여 증명을 수행하는 방식에 영향을 주는 이벤트에 대한 감사 로그를 생성합니다. 일반적으로 증명 인스턴스에 대한 정책이 변경되는 방법 또는 시기와 일부 관리 작업으로 한정됩니다.

### <a name="auditable-events"></a>감사 가능한 이벤트
수집하는 몇 가지 감사 로그는 다음과 같습니다.

|     이벤트/API                              |     이벤트 설명                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     인스턴스 만들기                        |     증명 서비스의 새 인스턴스를 만듭니다. |
|     인스턴스 삭제                       |     증명 서비스의 인스턴스를 삭제합니다. |
|     정책 인증서 추가                 |     현재 정책 관리 인증서 세트에 인증서를 추가합니다. |
|     정책 인증서 제거              |     현재 정책 관리 인증서 세트에서 인증서를 제거합니다. |
|     현재 정책 설정                     |     지정된 TEE 형식에 대한 증명 정책을 설정합니다. |
|     증명 정책 다시 설정               |     지정된 TEE 형식에 대한 증명 정책을 다시 설정합니다. |
|     정책 업데이트 준비               |     지정된 TEE 형식에 대한 증명 정책 업데이트를 준비합니다. |
|     재해 발생 후 테넌트 리하이드레이션       |     증명 서비스의 이 인스턴스에 있는 모든 증명 테넌트를 다시 봉인합니다. 이 작업은 증명 서비스 관리자만 수행할 수 있습니다. |

### <a name="collected--information"></a>수집된 정보
이러한 각 이벤트에 대해 Azure Attestation은 다음 정보를 수집합니다.

- 작업 이름
- 작업 성공
- 작업 호출자로, 다음 중 하나일 수 있습니다.
    - Azure AD UPN
    - 개체 ID
    - 인증서
    - Azure AD 테넌트 ID
- 작업 대상으로, 다음 중 하나일 수 있습니다.
    - 환경
    - 서비스 지역
    - 서비스 역할
    - 서비스 역할 인스턴스
    - 리소스 ID
    - 리소스 지역

### <a name="sample-audit-log"></a>샘플 감사 로그

감사 로그는 JSON 형식으로 제공됩니다. 다음은 감사 로그 형태의 예입니다.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>감사 로그 액세스

이러한 로그는 Azure에 저장되며 직접 액세스할 수 없습니다. 이러한 로그에 액세스해야 하는 경우 지원 티켓을 제출합니다. 자세한 내용은 [Microsoft 지원에 문의](https://azure.microsoft.com/support/options/)를 참조하세요. 

지원 티켓이 제출되면 Microsoft에서 다운로드하여 이러한 로그에 대한 액세스 권한을 제공합니다.
