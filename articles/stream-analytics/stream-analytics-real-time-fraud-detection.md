---
title: 자습서 - Azure Stream Analytics를 사용하여 사기 전화 데이터를 분석하고 Power BI 대시보드에 결과 시각화
description: 이 자습서에서는 Azure Stream Analytics를 사용하여 전화 통화 스트림의 사기성 호출을 분석하는 방법에 대한 엔드투엔드 데모를 제공합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018873"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>자습서: Stream Analytics를 사용하여 사기 전화 데이터를 분석하고 Power BI 대시보드에 결과 시각화

이 자습서에서는 Azure Stream Analytics를 사용하여 전화 통화 데이터를 분석하는 방법을 보여줍니다. 클라이언트 애플리케이션에서 생성한 전화 통화 데이터에는 사기 전화가 포함되며 이는 Stream Analytics 작업을 통해 필터링됩니다. 신용카드 사기 또는 ID 도용 같은 다른 유형의 부정 행위 감지에 이 자습서의 기법을 사용할 수 있습니다.

이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 샘플 전화 통화 데이터를 생성하여 Azure Event Hubs로 보냅니다.
> * Stream Analytics 작업을 만듭니다.
> * 작업 입력 및 출력을 구성합니다.
> * 사기 전화를 필터링하는 쿼리를 정의합니다.
> * 작업을 테스트하고 시작합니다.
> * Power BI에서 결과를 시각화합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 단계를 완료해야 합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Microsoft 다운로드 센터에서 전화 통화 이벤트 생성기 앱 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)을 다운로드하거나 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)에서 소스 코드를 가져옵니다.
* Power BI 계정이 필요합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기

Stream Analytics가 사기성 호출 데이터 스트림을 분석하려면 Azure로 데이터를 보내야 합니다. 이 자습서에서는 [Azure Event Hubs](../event-hubs/event-hubs-about.md)를 사용하여 Azure로 데이터를 보낼 것입니다.

