---
title: Windows 유니버설에서 Engagement API를 사용하는 방법
description: Windows 유니버설에서 Engagement API를 사용하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4f8f211764646bc53319f435d74a16a026329039
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>Windows 유니버설에서 Engagement API를 사용하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서는 [Windows 유니버설에서 Engagement를 통합하는 방법](mobile-engagement-windows-store-integrate-engagement.md)문서를 보완하는 추가 문서로, Engagement API를 사용하여 응용 프로그램 통계를 보고하는 방법을 자세히 설명합니다.

Engagement에서 응용 프로그램 세션, 활동, 충돌 및 기술 정보만 보고하도록 하려는 경우, 가장 간단한 방법은 모든 `Page` 서브클래스가 `EngagementPage` 클래스에서 상속되도록 지정하는 것입니다.

응용 프로그램 관련 이벤트, 오류, 작업을 보고하는 등 추가 작업을 수행하려는 경우 또는 `EngagementPage` 클래스에서 구현되는 것과는 다른 방식으로 응용 프로그램 활동을 보고해야 하는 경우에는 Engagement API를 사용해야 합니다.

Engagement API는 `EngagementAgent` 클래스를 통해 제공됩니다. 해당 메서드는 `EngagementAgent.Instance`을(를) 통해 액세스할 수 있습니다.

에이전트 모듈이 초기화되지 않은 경우에도 각 API 호출은 연기되며 에이전트를 사용할 수 있게 되면 다시 실행됩니다.

## <a name="engagement-concepts"></a>Engagement 개념
다음 요소는 Windows 유니버설 플랫폼과 관련된 일반적인 [Mobile Engagement 개념](mobile-engagement-concepts.md) 을 구체화합니다.

### <a name="session-and-activity"></a>`Session` 및 `Activity`
*작업*은 일반적으로 단일 응용 프로그램 페이지와 연결됩니다. 즉 *작업*은 페이지를 표시하면 시작되며 페이지를 닫으면 중지됩니다. `EngagementPage` 클래스를 사용하여 Engagement SDK를 통합하는 경우 이러한 방식이 사용됩니다.

하지만 Engagement API를 사용하여 *활동* 을 수동으로 제어할 수도 있습니다. 이렇게 하면 지정된 페이지를 여러 하위 부분으로 분할하여 해당 페이지의 사용에 대해 더 많은 세부 정보(예: 이 페이지 내에서 대화 상자를 사용하는 빈도와 기간)를 확인할 수 있습니다.

## <a name="reporting-activities"></a>활동 보고
### <a name="user-starts-a-new-activity"></a>사용자가 새 활동을 시작함
#### <a name="reference"></a>참고 자료
            void StartActivity(string name, Dictionary<object, object> extras = null)

사용자 활동이 변경될 때마다 `StartActivity()` 을(를) 호출해야 합니다. 이 함수를 처음 호출하면 새 사용자 세션이 시작됩니다.

> [!IMPORTANT]
> 응용 프로그램을 닫을 때 SDK는 EndActivity 메서드를 자동으로 호출합니다. 따라서 사용자 활동이 변경될 때마다 StartActivity 메서드를 호출하는 것이 좋으며 EndActivity 메서드는 호출하지 않는 것이 좋습니다. EndActivity 메서드를 호출하면 현재 세션이 강제로 종료되기 때문입니다.
> 
> 

#### <a name="example"></a>예
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>사용자가 현재 활동을 종료함
#### <a name="reference"></a>참고 자료
            void EndActivity()

활동과 세션이 종료됩니다. 반드시 필요한 경우가 아니면 이 메서드를 호출해서는 안 됩니다.

#### <a name="example"></a>예
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>작업 보고
### <a name="start-a-job"></a>작업 시작
#### <a name="reference"></a>참고 자료
            void StartJob(string name, Dictionary<object, object> extras = null)

작업을 사용하여 일정 기간 동안의 특정 태스크를 추적할 수 있습니다.

#### <a name="example"></a>예
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>작업 종료
#### <a name="reference"></a>참고 자료
            void EndJob(string name)

작업에 의해 추적되는 태스크가 종료되는 즉시 해당 작업에 대해 작업 이름을 제공하여 EndJob 메서드를 호출해야 합니다.

#### <a name="example"></a>예
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>이벤트 보고
이벤트에는 다음의 세 가지 유형이 있습니다.

* 독립 실행형 이벤트
* 세션 이벤트
* 작업 이벤트

### <a name="standalone-events"></a>독립 실행형 이벤트
#### <a name="reference"></a>참고 자료
            void SendEvent(string name, Dictionary<object, object> extras = null)

독립 실행형 이벤트는 세션의 컨텍스트 외부에서 발생할 수 있습니다.

#### <a name="example"></a>예
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>세션 이벤트
#### <a name="reference"></a>참고 자료
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

세션 이벤트는 일반적으로 사용자가 세션 중에 수행하는 동작을 보고하는 데 사용됩니다.

#### <a name="example"></a>예
**데이터 제외:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**데이터 포함:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>작업 이벤트
#### <a name="reference"></a>참고 자료
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

작업 이벤트는 일반적으로 사용자가 작업 중에 수행하는 동작을 보고하는 데 사용됩니다.

#### <a name="example"></a>예
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>오류 보고
세 가지 유형의 오류가 있습니다.

* 독립 실행형 오류
* 세션 오류
* 작업 오류

### <a name="standalone-errors"></a>독립 실행형 오류
#### <a name="reference"></a>참고 자료
            void SendError(string name, Dictionary<object, object> extras = null)

