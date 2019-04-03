---
title: Azure Key Vault 제한 지침
description: Key Vault 제한은 리소스의 과용을 방지하기 위해 동시 호출 수를 제한합니다.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 0f8aafce4c4feeed742504db84664e4dfd472ca6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884145"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 제한 지침

제한은 리소스의 과용을 방지하기 위해 Azure 서비스에 대한 동시 호출 수를 제한하는 사용자 시작 프로세스입니다. Azure Key Vault(AKV)는 많은 양의 요청을 처리하도록 설계되었습니다. 매우 많은 수의 요청이 발생할 경우 클라이언트의 요청을 제한하면 AKV 서비스의 최적의 성능 및 안정성을 유지하는 데 도움이 됩니다.

제한 한도는 시나리오에 따라 다릅니다. 예를 들어, 대용량 쓰기를 수행하는 경우 읽기만 수행하는 경우보다 제한 가능성이 높습니다.

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault가 한도를 처리하는 방법

Key Vault의 서비스 한도는 리소스의 오용을 방지하고 모든 Key Vault 클라이언트의 서비스 품질을 보장하기 위한 것입니다. 서비스 임계값이 초과되면 Key Vault가 해당 클라이언트에서 들어오는 추가 요청을 일정 시간 동안 제한합니다. 이 경우 Key Vault는 HTTP 상태 코드 429(너무 많은 요청)를 반환하고 요청은 실패합니다. 또한 429를 반환한 실패한 요청은 Key Vault에서 추적하는 제한 한도에 포함됩니다. 

더 높은 제한 한도에 대한 유효한 비즈니스 사례가 있을 경우 Microsoft에 연락해 주세요.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>서비스 한도에 대응하여 앱을 제한하는 방법

다음은 **모범 사례** 서비스는 제한 되는 경우 구현 해야 합니다.
- 요청당 작업 수를 줄입니다.
- 요청의 빈도를 줄입니다.
- 즉시 재시도를 방지합니다. 
    - 모든 요청은 사용량 한도에 누적됩니다.

앱의 오류 처리를 구현할 때는 HTTP 오류 코드 429를 사용하여 클라이언트 측 제한이 필요한지 감지하세요. HTTP 429 오류 코드와 함께 요청이 다시 실패하더라도 Azure 서비스 한도에 도달하게 됩니다. 권장되는 클라이언트 측 제한 방법을 계속 사용하여 성공할 때까지 요청을 다시 시도해 보세요.

지수 백오프를 구현하는 코드는 다음과 같습니다. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


C 클라이언트에서이 코드를 사용 하 여\# 응용 프로그램은 간단 합니다. 다음 예제는 HttpClient 클래스를 사용하는 방법을 보여 줍니다.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

이 코드는 개념 증명으로만 적합하다는 것을 잊지 마세요. 

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