다음 단계에 따라 이벤트 허브를 만들고 해당 이벤트 허브로 호출 데이터를 전송합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** > **사물 인터넷** > **Event Hubs** 를 선택합니다.

   ![포털에서 Azure Event Hub 만들기](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. 다음 값으로 **네임스페이스 만들기** 창을 채웁니다.

   |**설정**  |**제안 값** |**설명**  |
   |---------|---------|---------|
   |속성     | asaTutorialEventHub        |  이벤트 허브 네임스페이스를 식별하는 고유 이름입니다.       |
   |Subscription     |   \<Your subscription\>      |   이벤트 허브를 만들 Azure 구독을 선택합니다.      |
   |Resource group     |   MyASADemoRG      |  **새로 만들기** 를 선택하고 계정의 새로운 리소스 그룹 이름을 입력합니다.       |
   |위치     |   미국 서부2      |    이벤트 허브 네임스페이스를 배포할 수 있는 위치입니다.     |

4. 나머지 설정에서는 기본 옵션을 사용하고 **검토 + 만들기** 를 선택합니다. 그런 다음, **만들기** 를 선택하여 배포를 시작합니다.

   ![Azure Portal에서 이벤트 허브 네임스페이스 만들기](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. 네임스페이스가 배포가 완료되면 **모든 리소스** 로 이동하여 Azure 리소스 목록에서 *asaTutorialEventHub* 를 찾습니다. *asaTutorialEventHub* 를 선택하여 엽니다.

6. 다음으로, **+Event Hub** 를 선택하고 Event Hub에 대한 **이름** 을 입력합니다. **파티션 수** 를 2로 설정합니다.  나머지 설정에서 기본 옵션을 사용하고 **만들기** 를 선택합니다. 배포가 성공할 때까지 기다립니다.

   ![Azure Portal의 이벤트 허브 구성](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>이벤트 허브에 대한 액세스 부여 및 연결 문자열 가져오기

애플리케이션에서 Event Hubs로 데이터를 보낼 수 있으려면 이벤트 허브에 액세스 권한을 허용하는 정책이 있어야 합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1. 이전 단계인 *MyEventHub* 에서 만든 이벤트 허브로 이동합니다. **설정** 아래에서 **공유 액세스 정책** 을 선택한 다음, **+ 추가** 를 선택합니다.

2. 정책 이름을 **MyPolicy** 로 지정하고 **관리** 확인란을 선택합니다. 그런 다음 **만들기** 를 선택합니다.

   ![이벤트 허브 공유 액세스 정책 만들기](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. 정책이 생성되면 정책 이름을 선택하여 정책을 엽니다. **연결 문자열 - 기본 키** 를 찾습니다. 연결 문자열 옆에 있는 **복사** 단추를 선택합니다.

   ![공유 액세스 정책 연결 문자열 저장](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. 연결 문자열을 텍스트 편집기에 붙여넣습니다. 그 다음 섹션에서 이 연결 문자열이 필요합니다.

   연결 문자열은 다음과 비슷합니다.

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   연결 문자열에는 **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey**, **EntityPath** 등 여러 개의 키-값 쌍이 세미콜론으로 구분되어 포함됩니다.

## <a name="start-the-event-generator-application"></a>이벤트 생성기 애플리케이션 시작

TelcoGenerator 앱을 시작하기 전에, 앞에서 만든 Azure Event Hubs로 데이터를 전송하도록 앱을 구성해야 합니다.

1. [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) 파일의 콘텐츠를 추출합니다.
2. 원하는 텍스트 편집기에서 `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` 파일을 엽니다. `.config` 파일이 여러 개 있으므로 올바른 파일을 열어야 합니다.

3. 구성 파일의 `<appSettings>` 요소를 다음 세부 정보로 업데이트합니다.

   * *EventHubName* 키의 값을 연결 문자열의 EntityPath 값으로 설정합니다.
   * *Microsoft.ServiceBus.ConnectionString* 키 값을 EntityPath 값을 뺀 연결 문자열로 설정합니다. EntityPath 값 앞에 오는 세미콜론은 제거해야 합니다.

4. 파일을 저장합니다.

5. 명령 창을 열고 TelcoGenerator 애플리케이션 압축을 푼 폴더로 변경합니다. 다음 명령을 입력합니다.

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   이 명령은 다음 매개 변수를 사용합니다.
   * 시간당 호출 데이터 레코드 수.
   * 사기 확률 - 즉, 앱에서 사기성 호출을 시뮬레이션해야 하는 빈도. 값 0.2는 호출 레코드의 약 20%가 사기성으로 나타남을 의미합니다.
   * 기간(시간) - 앱을 실행해야 하는 시간. 명령줄에서 프로세스(**Ctrl+C**)를 종료하여 언제든지 앱을 중지할 수도 있습니다.

   몇 초 후 앱에서 이벤트 허브로 데이터를 전송함에 따라 화면에 전화 통화 레코드가 표시되기 시작합니다. 이 전화 통화 데이터에는 다음 필드가 포함되어 있습니다.

   |**레코드**  |**정의**  |
   |---------|---------|
   |CallrecTime    |  호출 시작 시간에 대한 타임스탬프       |
   |SwitchNum     |  호출 연결에 사용되는 전화 스위치. 이 예에서는 스위치는 발신 국가/지역(미국, 중국, 영국, 독일 또는 오스트레일리아)를 나타내는 문자열입니다.       |
   |CallingNum     |  호출자의 전화번호.       |
   |CallingIMSI     |  국제 모바일 구독자 ID(IMSI) 호출자의 고유 식별자.       |
   |CalledNum     |   호출 수신자의 전화번호.      |
   |CalledIMSI     |  국제 모바일 구독자 ID(IMSI) 호출 수신자의 고유 식별자.       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

이제 호출 이벤트 스트림이 생겼으니, 이벤트 허브에서 데이터를 읽는 Stream Analytics 작업을 만들 수 있습니다.

1. Stream Analytics 작업을 만들려면 [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. **리소스 만들기** 를 선택하고 **Stream Analytics 작업** 을 검색합니다. **Stream Analytics 작업** 타일을 선택하고 *만들기**를 선택합니다.

3. **새 Stream Analytics 작업** 양식을 다음 값으로 채웁니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |작업 이름     |  ASATutorial       |   이벤트 허브 네임스페이스를 식별하는 고유 이름입니다.      |
   |Subscription    |  \<Your subscription\>   |   작업을 만들 Azure 구독을 선택합니다.       |
   |Resource group   |   MyASADemoRG      |   **기존 항목 사용** 을 선택하고 계정의 새로운 리소스 그룹 이름을 입력합니다.      |
   |위치   |    미국 서부2     |      작업을 배포할 수 있는 위치입니다. 최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.      |
   |호스팅 환경    | 클라우드        |     Stream Analytics 작업은 클라우드 또는 에지에 배포할 수 있습니다. 클라우드를 사용하면 Azure 클라우드에 배포할 수 있고, 에지를 사용하면 IoT Edge 디바이스에 배포할 수 있습니다.    |
   |스트리밍 단위     |    1       |      스트리밍 단위는 작업을 실행하는 데 필요한 컴퓨팅 리소스를 나타냅니다. 기본적으로 이 값은 1로 설정됩니다. 스트리밍 단위 크기를 조정하는 방법에 대한 자세한 내용은 [스트리밍 단위의 이해 및 크기 조정](stream-analytics-streaming-unit-consumption.md) 문서를 참조하세요.      |

4. 나머지 설정에서는 기본 옵션을 사용하고 **만들기** 를 선택한 후 배포가 완료될 때까지 기다립니다.

   ![Azure Stream Analytics 작업 만들기](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>작업 입력 구성

다음 단계는 이전 섹션에서 만든 이벤트 허브를 사용하여 작업이 데이터를 읽을 입력 원본을 정의하는 것입니다.

1. Azure Portal에서 **모든 리소스** 페이지를 열고 *ASATutorial* Stream Analytics 작업을 찾습니다.

2. Stream Analytics 작업의 **작업 토폴로지** 섹션에서 **입력** 을 선택합니다.

3. **+ 스트림 입력 추가** 를 선택하고 **이벤트 허브** 를 선택합니다. 다음 값으로 입력 양식을 채웁니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |입력 별칭     |  CallStream       |  입력을 식별하는 표시 이름. 입력 별칭은 영숫자 문자, 하이픈, 밑줄만 사용할 수 있으며 길이가 3자에서 63자 사이여야 합니다.       |
   |Subscription    |   \<Your subscription\>      |   이벤트 허브를 만든 Azure 구독을 선택합니다. 이벤트 허브는 Stream Analytics 작업과 같은 구독일 수도 있고 다른 구독일 수도 있습니다.       |
   |이벤트 허브 네임스페이스    |  asaTutorialEventHub       |  이전 섹션에서 만든 이벤트 허브 네임스페이스를 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 허브 네임스페이스가 드롭다운에 나열됩니다.       |
   |이벤트 허브 이름    |   MyEventHub      |  이전 섹션에서 만든 이벤트 허브를 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 허브가 드롭다운에 나열됩니다.       |
   |이벤트 허브 정책 이름   |  MyPolicy       |  이전 섹션에서 만든 이벤트 허브 공유 액세스 정책을 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 정책이 드롭다운에 나열됩니다.       |

4. 나머지 설정에서는 기본 옵션을 사용하고 **저장** 을 선택합니다.

   ![Azure Stream Analytics 입력 구성](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>작업 출력 구성

마지막 단계는 작업이 변환된 데이터를 쓸 수 있는 출력 싱크를 정의하는 것입니다. 이 자습서에서는 Power BI를 사용하여 데이터를 출력하고 시각화합니다.

1. Azure Portal에서 **모든 리소스** 를 열고 *ASATutorial* Stream Analytics 작업을 선택합니다.

2. Stream Analytics 작업의 **작업 토폴로지** 섹션에서 **출력** 옵션을 선택합니다.

3. **+ 추가** > **Power BI** 를 선택합니다. 그런 다음, **권한 부여** 를 선택하고 프롬프트에 따라 Power BI를 인증합니다.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Power BI에 대한 권한 부여 단추":::

4. 다음 세부 정보로 출력 양식을 채우고 **저장** 을 선택합니다.

   |**설정**  |**제안 값**  |
   |---------|---------|
   |출력 별칭  |  MyPBIoutput  |
   |그룹 작업 영역| 내 작업 영역 |
   |데이터 세트 이름  |   ASAdataset  |
   |테이블 이름 |  ASATable  |
   | 인증 모드 | 사용자 토큰 |

   ![Stream Analytics 출력 구성](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   이 자습서에서는 *사용자 토큰* 인증 모드를 사용합니다. 관리 ID를 사용하려면 [관리 ID를 사용하여 Power BI에 대한 Azure Stream Analytics 작업 인증](powerbi-output-managed-identity.md)을 참조하세요.

## <a name="create-queries-to-transform-real-time-data"></a>실시간 데이터를 변환하는 쿼리 만들기

이 시점에는 들어오는 데이터 스트림을 읽도록 설정된 Stream Analytics 작업이 있습니다. 다음 단계는 실시간으로 데이터를 분석하는 쿼리를 만드는 것입니다. 쿼리는 Stream Analytics와 관련된 일부 확장 기능을 포함한 SQL과 유사한 언어를 사용합니다.

자습서의 이 섹션에서는 분석을 위해 입력 스트림을 변환할 수 있는 몇 가지 방법에 대해 알아보기 위해 여러 쿼리를 작성 및 테스트합니다. 

여기서 작성한 쿼리는 화면에 변환된 데이터를 표시하기만 합니다. 이후 섹션에서는 변환된 데이터를 Power BI에 기록합니다.

이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요.

### <a name="test-using-a-pass-through-query"></a>통과 쿼리를 사용하여 테스트

모든 이벤트를 보관하려는 경우 통과 쿼리를 사용하여 이벤트의 페이로드에서 모든 필드를 읽을 수 있습니다.

1. Azure Portal의 Stream Analytics 작업으로 이동하고 *작업 토폴로지* 에서 **쿼리** 를 선택합니다. 

2. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >SQL처럼 키워드는 대/소문자를 구분하지 않고 공백은 중요하지 않습니다.

    쿼리에서 `CallStream`은 입력을 만들 때 지정한 별칭입니다. 다른 별칭을 사용하는 경우 대신 해당 이름을 사용합니다.

3. **테스트 쿼리** 를 선택합니다.

    Stream Analytics 작업이 입력의 샘플 데이터에 대해 쿼리를 실행하고 창 아래쪽에 출력을 표시합니다. 결과에는 이벤트 허브 및 Stream Analytics 작업이 올바르게 구성되었다고 표시됩니다.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="테스트 쿼리의 샘플 출력":::

    표시되는 레코드의 정확한 개수는 샘플에 캡처된 레코드 수에 따라 달라집니다.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>열 프로젝션을 사용하여 필드 수 줄이기

대부분의 경우 분석 시에 입력 스트림의 모든 열이 필요하지는 않습니다. 쿼리를 사용하여 통과 쿼리에서보다 더 작은 집합의 반환된 필드를 프로젝션할 수 있습니다.

다음 쿼리를 실행하고 출력을 확인합니다.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>지역별로 수신 전화 수 계산: 집계가 있는 연속 창

지역당 들어오는 호출의 수를 계산한다고 가정합니다. 스트리밍 데이터에서 개수와 같은 집계 함수를 수행하려는 경우 데이터 스트림 자체가 사실상 무한하므로 스트림을 시간 단위로 분할해야 합니다. Streaming Analytics [window 함수](stream-analytics-window-functions.md)를 사용하여 이 작업을 수행합니다. 그런 다음 해당 창 내에서 데이터를 단위로 작업할 수 있습니다.

이 변환의 경우 겹치지 않는 temporal 창 시퀀스를 원하며 각 창에는 그룹화 및 집계할 수 있는 불연속 데이터 집합이 있습니다. 이러한 형식의 창을 *연속 창* 이라고 합니다. 연속 창 내에서 호출이 시작된 국가/지역을 나타내는 `SwitchNum`으로 그룹화된 들어오는 호출 수를 가져올 수 있습니다.

1. 쿼리 편집기에 다음 쿼리를 붙여넣습니다.

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    이 쿼리에서는 `FROM` 절에 `Timestamp By` 키워드를 사용하여 연속 창을 정의하는 데 사용할 입력 스트림의 타임스탬프 필드를 지정합니다. 이 경우 창은 각 레코드의 `CallRecTime` 필드에 따라 데이터를 세그먼트로 나눕니다. (이 필드를 지정하지 않으면 창 작업에서 각 이벤트가 이벤트 허브에 도착한 시간을 사용합니다.) [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)에서 “도착 시간과 애플리케이션 시간”을 참조하세요. 

    프로젝션에는 각 창의 끝에 대한 타임스탬프를 반환하는 `System.Timestamp`가 포함됩니다. 

    연속 창을 사용할 것인지를 지정하려면 `GROUP BY`절에 [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) 함수를 사용합니다. 함수에서 시간 단위(마이크로초에서 하루까지) 및 창 크기(단위 수)를 지정합니다. 이 예에서 연속 창은 5초 간격으로 구성되므로 5초 분량의 호출에 대한 국가/지역별 개수를 가져옵니다.

2. **테스트 쿼리** 를 선택합니다. 결과에서 **WindowEnd** 아래 타임스탬프가 5초 단위로 증가하는 것을 알 수 있습니다.

### <a name="detect-sim-fraud-using-a-self-join"></a>셀프 조인을 사용하여 SIM 사기 감지

이 예제에서는 5초 이내에 서로 다른 위치에서 동일한 사용자로부터 발생한 호출을 사기성 있는 사용으로 간주합니다. 예를 들어 동일한 사용자가 미국 및 오스트레일리아에서 동시에 합법적으로 전화를 걸 수 없습니다.

이러한 경우를 확인하려면 스트리밍 데이터의 셀프 조인을 사용하여 `CallRecTime` 값에 따라 스트림을 셀프 조인합니다. 그런 다음 `CallingIMSI` 값(발신 번호)이 동일하지만 `SwitchNum` 값(발신 국가/지역)은 다른 호출 레코드를 찾을 수 있습니다.

스트리밍 데이터에 조인을 사용할 경우 조인은 일치하는 행이 시간상으로 얼마나 분리할 수 있는지 정도에 대한 몇 가지 한도를 제공해야 합니다. 앞에서 설명한 대로 스트리밍 데이터는 사실상 무한합니다. 관계에 대한 시간 범위는 조인의 `ON` 절 내부에 `DATEDIFF` 함수를 사용하여 지정됩니다. 이 경우 조인은 호출 데이터의 5초 간격을 기준으로 합니다.

1. 쿼리 편집기에 다음 쿼리를 붙여넣습니다.

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    이 쿼리는 조인에서 `DATEDIFF` 함수를 제외하고 SQL 조인과 유사합니다. 이 `DATEDIFF` 버전은 Streaming Analytics에 국한되며 `ON...BETWEEN` 절에 표시되어야 합니다. 매개 변수는 시간 단위(이 예제에서는 초)와 조인의 두 원본에 대한 별칭입니다. 표준 SQL `DATEDIFF` 함수와는 다릅니다.

    `WHERE` 절에는 사기성 호출에 플래그를 지정하는 조건이 포함되며 발신 스위치는 동일하지 않습니다.

2. **테스트 쿼리** 를 선택합니다. 출력을 검토한 다음, **쿼리 저장** 을 선택합니다.

## <a name="start-the-job-and-visualize-output"></a>작업을 시작하고 출력을 시각화

1. 작업을 시작하려면 작업 **개요** 로 이동하여 **시작** 을 선택합니다.

2. 작업 출력 시작 시간으로 **지금** 을 선택하고 **시작** 을 선택합니다. 알림 표시줄에서 작업 상태를 볼 수 있습니다.

3. 작업이 성공하면 [Power BI](https://powerbi.com/)로 이동하여 회사 또는 학교 계정으로 로그인합니다. Stream Analytics 작업 쿼리가 결과를 출력 중이면 앞에서 만든 *ASAdataset* 데이터 세트가 **데이터 세트** 탭에 있는 것입니다.

4. Power BI 작업 영역에서 **+ 만들기** 를 선택하여 *사기성 호출* 이라는 새 대시보드를 만듭니다.

5. 창 맨 위에서 **편집** 및 **타일 추가** 를 선택합니다. **사용자 지정 스트리밍 데이터** 를 선택하고 **다음** 을 선택합니다. **데이터 세트** 아래에서 **ASAdataset** 를 선택합니다. **시각화 유형** 드롭다운에서 **카드** 를 선택하고 **필드** 에 **사기성 호출** 을 추가합니다. **다음** 을 선택하여 타일 이름을 입력하고, **적용** 을 선택하여 타일을 만듭니다.

   ![Power BI 대시보드 타일 만들기](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. 다음 옵션을 사용하여 5단계를 다시 수행합니다.
   * 시각화 유형으로 꺾은선형 차트를 선택합니다.
   * 축을 추가하고 **windowend** 를 선택합니다.
   * 값을 추가하고 **fraudulentcalls** 를 선택합니다.
   * **표시할 시간 창** 에 지난 10분을 선택합니다.

7. 두 타일이 모두 추가되면 대시보드가 아래 예제처럼 보입니다. 이벤트 허브 발신자 애플리케이션 및 Stream Analytics 애플리케이션이 실행 중이면 새 데이터가 도착할 때마다 Power BI 대시보드가 주기적으로 업데이트됩니다.

   ![Power BI 대시보드에서 결과 보기](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>웹 애플리케이션에 Power BI 대시보드 포함

자습서의 이 부분에서는 Power BI 팀에서 만든 [ASP.NET](https://asp.net/) 웹 애플리케이션을 사용하여 대시보드를 포함할 것입니다. 대시보드를 포함하는 방법에 대한 자세한 내용은 [Power BI를 통해 포함](/power-bi/developer/embedding) 문서를 참조하세요.

애플리케이션을 설정하려면 [PowerBI-개발자-샘플](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub 리포지토리로 이동하여 **사용자 소유 데이터** 섹션 아래의 지침을 따릅니다(**통합-웹앱** 하위 섹션 아래에 있는 리디렉션 및 홈페이지 URL 사용). 우리는 대시보드 예제를 사용할 예정이므로 [GitHub 리포지토리](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/)에 있는 **통합-웹앱** 샘플 코드를 사용합니다.
브라우저에서 애플리케이션을 실행한 후에는 다음 단계에 따라 앞에서 만든 대시보드를 웹 페이지에 포함합니다.

1. **Power BI에 로그인** 을 선택합니다. 그러면 Power BI 계정에 대시보드에 대한 애플리케이션 액세스 권한이 부여됩니다.

2. **대시보드 가져오기** 단추를 선택합니다. 그러면 계정의 대시보드가 테이블에 표시됩니다. 앞에서 만든 대시보드 이름(**powerbi-embedded-dashboard**)을 찾아서 해당 **EmbedUrl** 을 복사합니다.

3. 마지막으로 **EmbedUrl** 을 해당 텍스트 필드에 붙여넣고 **대시보드 포함** 을 선택합니다. 이제 웹 애플리케이션에 동일한 대시보드가 포함된 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 간단한 Stream Analytics 작업을 만들고, 들어오는 데이터를 분석하고, 그 결과를 Power BI 대시보드에 표시했습니다. Stream Analytics 작업에 대해 자세히 알아보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Stream Analytics 작업에서 Azure Functions 실행](stream-analytics-with-azure-functions.md)
