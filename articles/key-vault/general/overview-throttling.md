---
title: Azure Key Vault 제한 지침
description: Key Vault 제한은 리소스의 과용을 방지하기 위해 동시 호출 수를 제한합니다.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749538"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 제한 지침

제한은 리소스의 과용을 방지하기 위해 Azure 서비스에 대한 동시 호출 수를 제한하는 사용자 시작 프로세스입니다. Azure Key Vault(AKV)는 많은 양의 요청을 처리하도록 설계되었습니다. 매우 많은 수의 요청이 발생할 경우 클라이언트의 요청을 제한하면 AKV 서비스의 최적의 성능 및 안정성을 유지하는 데 도움이 됩니다.

제한 한도는 시나리오에 따라 다릅니다. 예를 들어, 대용량 쓰기를 수행하는 경우 읽기만 수행하는 경우보다 제한 가능성이 높습니다.

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault가 한도를 처리하는 방법

Key Vault의 서비스 한도는 리소스의 오용을 방지하고 모든 Key Vault 클라이언트의 서비스 품질을 보장합니다. 서비스 임계값이 초과되면 Key Vault는 일정 기간 동안 해당 클라이언트의 추가 요청을 제한하고 HTTP 상태 코드 429(요청이 너무 많음)를 반환하며 요청이 실패합니다. 429를 반환한 실패한 요청은 Key Vault에서 추적하는 제한 한도에 포함되지 않습니다. 

Key Vault는 원래 배포 시 비밀을 저장하고 검색하는 데 사용하도록 설계되었습니다.  세상은 진화했으며 Key Vault는 런타임에 비밀을 저장하고 검색하는 데 사용되고 있으며, 종종 앱과 서비스는 Key Vault를 데이터베이스처럼 사용하려고 합니다.  현재 한도는 높은 처리량 속도를 지원하지 않습니다.

Key Vault는 원래 [Azure Key Vault 서비스 제한](service-limits.md)에 지정된 한도에 의해 만들어졌습니다.  Key Vault 처리량 속도를 최대화하려면 처리량을 최대화하기 위한 몇 가지 권장 지침/모범 사례가 있습니다.
1. 적절한 대역폭 제한을 사용하는지 확인합니다.  클라이언트가 429에 대한 지수 백오프 정책을 준수하고 아래 지침에 따라 다시 시도를 수행하고 있는지 확인해야 합니다.
1. Key Vault 트래픽을 여러 자격 증명 모음 및 다른 지역으로 나눕니다.   각 보안/가용성 도메인에 대해 별도의 자격 증명 모음을 사용합니다.   두 지역에 각각 5개의 앱이 있는 경우 앱 및 지역에 고유한 비밀이 포함된 자격 증명 모음 10개를 권장합니다.  모든 트랜잭션 유형에 대한 구독 차원의 한도는 개인 키 자격 증명 모음 한도의 5배입니다. 예를 들어 구독당 HSM-기타 트랜잭션은 구독당 10초 내에 5,000개 트랜잭션으로 제한됩니다. 서비스 또는 앱 내에서 비밀을 캐싱하여 Key Vault에 대한 RPS를 직접 줄이거나 버스트 기반 트래픽을 처리하는 것을 고려합니다.  또한 트래픽을 여러 지역으로 나누어 대기 시간을 최소화하고 다른 구독/자격 증명 모음을 사용할 수 있습니다.  단일 Azure 지역의 Key Vault 서비스에 구독 제한보다 많이 보내지 마세요.
1. Azure Key Vault에서 검색한 비밀을 메모리에 캐시하고 가능하면 메모리에서 다시 사용합니다.  캐시된 복사본이 작동을 멈춘(예: 원본에서 회전되었기 때문에) 경우에만 Azure Key Vault에서 다시 읽습니다. 
1. Key Vault는 사용자 고유의 서비스 비밀을 위해 설계되었습니다.   고객의 비밀을 저장하는 경우(특히 처리량이 높은 키 저장소 시나리오의 경우) 암호화된 데이터베이스 또는 스토리지 계정에 키를 배치하고 Azure Key Vault에는 마스터 키만 저장하는 것이 좋습니다.
1. Key Vault에 대한 액세스 없이 공개 키 작업을 암호화, 래핑 및 확인하여 제한 위험을 줄일 뿐만 아니라 안정성도 향상시킵니다(공개 키 자료를 적절하게 캐시하는 한).
1. Key Vault를 사용하여 서비스에 대한 자격 증명을 저장하는 경우 해당 서비스가 Azure AD 인증을 지원하여 직접 인증하는지 확인합니다. 이렇게 하면 Key Vault가 Azure AD 토큰을 사용할 수 있으므로 Key Vault의 부하가 줄어들고 안정성이 향상되며 코드가 간소화됩니다.  많은 서비스가 Azure AD Auth를 사용하도록 이동되었습니다. [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)에서 현재 목록을 참조하세요.
1. 현재 RPS 한도를 유지하기 위해 오랜 시간 동안 시차를 두고 로드/배포를 수행하는 것이 좋습니다.
1. 앱이 동일한 비밀을 읽어야 하는 여러 노드로 구성된 경우 한 엔터티가 Key Vault에서 비밀을 읽고 모든 노드로 팬 아웃하는 팬 아웃 패턴을 사용하는 것이 좋습니다.   검색된 비밀은 메모리에만 캐시합니다.
위의 내용이 여전히 귀하의 요구 사항을 충족하지 않는 경우 아래 표를 작성하고 추가할 수 있는 용량을 확인하려면 당사에 문의하세요(아래는 예시용으로만 기재).

