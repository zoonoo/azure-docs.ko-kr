<properties 
	pageTitle="모바일 분석 시작 | 모바일 개발자 센터" 
	description="모바일 분석을 시작하는 방법에 대해 알아봅니다." 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="mahender"/>

# 모바일 분석 시작(Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

이 자습서에서는 [Capptain]을 사용하여 앱에 모바일 분석 기능을 추가합니다. 모바일 분석을 통해 사용자가 앱과 상호 작용하는 방식 및 가장 많은 활동이 수행되는 섹션을 확인할 수 있습니다. 이러한 데이터 가져오기를 시작하려면 Capptain SDK를 사용하여 앱을 계측합니다.


>[AZURE.NOTE] Microsoft 소유의 Capptain.com에서는 Azure 모바일 서비스 표준 계층 고객을 대상으로 매월 최대 10만 명의 실제 사용자에게 모바일 앱용 분석 기능을 무료로 제공합니다. 이 서비스를 사용하려면 mobileservices@microsoft.com에 추가 지침을 문의하세요. 이 자습서의 뒷부분에서는 Capptain.com의 기능에 대해 대략적으로 설명하고 기능 사용 방법 관련 지침을 제공합니다.


이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Capptain SDK 초기화]
2. [UIViewController 오버로드]

이 자습서를 사용하려면 다음이 필요합니다.

* [Capptain] 계정
* [모바일 서비스 표준 계층] 앱

## <a name="initialize"></a>Capptain SDK 초기화

1. Capptain에 등록된 앱의 **응용 프로그램 정보** 페이지로 이동하여 SDK 탭을 선택하고 패키지를 다운로드합니다.

2. XCode에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 "파일 추가..."를 선택하여 Capptain SDK를 프로젝트에 추가합니다. CapptainSDK 폴더를 선택합니다.

3. 프로젝트를 선택하고 **빌드 단계** 탭에서 **이진 파일과 라이브러리 연결**을 선택한 후 다음 프레임워크를 추가합니다.
    * AdSupport.framework - set the link as Optional
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE] AdSupport 프레임워크는 제거할 수 있습니다. Capptain에서 IDFA를 수집하려면 이 프레임워크가 필요합니다. 그러나 이 ID와 관련한 Apple 정책을 준수하기 위해 IDFA 수집을 사용하지 않도록 설정할 수 있습니다.

4. 응용 프로그램 대리자 구현 파일에서 Capptain 에이전트를 가져옵니다.


        #import "CapptainAgent.h"


5. `applicationDidFinishLaunching:` 또는 `application:didFinishLaunchingWithOptions:` 내에서 앱 ID와 SDK 키를 사용해 `registerapp:identifiedby:`를 전달하여 Capptain 에이전트를 초기화합니다.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>UIViewController 오버로드

1. 프로젝트에서  `UIViewController`의 각 자식을 찾은 다음 각 항목이  `CapptainViewController`에서 상속하는지 확인합니다.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. 프로젝트에서  `UITableViewController`의 각 자식을 찾은 다음 각 항목이  `CapptainTableViewController`에서 상속하는지 확인합니다.

    이제 앱이 Capptain으로 분석 데이터를 보내도록 구성되었습니다.

## 다음 단계
[http://www.capptain.com](http://www.capptain.com) (영문)에서 Capptain을 통해 앱에 수행할 수 있는 작업에 대해 자세히 알아보세요.

<!-- Anchors. -->
[Capptain SDK 초기화]: #initialize
[UIViewController 오버로드]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[모바일 서비스 표준 계층]: /ko-kr/pricing/details/mobile-services/


<!--HONumber=42-->
