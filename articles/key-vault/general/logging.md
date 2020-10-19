---
title: Azure Key Vault 로깅 | Microsoft Docs
description: 사용자가 제공하는 정보를 Azure 스토리지 계정에 저장하도록 Azure Key Vault에서 로깅을 설정하여 키 자격 증명 모음에 대한 액세스를 모니터링하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 162e40555e11dff716b58eec4b1168728257693e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131176"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 로깅

하나 이상의 키 자격 증명 모음을 만든 후에는 키 자격 증명 모음에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. 이 작업은 제공하는 정보를 Azure 스토리지 계정에 저장하는 Azure Key Vault에서 로깅을 사용하도록 설정하여 수행할 수 있습니다. 이를 설정하는 방법에 대한 단계별 지침은 [키 자격 증명 모음 로깅을 사용하는 방법](howto-logging.md)을 참조하세요.

키 자격 증명 모음 작업 후 최대 10분 동안 로깅 정보에 액세스할 수 있습니다. 대부분의 경우 이것보다 빠릅니다.  스토리지 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 스토리지 계정에 유지하지 않으려는 로그를 삭제합니다.

Key Vault에 대한 개요는 [Azure Key Vault란?](overview.md)을 참조하세요. Key Vault를 사용할 수 있는 위치에 대한 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요. [Key Vault에 대한 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) 사용에 대한 정보입니다.

## <a name="interpret-your-key-vault-logs"></a>Key Vault 로그 해석

로깅을 사용하면 지정한 스토리지 계정에 대해 **insights-logs-auditevent**라는 새 컨테이너가 자동으로 만들어집니다. 여러 키 자격 증명 모음에 대한 로그를 수집하는 데 이 동일한 스토리지 계정을 사용할 수 있습니다.

개별 Blob은 JSON Blob 형식으로 텍스트로 저장됩니다. 로그 항목 예제를 살펴보겠습니다. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

다음 표에는 필드 이름 및 설명이 나와 있습니다.

| 필드 이름 | Description |
| --- | --- |
| **time** |UTC 형식의 날짜 및 시간입니다. |
| **resourceId** |Azure Resource Manager 리소스 ID입니다. 키 자격 증명 모음 로그의 경우 이는 항상 키 자격 증명 모음 리소스 ID입니다. |
| **operationName** |다음 표에 설명된 대로 작업의 이름입니다. |
| **operationVersion** |클라이언트에서 요청한 REST API 버전입니다. |
| **category** |결과 유형입니다. Key Vault 로그의 경우 `AuditEvent`는 사용 가능한 단일 값입니다. |
| **resultType** |REST API 요청의 결과입니다. |
| **resultSignature** |HTTP 상태입니다. |
| **resultDescription** |사용 가능한 경우 결과에 대한 추가 설명입니다. |
| **durationMs** |밀리초 단위로 REST API 요청을 처리하는 데 걸린 시간입니다. 네트워크 대기 시간을 포함하지 않으므로 클라이언트 쪽에서 측정한 시간은 이 시간과 일치하지 않을 수 있습니다. |
| **callerIpAddress** |요청한 클라이언트의 IP 주소입니다. |
| **correlationId** |클라이언트가 서비스 쪽(키 자격 증명 모음) 로그를 사용하여 클라이언트 쪽 로그 상관 관계를 지정하도록 전달할 수 있는 선택적 GUID입니다. |
| **identity** |REST API 요청에 제공된 토큰의 ID입니다. Azure PowerShell cmdlet에서 발생하는 요청의 경우와 마찬가지로 일반적으로 "사용자", "서비스 주체" 또는 "사용자+appId"의 조합입니다. |
| **properties** |작업(**operationName**)에 따라 달라지는 정보입니다. 대부분의 경우 이 필드에는 클라이언트 정보(클라이언트에서 전달한 사용자 에이전트 문자열), 정확한 REST API 요청 URI 및 HTTP 상태 코드가 포함됩니다. 또한 개체가 요청의 결과로 반환되면(예: **KeyCreate** 또는 **VaultGet**) 키 URI(`id`로), 자격 증명 모음 URI 또는 비밀 URI도 포함됩니다. |

**operationName** 필드 값은 *ObjectVerb* 형식입니다. 예를 들면 다음과 같습니다.

* 모든 키 자격 증명 모음 작업은 `Vault<action>` 형식입니다(예: `VaultGet` 및 `VaultCreate`).
* 모든 키 작업은 `Key<action>` 형식입니다(예: `KeySign` 및 `KeyList`).
* 모든 비밀 작업은 `Secret<action>` 형식입니다(예: `SecretGet` 및 `SecretListVersions`).

다음 표에는 **operationName** 값 및 해당 REST API 명령이 나와 있습니다.

### <a name="operation-names-table"></a>작업 이름 테이블

| operationName | REST API 명령 |
| --- | --- |
| **인증** |Azure Active Directory 엔드포인트를 통해 인증 |
| **VaultGet** |[키 자격 증명 모음에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[키 자격 증명 모음 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[키 자격 증명 모음 삭제](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[키 자격 증명 모음 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[리소스 그룹의 모든 키 자격 증명 모음 목록](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[키 만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[키에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[자격 증명 모음으로 키 가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[키 백업](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[키 삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[키 복원](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[키로 서명](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[키로 확인](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[키 래핑](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[키 래핑 취소](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[키로 암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[키로 암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[키 업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[자격 증명 모음에 키 나열](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[키 버전 나열](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[암호 만들기](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[비밀 가져오기](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[암호 업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[암호 삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[자격 증명 모음에 암호 나열](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[암호 버전 나열](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | 자격 증명 모음 액세스 정책 변경 이벤트가 게시됨 |
| **SecretNearExpiryEventGridNotification** |비밀이 곧 만료되는 이벤트가 게시됨 |
| **SecretExpiredEventGridNotification** |비밀이 만료된 이벤트가 게시됨 |
| **KeyNearExpiryEventGridNotification** |키가 곧 만료되는 이벤트가 게시됨 |
| **KeyExpiredEventGridNotification** |키가 만료된 이벤트가 게시됨 |
| **CertificateNearExpiryEventGridNotification** |인증서가 곧 만료되는 이벤트가 게시됨 |
| **CertificateExpiredEventGridNotification** |인증서가 만료된 이벤트가 게시됨 |

## <a name="use-azure-monitor-logs"></a>Azure Monitor 로그 사용

Azure Monitor 로그에서 Key Vault 솔루션을 사용하여 Key Vault `AuditEvent` 로그를 검토할 수 있습니다. Azure Monitor 로그에서 로그 쿼리를 사용하여 데이터를 분석하고 필요한 정보를 가져옵니다. 

이를 설정하는 방법을 포함한 자세한 내용은 [Azure Monitor의 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [키 자격 증명 모음 로깅을 사용하는 방법](howto-logging.md)
- .NET 웹 애플리케이션에서 Azure Key Vault를 사용하는 자습서는 [웹 애플리케이션에서 Azure Key Vault 사용](tutorial-net-create-vault-azure-web-app.md)을 참조하세요.
- 프로그래밍 참조는 [Azure Key Vault 개발자 가이드](developers-guide.md)를 참조하세요.
- Azure Key Vault용 Azure PowerShell 1.0 cmdlet 목록은 [Azure Key Vault cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)을 참조하세요.
