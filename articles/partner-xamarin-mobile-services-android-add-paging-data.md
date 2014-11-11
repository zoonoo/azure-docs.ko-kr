<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# 페이징을 사용하여 모바일 서비스 쿼리 구체화

<div class="dev-center-tutorial-selector sublanding">

[Windows 스토어 C#][Windows 스토어 C#][Windows 스토어 JavaScript][Windows 스토어 JavaScript][Windows Phone][Windows Phone][iOS][iOS][Android][Android][HTML][HTML][iOS C#][iOS C#][Android C#][Android C#]

</div>

이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 Xamarin.Android 앱으로 반환되는 데이터 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트에서 **Take** 및 **Skip** 쿼리 메서드를 사용하여 데이터의 특정 "페이지"를 요청합니다.

<div class="dev-callout">

**참고**
모바일 장치 클라이언트에서 데이터 오버플로를 방지하기 위해 모바일 서비스는 기본적으로 응답의 최대 항목 수를 50개로 설정하는 자동 페이지 제한을 구현합니다. 페이지 크기를 지정하여 응답에서 최대 1,000개 항목을 명시적으로 요청할 수 있습니다.

</div>

이 자습서는 이전 자습서인 [데이터 시작][데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 적어도 데이터 계열 작업에 대한 첫 번째 자습서인 [데이터 시작][데이터 시작] 자습서를 완료해야 합니다.

1.  Xamarin Studio에서 [데이터 시작][데이터 시작] 자습서를 완료할 때 만든 프로젝트를 엽니다.

2.  **Run(실행)**을 클릭하여 앱을 시작한 후 입력란에 텍스트를 입력하고 **Add(추가)** 단추를 클릭합니다.

3.  TodoItem 테이블에 저장된 항목이 네 개 이상이므로 이전 단계를 세 번 이상 반복합니다.

4.  **TodoActivity.cs** 파일에서 **RefreshItemsFromTableAsync** 메서드의 LINQ 쿼리를 다음 쿼리로 바꿉니다.

        var list = await todoTable.Where(item => item.Complete == false)
                                  .Take(3)
                                  .ToListAsync();

    이 쿼리는 완료됨으로 표시되지 않은 맨 위 항목 세 개를 반환합니다.

5.  앱을 다시 빌드하고 시작합니다.

    TodoItem 테이블에서 처음 세 개의 결과만 표시됩니다.

6.  (옵션) 브라우저 개발자 도구 또는 [Fiddler]와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 확인합니다.

    `Take(3)` 메서드는 쿼리 URI에서 쿼리 옵션 `$top=3`으로 변환되었습니다.

7.  **RefreshItemsFromTableAsync** 메서드의 LINQ 쿼리를 한 번 더 다음 쿼리로 업데이트합니다.

            var list = await todoTable.Where(item => item.Complete == false)
                                      .Skip(3)
                                      .Take(3)
                                      .ToListAsync();

    이 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

    <div class="dev-callout">

    **참고**
    이 자습서에서는 하드 코드된 페이징 값을 **Take** 및 **Skip** 메서드에 전달하여 간소화된 시나리오를 사용합니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다. **IncludeTotalCount** 메서드를 호출하여 서버에서 사용 가능한 총 항목 수를 페이징 데이터와 함께 가져올 수도 있습니다.

    </div>

8.  (옵션) 모바일 서비스에 전송된 요청의 URI를 다시 봅니다.

    `Skip(3)` 메서드는 쿼리 URI에서 쿼리 옵션 `$skip=3`으로 변환되었습니다.

## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스의 데이터 작업 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [인증 시작하기][인증 시작하기]
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.



  [Windows 스토어 C#]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-js "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [iOS C#]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Android C#]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [인증 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-android