세션 오류와 달리 독립 실행형 오류는 세션의 컨텍스트 외부에서 발생할 수 있습니다.

#### <a name="example"></a>예
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>세션 오류
#### <a name="reference"></a>참고 자료
            void SendSessionError(string name, Dictionary<object, object> extras = null)

세션 오류는 일반적으로 세션 중에 사용자에게 영향을 주는 오류를 보고하는 데 사용됩니다.

#### <a name="example"></a>예
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>작업 오류
#### <a name="reference"></a>참고 자료
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

오류는 현재 사용자 세션이 아닌 실행 중인 작업에 관련될 수 있습니다.

#### <a name="example"></a>예
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>작동 중단 보고
에이전트는 작동 중단을 처리하는 두 가지 방법을 제공합니다.

### <a name="send-an-exception"></a>예외 보내기
#### <a name="reference"></a>참고 자료
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>예
언제든지 다음을 호출하여 예외를 보낼 수 있습니다.

            EngagementAgent.Instance.SendCrash(aCatchedException);

또한 선택적 매개 변수를 사용하여 작동 중단을 보냄과 동시에 Engagement 세션을 종료할 수도 있습니다. 이렇게 하려면 다음을 호출합니다.

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

이 경우 작동 중단을 보낸 직후에 세션과 작업이 닫힙니다.

### <a name="send-an-unhandled-exception"></a>처리되지 않은 예외 보내기
#### <a name="reference"></a>참고 자료
            void SendCrash(Exception e)

Engagement의 자동 **충돌** 보고를 **사용 안 함**으로 설정한 경우에는 처리되지 않은 예외를 보내는 방법도 제공됩니다. 이 방법은 응용 프로그램 UnhandledException 이벤트 처리기 내에서 사용하는 경우 특히 유용합니다.

이 방법을 사용하는 경우 **항상** Engagement 세션과 작업이 호출된 후에 종료됩니다.

#### <a name="example"></a>예
이 방법을 사용하면 고유한 UnhandledExceptionEventArgs 처리기를 구현할 수 있습니다. 예를 들어 다음과 같이 `App.xaml.cs` 파일의 `Current_UnhandledException` 메서드를 추가합니다.

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

App.xaml.cs의 "Public App(){}"에 다음 코드를 추가합니다.

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>장치 ID
            String EngagementAgent.Instance.GetDeviceId()

이 메서드를 호출하여 Engagement 장치 ID를 가져올 수 있습니다.

## <a name="extras-parameters"></a>extras 매개 변수
이벤트, 오류, 활동 또는 작업에 임의 데이터를 연결할 수 있습니다. 사전을 사용하여 이러한 데이터를 구조화할 수 있습니다. 모든 형식의 키와 값을 사용할 수 있습니다.

extras 데이터는 serialize되므로 원하는 형식을 extras에 삽입하려면 해당 형식용 데이터 계약을 추가해야 합니다.

### <a name="example"></a>예
아래 예제에서는 "Person"이라는 새 클래스를 만듭니다.

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

그런 다음 `Person` 인스턴스를 extras에 추가합니다.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> 다른 형식의 개체를 추가할 때는 사용자가 읽을 수 있는 문자열을 반환하도록 해당 ToString() 메서드를 구현해야 합니다.
> 
> 

### <a name="limits"></a>제한
#### <a name="keys"></a>구성
개체의 각 키는 다음 정규식과 일치해야 합니다.

`^[a-zA-Z][a-zA-Z_0-9]*$`

즉, 키는 하나 이상의 문자로 시작해야 하며 그 뒤에 문자, 숫자 또는 밑줄(\_)이 붙어야 합니다.

#### <a name="size"></a>크기
extras는 호출당 **1024** 자로 제한됩니다.

## <a name="reporting-application-information"></a>응용 프로그램 정보 보고
### <a name="reference"></a>참고 자료
            void SendAppInfo(Dictionary<object, object> appInfos)

SendAppInfo() 함수를 사용하면 추적 정보 또는 기타 응용 프로그램 관련 정보를 수동으로 보고할 수 있습니다.

이 데이터를 증분 방식으로 보낼 수 있습니다. 그러면 특정 장치에 대해 지정한 키의 최신 값만 보관됩니다. 이벤트 추가 항목과 마찬가지로 Dictionary\<object, object\>를 사용하여 데이터를 연결합니다.

### <a name="example"></a>예
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>제한
#### <a name="keys"></a>구성
개체의 각 키는 다음 정규식과 일치해야 합니다.

`^[a-zA-Z][a-zA-Z_0-9]*$`

즉, 키는 하나 이상의 문자로 시작해야 하며 그 뒤에 문자, 숫자 또는 밑줄(\_)이 붙어야 합니다.

#### <a name="size"></a>크기
응용 프로그램 정보는 호출당 **1024** 자로 제한됩니다.

위의 예제에서 서버로 전송된 JSON의 길이는 44자입니다.

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>로깅
### <a name="enable-logging"></a>로깅 사용
SDK는 IDE 콘솔에서 테스트 로그를 생성하도록 구성될 수 있습니다.
이러한 로그는 기본적으로 활성화되지 않습니다. 이를 사용자 지정하려면 속성 `EngagementAgent.Instance.TestLogEnabled`를 `EngagementTestLogLevel` 열거형에서 사용 가능한 값 중 하나로 업데이트합니다. 예를 들어 다음과 같습니다.

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

