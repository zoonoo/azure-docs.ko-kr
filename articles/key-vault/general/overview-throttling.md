---
title: Azure Key Vault 제한 지침
description: Key Vault 제한은 리소스의 과용을 방지하기 위해 동시 호출 수를 제한합니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432087"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 제한 지침

제한은 리소스의 과용을 방지하기 위해 Azure 서비스에 대한 동시 호출 수를 제한하는 사용자 시작 프로세스입니다. Azure Key Vault(AKV)는 많은 양의 요청을 처리하도록 설계되었습니다. 매우 많은 수의 요청이 발생할 경우 클라이언트의 요청을 제한하면 AKV 서비스의 최적의 성능 및 안정성을 유지하는 데 도움이 됩니다.

제한 한도는 시나리오에 따라 다릅니다. 예를 들어, 대용량 쓰기를 수행하는 경우 읽기만 수행하는 경우보다 제한 가능성이 높습니다.

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault가 한도를 처리하는 방법

Key Vault의 서비스 제한은 리소스의 오용을 방지 하 고 모든 Key Vault 클라이언트에 대해 서비스 품질을 보장 합니다. 서비스 임계값이 초과 되 면 해당 클라이언트에서 일정 기간 동안 추가 요청을 제한 하는 Key Vault HTTP 상태 코드 429 (너무 많은 요청)을 반환 하 고 요청이 실패 합니다. Key Vault에서 추적 하는 제한 제한에 429 수를 반환 하는 실패 한 요청입니다. 

Key Vault는 원래 배포 시 암호를 저장 하 고 검색 하는 데 사용 하도록 설계 되었습니다.  전 세계에서 진화 하 고 Key Vault는 런타임에 암호를 저장 하 고 검색 하는 데 사용 되 고 있으며, 응용 프로그램 및 서비스에서 데이터베이스와 같은 Key Vault를 사용 하려고 합니다.  현재 한도는 높은 처리량 속도를 지원 하지 않습니다.

Key Vault는 원래 [Azure Key Vault 서비스 제한](service-limits.md)에 지정 된 제한에 의해 만들어졌습니다.  추가 요금을 통해 Key Vault을 최대화 하려면 처리량을 최대화 하기 위한 몇 가지 권장 지침/모범 사례를 참조 하세요.
1. 제한이 있는지 확인 합니다.  클라이언트는 429의 지 수 백오프 정책을 준수 하 고 아래 지침에 따라 재시도를 수행 해야 합니다.
1. 여러 자격 증명 모음 및 다른 지역 간에 Key Vault 트래픽을 나눕니다.   각 보안/가용성 도메인에 대해 별도의 자격 증명 모음을 사용 합니다.   각각 두 개의 지역에 5 개의 앱이 있는 경우 각각 앱 및 지역에 고유한 비밀을 포함 하는 10 개의 자격 증명 모음을 권장 합니다.  모든 트랜잭션 유형에 대 한 구독 전체 제한은 개별 key vault 제한의 5 배입니다. 예를 들어 HSM-구독 당 다른 트랜잭션은 구독 당 10 초 내에 5000 트랜잭션으로 제한 됩니다. 서비스 또는 앱 내에서 암호를 캐시 하 여 키 자격 증명 모음에 대 한 RPS를 직접 줄이거나 버스트 기반 트래픽을 처리할 수 있습니다.  또한 서로 다른 지역 간에 트래픽을 분할 하 여 대기 시간을 최소화 하 고 다른 구독/자격 증명 모음을 사용할 수 있습니다.  단일 Azure 지역에서 Key Vault 서비스에 구독 제한을 초과 하 여 전송 하지 마세요.
1. Azure Key Vault에서 검색 한 암호를 메모리에 캐시 하 고 가능한 경우 메모리에서 다시 사용 합니다.  캐시 된 복사의 작동이 중지 되는 경우에만 Azure Key Vault에서 다시 읽습니다 (예: 소스에서 회전 한 경우). 
1. Key Vault는 사용자 고유의 서비스 비밀을 위해 설계 되었습니다.   고객의 암호를 저장 하는 경우 (특히 처리량이 높은 키 저장소 시나리오의 경우) 암호화 된 데이터베이스 또는 저장소 계정에 키를 배치 하 고 Azure Key Vault에 마스터 키만 저장 하는 것이 좋습니다.
1. Key Vault에 대 한 액세스 없이 공개 키 작업을 암호화 하 고, 래핑하고, 확인 하는 작업을 수행할 수 있습니다 .이 작업은 제한의 위험을 줄일 뿐만 아니라, 안정성도 향상 됩니다 (공개 키 자료를 적절 하 게 캐시 하는 동안).
1. Key Vault를 사용 하 여 서비스에 대 한 자격 증명을 저장 하는 경우 해당 서비스가 Azure AD 인증를 지원 하는지 확인 하 여 직접 인증 합니다. 이렇게 하면 Key Vault Azure AD 토큰을 사용할 수 있으므로 Key Vault 부하가 줄어들고 안정성이 향상 되 고 코드가 간소화 됩니다.  많은 서비스가 Azure AD 인증을 사용 하 여로 이동 했습니다.  [Azure 리소스에 대 한 관리 되는 id를 지 원하는 서비스](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)의 현재 목록을 참조 하세요.
1. 현재 RPS 한도를 유지 하기 위해 더 오랜 시간 동안 부하/배포의 시차를 고려 합니다.
1. 앱이 동일한 암호를 읽어야 하는 여러 노드로 구성 된 경우 한 엔터티가 Key Vault에서 비밀을 읽고 모든 노드로 팬 아웃 하는 팬 아웃 패턴을 사용 하는 것이 좋습니다.   검색 된 암호를 메모리에만 캐시 합니다.
위의 내용이 여전히 사용자의 요구를 충족 하지 않는 것으로 확인 되는 경우 아래 표를 입력 하 고 문의 하 여 추가 용량을 추가할 수 있는 용량을 확인 하세요 (아래 예제는 설명 목적 으로만 사용 가능).

