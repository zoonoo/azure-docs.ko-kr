<properties
   pageTitle="NuGet 패키지 | Microsoft Azure"
   description="NuGet 패키지의 일반적인 재시도 정책 작업에 대한 지침입니다."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# NuGet 패키지

<p class="lead">더 많은 구성 요소가 통신하기 시작하면서, 일시적인 장애를 스마트하게 처리해야 할 중요성이 높아집니다. NuGet 패키지의 재시도 정책에 의해 처리되는 일시적인 오류 처리 작업은 단일 인스턴스 내에서 재시도를 처리하는 데 도움이 됩니다.</p>

> 이 문서는 개념 증명으로 초안을 기반으로 합니다. 실제 검토한 지침은 아닙니다.

패턴 및 사례 `TransientFaultHandling` 코드는 일반적인 재시도 정책 작업에 대해 권장됩니다.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## 구성

섹션에는 재시도 기능에 대한 구성 정보가 포함됩니다.

매개 변수 | 설명
-------------------- | ----------------------
MaximumExecutionTime | 모든 잠재적인 재시도를 포함하여 요청에 대한 최대 실행 시간
ServerTimeOut | 요청에 대한 서버 제한 시간 간격
RetryPolicy | 재시도 정책. 아래 정책 섹션 참조

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

프로그래밍 방식:

- 클라이언트에 대한 설정 지원
- 클라이언트에서 제공하는 작업에 대해 재정의 사용

구성 파일:

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## 정책

### 지수

서비스 제한을 피하기 위해 반복적인 서비스 호출 시도의 간격을 기하급수적으로 늘리는 데 사용됩니다.

__접근 방식:__

후속 시도 사이의 백오프 간격을 기하급수적으로 늘립니다. 백오프 간격에 불규칙을 (+/- 20%)까지 추가하여 모든 클라이언트가 동시에 다시 시도하는 것을 방지합니다.

__구성:__

매개 변수 | 설명
-------------------- | -------------------------------------------------------
maxAttempt | 재시도 횟수
deltaBackoff | 재시도 사이의 백오프 간격. 후속 재시도에 이 시간 범위의 배수가 사용됩니다.
MinBackoff | deltaBackoff에서 계산된 모든 다시 시도 간격에 추가됨
FastFirst | 즉각적인 첫 번째 재시도
MaxBackoff | MaxBackoff는 계산된 다시 시도 간격이 MaxBackoff보다 큰 경우에 사용됩니다. 이 값은 변경할 수 없습니다.

__구현 논리:__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## 선형

서비스 제한을 피하기 위해 반복적인 서비스 호출 시도의 간격을 선형적으로 늘리는 데 사용됩니다.

__접근 방식:__

재시도 사이의 지정된 고정 시간 간격을 사용하여 지정된 횟수만큼 다시 시도합니다. 백오프 간격에 불규칙을 (+/- 20%)까지 추가하여 모든 클라이언트가 동시에 다시 시도하는 것을 방지합니다.

__구성:__

매개 변수 | 설명
-------------------- | -------------------------------------------------------
maxAttempt | 재시도 횟수
deltaBackoff | 재시도 사이의 백오프 간격.
FastFirst | 즉각적인 첫 번째 재시도

__구현 논리:__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## 적응

서비스로 응답 헤더에 전달된 오류 코드/메타데이터를 기반으로 하여 반복적인 서비스 호출 시도의 간격을 늘리는 데 사용됩니다.

__접근 방식:__

서비스로 응답 헤더에 전달된 오류 코드/메타데이터를 기반으로 하여 계산된 백오프 간격을 사용해 지정된 횟수만큼 다시 시도합니다.


__구성:__

구성할 수 없음

__구현 논리:__

서비스에 의해 응답 헤더에 전달된 오류 코드/메타데이터 기반

__회로 차단:__

[회로 차단기](http://msdn.microsoft.com/library/dn589784.aspx) 기반

## 확장성

사용자 지정 재시도 정책을 제공하기 위해 구현할 수 있는 공용 인터페이스입니다.

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## 원격 분석

EventSource를 사용하여 재시도를 ETW 이벤트로 기록합니다. 모든 재시도에 대해 기록해야 하는 필드는 다음과 같습니다.

매개 변수 | 설명
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operation | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "9/5/2014 10:00:13 PM"
operationEndTime | "9/5/2014 10:00:14 PM"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "원격 이름을 확인할 수 없습니다: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=Oct15_HO3-->