| 자격 증명 모음 이름 | 자격 증명 모음 지역 | 개체 유형(비밀, 키 또는 인증서) | 작업* | 키 유형 | 키 길이 또는 곡선 | HSM 키?| 안정적인 상태 RPS 필요 | 최고 RPS 필요 |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | 키 | 로그인 | EC | P-256 | 예 | 200 | 1000 |

\* 가능한 값의 전체 목록은 [Azure Key Vault 작업](/rest/api/keyvault/key-operations)을 참조하세요.

추가 용량이 승인되면 용량 증가의 결과로 다음 사항에 유의하세요.
1. 데이터 일관성 모델이 변경됩니다. 자격 증명 모음이 추가 처리 용량으로 허용 목록에 추가되면 Key Vault 서비스 데이터 일관성이 변경됩니다(기본 Azure Storage 서비스가 따라 잡을 수 없으므로 더 높은 볼륨 RPS를 충족하는 데 필요함).  간략하게 설명하자면,
  1. **허용 목록 사용 안 함**: Key Vault 서비스는 후속 호출(예: SecretGet, KeySign)에서 즉시 쓰기 작업(예: SecretSet, CreateKey)의 결과를 반영합니다.
  1. **허용 목록 사용**: Key Vault 서비스는 후속 호출(예: SecretGet, KeySign)에서 60초 이내에 쓰기 작 (예 : SecretSet, CreateKey)의 결과를 반영합니다.
1. 클라이언트 코드는 429 재시도에 대해 백오프 정책을 준수해야 합니다. Key Vault 서비스를 호출하는 클라이언트 코드는 429 응답 코드를 수신할 때 Key Vault 요청을 즉시 다시 시도해서는 안 됩니다.  여기에 게시된 Azure Key Vault 제한 지침은 429 Http 응답 코드를 수신할 때 지수 백오프를 적용하도록 권장합니다.

더 높은 제한 한도에 대한 유효한 비즈니스 사례가 있을 경우 Microsoft에 연락해 주세요.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>서비스 한도에 대응하여 앱을 제한하는 방법

다음은 서비스가 제한될 때 구현해야 하는 **모범 사례** 입니다.
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
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


클라이언트 C# 애플리케이션에서 이 코드를 사용하는 것은 간단합니다. 

### <a name="recommended-client-side-throttling-method"></a>권장되는 클라이언트 측 제한 방법

HTTP 오류 코드 429가 발생할 경우 지수 백오프 접근법을 사용하여 클라이언트 제한을 시작하세요.

1. 1초를 기다렸다가 요청을 다시 시도합니다.
2. 그래도 제한된 경우 2초를 기다렸다가 요청을 다시 시도합니다.
3. 그래도 제한된 경우 4초를 기다렸다가 요청을 다시 시도합니다.
4. 그래도 제한된 경우 8초를 기다렸다가 요청을 다시 시도합니다.
5. 그래도 제한된 경우 16초를 기다렸다가 요청을 다시 시도합니다.

이때 HTTP 429 응답 코드가 발생해서는 안 됩니다.

## <a name="see-also"></a>참조

Microsoft Cloud의 제한에 대한 더 자세한 소개는 [제한 패턴](/azure/architecture/patterns/throttling)을 참조하세요.
