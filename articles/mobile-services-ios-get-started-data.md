<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# 모바일 서비스에서 데이터 시작

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

<div class="dev-callout"><b>참고</b>
<p>이 자습서는 iOS 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 <a href="/ko-kr/develop/mobile/tutorials/get-started-ios">모바일 서비스 시작</a> 자습서를 완료하는 것이 좋습니다.</p>
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [iOS 앱 프로젝트 다운로드][iOS 앱 프로젝트 다운로드]
2.  [모바일 서비스 만들기][모바일 서비스 만들기]
3.  [저장소로 사용할 데이터 테이블 추가][저장소로 사용할 데이터 테이블 추가]
4.  [모바일 서비스를 사용하도록 앱 업데이트][모바일 서비스를 사용하도록 앱 업데이트]
5.  [모바일 서비스에 대해 앱 테스트][모바일 서비스에 대해 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

-   [모바일 서비스 iOS SDK][모바일 서비스 iOS SDK]와 [XCode 4.5][XCode 4.5] 및 iOS 5.0 이상 버전
-   Microsoft Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.
    </p>
    </div>

## <a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 iOS 앱인 [GetStartedWithData 앱][GetStartedWithData 앱]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 iOS 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1.  GetStartedWithData [샘플 앱][GetStartedWithData 앱]을 다운로드합니다.

2.  다운로드한 프로젝트를 Xcode에서 열고 TodoService.m 파일을 검사합니다.

    이 앱이 모바일 서비스에서 작동하기 위해 수행해야 하는 단계를 지정하는 8개의 **// TODO** 주석이 있습니다.

3.  **Run** 단추(또는 Command+R 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 텍스트 상자에 일부 텍스트를 입력한 후 **+** 단추를 클릭합니다.

    ![][]

    저장된 텍스트가 아래 목록에 표시됩니다.

## <a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a>모바일 서비스에 새 테이블 추가

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][mobile-services-create-new-service-data-2]]

## <a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access][mobile-services-ios-enable-mobile-service-access]]

## <a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트

1.  Xcode에서 배포 대상 에뮬레이터(iPhone 또는 iPad)를 선택하고 **Run** 단추(또는 Command+R 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

    그러면 모바일 서비스에서 항목을 쿼리하며 iOS SDK로 빌드된 Azure 모바일 서비스 클라이언트가 실행됩니다.

2.  앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **+** 단추를 클릭합니다.

    그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3.  [관리 포털][관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4.  **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

    ![][1]

    이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

이제 iOS용 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 iOS 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다.

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정][스크립트를 사용하여 데이터 유효성 검사 및 수정]
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다른 iOS 자습서 중 하나를 시도해 보십시오.

-   [인증 시작][인증 시작]
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    모바일 서비스를 사용하여 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started-data.md
  [모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-ios
  [iOS 앱 프로젝트 다운로드]: #download-app
  [모바일 서비스 만들기]: #create-service
  [저장소로 사용할 데이터 테이블 추가]: #add-table
  [모바일 서비스를 사용하도록 앱 업데이트]: #update-app
  [모바일 서비스에 대해 앱 테스트]: #test-app
  [모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData 앱]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  []: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [관리 포털]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-ios
  [인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-ios
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-ios