| 자격 증명 모음 이름 | 자격 증명 모음 지역 | 개체 유형 (비밀, 키 또는 인증서) | 작업 * | 키 유형 | 키 길이 또는 곡선 | HSM 키 인가요?| 안정적인 상태 RPS 필요 | 최고 RPS 필요 |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | 키 | 로그인 | EC | P-256 | 아니요 | 200 | 1000 |

\*가능한 값의 전체 목록은 [Azure Key Vault 작업](/rest/api/keyvault/key-operations)을 참조 하세요.

추가 용량이 승인 되 면 용량 향상의 결과로 다음 사항에 유의 하세요.
1. 데이터 일관성 모델이 변경 됩니다. 추가 처리량 용량으로 자격 증명 모음을 허용 하는 경우에는 Key Vault 서비스 데이터 일관성이 변경 됩니다 (기본 Azure Storage 서비스를 유지할 수 없어 더 높은 볼륨 RPS를 충족 시키는 데 필요).  간략하게 설명하자면,
  1. **허용 목록 사용**안 함: Key Vault 서비스에서 쓰기 작업의 결과를 반영 합니다 (예: SecretSet, CreateKey)를 후속 호출에서 즉시 호출 합니다 (예: SecretGet, KeySign).
  1. **허용 목록 사용**: Key Vault 서비스는 쓰기 작업의 결과를 반영 합니다 (예: SecretSet, CreateKey) 이후의 호출에서 60 초 이내 (예: SecretGet, KeySign).
1. 클라이언트 코드는 429 재시도에 대해 백오프 정책을 준수 해야 합니다. Key Vault 서비스를 호출 하는 클라이언트 코드는 429 응답 코드를 받을 때 Key Vault 요청을 즉시 다시 시도 하면 안 됩니다.  여기에 게시 된 Azure Key Vault 제한 지침은 429 Http 응답 코드를 받을 때 지 수 백오프를 적용 하는 것을 권장 합니다.

더 높은 제한 한도에 대한 유효한 비즈니스 사례가 있을 경우 Microsoft에 연락해 주세요.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>서비스 한도에 대응하여 앱을 제한하는 방법

다음은 서비스가 제한 될 때 구현 해야 하는 모범 **사례** 입니다.
- 요청당 작업 수를 줄입니다.
- 요청의 빈도를 줄입니다.
- 즉시 재시도를 방지합니다. 
    - 모든 요청은 사용량 한도에 누적됩니다.

앱의 오류 처리를 구현할 때는 HTTP 오류 코드 429를 사용하여 클라이언트 측 제한이 필요한지 감지하세요. HTTP 429 오류 코드와 함께 요청이 다시 실패하더라도 Azure 서비스 한도에 도달하게 됩니다. 권장되는 클라이언트 측 제한 방법을 계속 사용하여 성공할 때까지 요청을 다시 시도해 보세요.

지수 백오프를 구현하는 코드는 다음과 같습니다. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


클라이언트 c # 응용 프로그램에서이 코드를 사용 하는 것은 간단 합니다. 

### <a name="recommended-client-side-throttling-method"></a>권장되는 클라이언트 측 제한 방법

HTTP 오류 코드 429가 발생할 경우 지수 백오프 접근법을 사용하여 클라이언트 제한을 시작하세요.

1. 1초를 기다렸다가 요청을 다시 시도합니다.
2. 그래도 제한된 경우 2초를 기다렸다가 요청을 다시 시도합니다.
3. 그래도 제한된 경우 4초를 기다렸다가 요청을 다시 시도합니다.
4. 그래도 제한된 경우 8초를 기다렸다가 요청을 다시 시도합니다.
5. 그래도 제한된 경우 16초를 기다렸다가 요청을 다시 시도합니다.

이때 HTTP 429 응답 코드가 발생해서는 안 됩니다.

## <a name="see-also"></a>참고 항목

Microsoft Cloud의 제한에 대한 더 자세한 소개는 [제한 패턴](https://docs.microsoft.com/azure/architecture/patterns/throttling)을 참조하세요.

