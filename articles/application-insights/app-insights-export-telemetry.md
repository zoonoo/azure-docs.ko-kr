---
title: "Application Insights에서 원격 분석 연속 내보내기 | Microsoft Docs"
description: "Microsoft Azure에서 저장소에 진단 및 사용량 데이터를 내보내고 여기에서 다운로드합니다."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7bd26ffdec185a1ebd71fb88383c2ae4cd6d504f
ms.openlocfilehash: 3b1654355ee84610d25da8b6ad4a66036285faba


---
# <a name="export-telemetry-from-application-insights"></a>Application Insights에서 원격 분석 내보내기
표준 보존 기간 보다 오랫동안 원격 분석을 유지하시겠습니까? 또는 일부 특수한 방식으로 처리하시겠습니까? 그렇다면 연속 내보내기가 적합합니다. Application Insights 포털에 표시되는 이벤트는 JSON 형식으로 Microsoft Azure에서 저장소로 내보낼 수 있습니다. 여기에서 데이터를 다운로드하고 프로세스에 필요한 모든 코드를 작성할 수 있습니다.  

연속 내보내기는 [엔터프라이즈 가격 책정 모델](http://azure.microsoft.com/pricing/details/application-insights/)에서 사용할 수 있습니다.

연속 내보내기를 설정하기 전에 고려하려는 일부 대안이 있습니다.

* [내보내기 단추](app-insights-metrics-explorer.md#export-to-excel) 를 사용하면 테이블 및 차트를 Excel 스프레드시트에 전송할 수 있습니다. 

* [분석](app-insights-analytics.md) 은 원격 분석을 위한 강력한 쿼리 언어를 제공하고 또한 결과를 내보낼 수 있습니다.
* [Power BI에서 데이터를 탐색](app-insights-export-power-bi.md)하려는 경우 연속 내보내기를 사용하지 않고 탐색할 수 있습니다.
* [데이터 액세스 REST API](https://dev.applicationinsights.io/)를 사용하여 원격 분석에 프로그래밍 방식으로 액세스할 수 있습니다. 

연속 내보내기를 통해 저장소에 데이터를 복사한 후에도(원하는 기간 동안 저장소에 유지할 수 있음) 일반적인 [보존 기간](app-insights-data-retention-privacy.md) 동안 Application Insights를 계속 사용할 수 있습니다. 

## <a name="create-a-storage-account"></a>저장소 계정 만들기
"클래식" 저장소 계정이 없는 경우 지금 만듭니다.

1. [Azure 포털](https://portal.azure.com)에서 구독에 저장소 계정을 만듭니다.
   
    ![Azure 포털에서 새로 만들기, 데이터, 저장소 선택](./media/app-insights-export-telemetry/030.png)
2. 컨테이너를 만듭니다.
   
    ![새 저장소에서 컨테이너를 선택하고 컨테이너 타일, 추가를 차례로 클릭합니다.](./media/app-insights-export-telemetry/040.png)

Application Insights 리소스와 다른 지역에서 저장소를 만든 경우 [데이터 전송 요금이 부과](https://azure.microsoft.com/pricing/details/bandwidth/)될 수 있습니다.

## <a name="a-namesetupa-set-up-continuous-export"></a><a name="setup"></a> 연속 내보내기 설정
Application Insights 포털의 응용 프로그램 개요 블레이드에서 연속 내보내기를 엽니다. 

![아래로 스크롤하여 연속 내보내기 클릭](./media/app-insights-export-telemetry/01-export.png)

연속 내보내기를 추가하고 내보내려는 이벤트 유형을 선택합니다.

![추가, 내보내기 대상, 저장소 계정을 클릭한 다음 새 저장소를 만들거나 기존 저장소 선택](./media/app-insights-export-telemetry/02-add.png)

데이터를 저장할 [Azure Storage 계정](../storage/storage-introduction.md)을 선택하거나 만듭니다.

![이벤트 유형 선택 클릭](./media/app-insights-export-telemetry/03-types.png)

내보내기를 만들면 진행을 시작합니다. (내보내기를 만든 후에는 도착하는 데이터만 받습니다.) 

데이터가 Blob에 표시되기까지 1시간 정도 지연될 수 있습니다.

나중에 이벤트 유형을 변경하려는 경우 내보내기를 편집하면 됩니다.

![이벤트 유형 선택 클릭](./media/app-insights-export-telemetry/05-edit.png)

스트림을 중지하려면 사용 안함을 클릭합니다. 사용을 다시 클릭하면 새 데이터로 스트림이 다시 시작합니다. 내보내기를 사용하지 않는 동안 포털에 도착한 데이터는 받지 못합니다.

스트림을 영구적으로 중지하려면 내보내기를 삭제합니다. 이렇게 해도 저장소에서 데이터를 삭제하지 않습니다.

#### <a name="cant-add-or-change-an-export"></a>내보내기를 추가 또는 변경할 수 있나요?
* 내보내기를 추가 또는 변경하려면 소유자, 참여자 또는 Application Insights 참여자 액세스 권한이 필요합니다. [역할에 대해 알아봅니다][roles].

## <a name="a-nameanalyzea-what-events-do-you-get"></a><a name="analyze"></a> 어떤 이벤트를 얻나요?
클라이언트 IP 주소에서 계산하는 위치 데이터를 추가한다는 점을 제외하고 내보낸 데이터는 응용 프로그램에서 수신하는 원시 원격 분석입니다. 

[샘플링](app-insights-sampling.md) 에서 무시된 데이터는 내보낸 데이터에 포함되지 않습니다.

계산된 다른 메트릭은 포함되지 않습니다. 예를들어 평균 CPU 사용률을 내보내지 않지만 평균이 계산된 곳에서 원시 원격 분석을 내보냅니다.

데이터에는 설정한 [가용성 웹 테스트](app-insights-monitor-web-app-availability.md) 의 결과도 포함됩니다. 

> [!NOTE]
> **샘플링**  응용 프로그램이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에서의 Application Insights SDK를 사용하는 경우 적응 샘플링 기능이 작동하고 원격 분석의 백분율만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](app-insights-sampling.md)
> 
> 

## <a name="a-namegeta-inspect-the-data"></a><a name="get"></a> 데이터 검사
포털에서 직접 저장소를 검사할 수 있습니다. **찾아보기**를 클릭하고 저장소 계정을 선택한 후 **컨테이너**를 엽니다.

Visual Studio에서 Azure Storage를 검사하려면 **보기**, **클라우드 탐색기**를 차례로 엽니다. (해당 메뉴 명령이 없는 경우 Azure SDK를 설치해야 합니다. **새 프로젝트** 대화 상자를 열고 Visual C#/클라우드를 확장한 다음 **.NET용 Microsoft Azure SDK 가져오기**를 선택합니다.)

blob 저장소를 열면 blob 파일 집합이 포함된 컨테이너가 보입니다. Application Insights 리소스 이름, 계측 키, 원격 분석 유형/날짜/시간에서 파생된 각 파일의 URI입니다. 리소스 이름은 모두 소문자이고 계측 키에서 대시를 생략합니다.

![적합한 도구를 사용하여 blob 저장소 검사](./media/app-insights-export-telemetry/04-data.png)

날짜 및 시간은 UTC이며 생성된 시간이 아니라 원격 분석이 저장소에 보관된 시기입니다. 따라서 데이터를 다운로드할 코드를 작성하는 경우 데이터를 선형으로 이동할 수 있습니다.

경로의 형식은 다음과 같습니다.

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Where 

* `blobCreationTimeUtc` 는 Blob이 내부 준비 저장소에 만들어진 시간입니다.
* `blobDeliveryTimeUtc` 는 Blob이 내보내기 대상 저장소에 복사되는 시간입니다.

## <a name="a-nameformata-data-format"></a><a name="format"></a> 데이터 형식
* 각 blob은 다중 '\n'-separated 행을 포함하는 텍스트 파일입니다. 여기에는 약 30초 동안 처리된 원격 분석 데이터가 있습니다.
* 각 행은 요청 또는 페이지 보기와 같은 원격 분석 데이터 요소를 나타냅니다.
* 각 행은 서식이 지정되지 않은 JSON 파일입니다. 관찰만 하려는 경우 Visual Studio에서 열고 편집, 고급, 형식 파일을 선택합니다.

![적합한 도구를 사용하여 원격 분석 보기](./media/app-insights-export-telemetry/06-json.png)

시간 기간은 틱 단위이며 10,000틱은 1ms입니다. 예를 들어 이러한 값은 브라우저에서 요청을 보내는 데 1ms, 받는 데 3ms, 브라우저에서 페이지를 처리하는 데 1.8s를 보여 줍니다.

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>데이터 처리
작은 규모에서 데이터를 분리하고, 스프레드시트에서 읽는 등의 처리를 위한 코드를 작성할 수 있습니다. 예:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

더 큰 코드 샘플에 대해서는 [작업자 역할 사용][exportasa]을 참조하세요.

## <a name="a-namedeleteadelete-your-old-data"></a><a name="delete"></a>이전 데이터 삭제
필요한 경우 스토리지 용량을 관리하고 오래된 데이터를 삭제해야 합니다. 

## <a name="if-you-regenerate-your-storage-key"></a>저장소 키를 다시 생성하는 경우...
저장소 키를 변경하는 경우 연속 내보내기 작업이 중지됩니다. Azure 계정에 알림이 표시됩니다. 

연속 내보내기 블레이드를 열고 내보내기를 편집합니다. 내보내기 대상을 편집하되 선택한 것과 동일한 저장소는 그대로 둡니다. 확인을 클릭하여 확인합니다.

![연속 내보내기를 편집하고 내보내기 대상을 열고 닫기](./media/app-insights-export-telemetry/07-resetstore.png)

연속 내보내기가 다시 시작됩니다.

## <a name="export-samples"></a>내보내기 샘플
* [작업자 역할을 사용하여 SQL로 내보내기][exportcode]
* [Stream Analytics를 사용하여 SQL로 내보내기][exportasa]
* [Stream Analytics 샘플 2](app-insights-export-stream-analytics.md)

더 큰 규모에서는 [HDInsight](https://azure.microsoft.com/services/hdinsight/) (클라우드의 Hadoop 클러스터)를 고려합니다. HDInsight는 빅 데이터에 대한 다양한 관리 분석 기술을 제공하므로 이를 사용하여 Application Insights에서 내보낸 데이터를 처리할 수 있습니다.

## <a name="q-a"></a>질문과 대답
* *하지만 원하는 모든 것은 차트의 일회성 다운로드입니다.*  
  
    예, 수행할 수 있습니다. 블레이드 맨 위에서 [데이터 내보내기](app-insights-metrics-explorer.md#export-to-excel)를 클릭합니다.
* *내보내기를 설정했지만 내 저장소에 데이터가 없습니다.*
  
    내보내기를 설정한 후 Application Insights가 앱에서 원격 분석을 받았나요? 새 데이터만 받게 됩니다.
* *내보내기를 설정하려 했지만 액세스가 거부되었습니다.*
  
    계정이 조직 소유인 경우 소유자 또는 참여자 그룹의 구성원이어야 합니다.
* *나만의 온-프레미스 저장소로 직접 내보낼 수 있나요?* 
  
    아니요. 죄송합니다. 우리의 내보내기 엔진은 현재 Azure 저장소에서만 작동합니다.  
* *내 저장소에 보관하는 데이터량에 제한이 있나요?* 
  
    아니요. 내보내기를 삭제할 때까지 푸싱한 데이터를 유지합니다. blob 저장소에 대한 외부 제한에 도달하는 경우 중지하지만 제한은 상당히 큽니다. 사용자가 이용하는 저장소 크기는 사용자가 제어하기 나름입니다.  
* *저장소에서 몇 개의 BLOB를 볼 수 있나요?*
  
  * 내보내려고 선택한 모든 데이터 형식에 대해 새 blob(데이터 파일이 사용 가능한 경우)이 매 분마다 만들어 집니다. 
  * 또한, 트래픽이 많은 응용 프로그램은 추가 파티션이 할당됩니다. 이 경우 모든 각 유닛이 매 분마다 blob을 생성합니다.
* *내 저장소 키를 다시 생성하거나 컨테이너의 이름을 변경한 경우에는 현재 내보내기가 동작하지 않습니다.*
  
    내보내기를 편집하고 내보내기 대상 블레이드를 엽니다. 이전과 마찬가지로 선택된 것과 동일한 저장소는 그대로 두고 확인을 클릭하여 확인합니다. 내보내기가 다시 시작됩니다. 지난 몇 일 이내에 변경된 경우 데이터가 손실됩니다.
* *내보내기를 일시 중지할 수 있나요?*
  
    예. 사용 안함을 클릭합니다.

## <a name="code-samples"></a>코드 샘플
* [작업자 역할을 사용하여 내보낸 JSON 구문 분석][exportcode]
* [Stream Analytics 샘플](app-insights-export-stream-analytics.md)
* [Stream Analytics를 사용하여 SQL로 내보내기][exportasa]
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](app-insights-export-data-model.md)

<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md





<!--HONumber=Nov16_HO3-->


