---
title: "Azure 웹앱 성능 테스트 | Microsoft Docs"
description: "Auzre 웹앱 성능을 테스트하여 앱이 사용자 부하를 어떻게 처리하는지 확인해 보세요. 응답 시간을 측정하고 문제가 될 수 있는 오류를 찾아보세요."
services: app-service\web
documentationcenter: 
author: ecfan
manager: douge
editor: jimbe
ms.assetid: e4858f40-038d-4039-a3d4-beee7960ac7d
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: estfan; manasma; ahomer
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 71f0a6e8d63e8f9ebb9307cce051010649a31a53


---
# <a name="performance-test-your-azure-web-app-under-load"></a>부하 상태에서 Azure 웹앱 성능 테스트
시작하거나 프로덕션 환경에 업데이트를 배포하기 전에 웹앱의 성능을 확인하십시오. 이를 통해 앱이 출시할 준비가 되었는지 더 잘 평가할 수 있습니다. 사용량이 가장 많을 때 앱이 트래픽을 처리할 수 있는지 파악하여 확신을 가지고 다음 마케팅에 활용할 수 있습니다.

공개 미리보기 중에 Azure 포털에서 무료로 앱의 성능 테스트를 할 수 있습니다.
이러한 테스트는 특정 기간 동안 사용자 부하를 시뮬레이션하여 앱의 응답을 측정합니다. 예를 들어 특정한 사용자 수에 대해 앱이 얼마나 빨리 응답하는지 테스트 결과를 볼 수 있습니다. 또한 실패한 요청의 수도 표시되는데, 이는 앱에 문제가 있다는 것을 나타냅니다.      

![웹앱의 성능 문제 찾기](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## <a name="before-you-start"></a>시작하기 전에
* [Azure 구독](https://account.windowsazure.com/subscriptions)이 아직 없다면, 이 테스트를 위해 필요합니다. [무료로 Azure 계정을 여는 방법](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)을 알아보세요.
* 성능 테스트 내역을 유지하려면 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) 계정이 필요합니다. 성능 테스트를 설정하면 적합한 계정이 자동으로 만들어집니다. 또는 새 계정을 만들거나, 계정 소유자인 경우 기존 계정을 사용할 수 있습니다. 
* 비-프로덕션 환경에서 테스트를 위해 응용 프로그램을 배포합니다. 
  프로덕션 환경이 아닌 용도로 앱에서 앱 서비스 계획을 사용하도록 합니다. 
  이렇게 하면 프로덕션 환경에서 기존 고객에 영향을 주거나 앱의 속도가 저하되지 않습니다. 

## <a name="set-up-and-run-your-performance-test"></a>성능 테스트 설정 및 실행
1. [Azure 포털](https://portal.azure.com)에 로그인합니다. 
   이미 Visual Studio Team Services 계정이 있다면 이 계정으로 로그인합니다.
2. 웹앱으로 이동합니다.
   
   ![모두 찾아보기, 웹앱, 내 웹앱으로 이동](./media/app-service-web-app-performance-test/azure-np-web-apps.png)
3. **성능 테스트**로 이동합니다.
   
   ![도구, 성능 테스트로 이동](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
4. 이제 성능 테스트 내역을 유지하려면 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) 계정을 연결합니다.
   
    사용할 Team Services 계정이 있다면 이 계정을 선택합니다. 계정이 없다면 새 계정을 만듭니다.
   
    ![기존 Team Services 계정을 선택하거나 새 계정 만들기](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)
5. 성능 테스트를 만듭니다. 세부 정보를 설정하고 테스트를 실행합니다. 

테스트를 실행하는 동안 실시간으로 결과를 볼 수 있습니다.

예를 들어 작년 휴일 세일에서 쿠폰을 지급한 앱이 있다고 가정해 보겠습니다. 이 이벤트는 15분 동안 지속되었으며 최고 100명의 동시 사용자 부하가 발생했습니다. 올해에는 고객의 수를 두 배로 늘리려고 합니다. 또한 페이지 로드 시간을 5초에서 2초로 단축하여 고객의 만족도를 높이려고 합니다. 따라서 15분 동안 250명의 사용자 부하로 업데이트된 앱의 성능을 테스트하려 합니다.

가상 사용자(고객)를 생성하여 웹 사이트에 동시에 방문하게 해서 앱에 부하 가하는 시뮬레이션을 해보겠습니다. 테스트를 통해 얼마나 많은 요청이 실패하며 응답이 느려지는지 확인할 수 있습니다.

  ![성능 테스트 만들기, 설정 및 실행](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

* 웹앱의 기본 URL은 자동으로 추가됩니다. 
  다른 페이지(HTTP GET 요청만 가능)를 테스트하려면 URL을 변경할 수 있습니다.
* 실제 조건과 유사하게 시뮬레이션하고 대기 시간을 줄이려면 사용자가 부하를 발생시키는 가장 근접한 지점을 선택하십시오.
  
  진행 중인 테스트는 다음과 같습니다. 첫 1분 동안, 페이지 로드가 원하는 결과보다 느리게 나타납니다.
  
  ![실시간 데이터와 함께 진행 중인 성능 테스트](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)
  
  테스트가 완료된 후, 페이지 로드가 첫 1분보다 훨씬 빠르다는 것을 알 수 있습니다. 이 결과로 문제를 어디서부터 해결해야 하는지 파악하는 데 도움이 됩니다.
  
  ![실패한 요청이 포함된 테스트 결과를 보여주는 완료된 성능 테스트](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## <a name="test-multiple-urls"></a>여러 개의 URL 테스트
Visual Studio 웹 테스트 파일을 업로드하여 종단 간 사용자 시나리오를 표시하는 여러 개의 URL을 통합하는 성능 테스트를 실행할 수도 있습니다. Visual Studio 웹 테스트 파일을 만들 수 있는 몇 가지 방법은 다음과 같습니다.

* [Fiddler를 사용하여 트래픽을 캡처하고 Visual Studio 웹 테스트 파일로 내보내기](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Visual Studio에서 부하 테스트 파일 만들기](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Visual Studio 웹 테스트 파일을 업로드하고 실행하려면

1. 위 단계에 따라 **새 성능 테스트** 블레이드를 엽니다.
   이 블레이드에서 다음을 사용하여 테스트 구성 옵션을 선택하여 **다음을 사용하여 테스트 구성** 블레이드를 엽니다.  
   
    ![부하 테스트 구성 블레이드 열기](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)
2. 테스트 형식이 **Visual Studio 웹 테스트** 로 설정되었는지 확인하고 HTTP 보관 파일을 선택합니다.
    "폴더" 아이콘을 사용하여 파일 선택기 대화 상자를 엽니다.
   
    ![여러 URL Visual Studio 웹 테스트 파일 업로드](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)
   
    파일이 업로드되면 URL 세부 정보 섹션에서 테스트할 URL 목록이 표시됩니다.
3. 사용자 부하 및 테스트 기간을 지정한 다음 **테스트 실행**을 선택합니다.
   
    ![사용자 부하 및 기간 선택](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)
   
    테스트가 완료되면 두 개의 창에 결과가 표시됩니다. 왼쪽 창에는 일련의 차트 형태로 성능 정보가 표시됩니다.
   
    ![성능 결과 창](./media/app-service-web-app-performance-test/multiple-01a-results.png)
   
    오른쪽 창에는 오류 유형 및 발생한 횟수를 포함하는 실패한 요청 목록이 표시됩니다.
   
    ![요청 오류 창](./media/app-service-web-app-performance-test/multiple-01b-results.png)
4. 오른쪽 창 위쪽에 있는 **다시 실행** 아이콘을 선택하여 테스트를 다시 실행합니다.
   
    ![테스트 다시 실행](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

## <a name="q--a"></a>질문과 대답
#### <a name="q-is-there-a-limit-on-how-long-i-can-run-a-test"></a>Q: 테스트 실행 시간에 제한이 있나요?
**A**: 예. Azure Portal에서 최대에서 최대 1시간 동안 테스트를 실행할 수 있습니다.

#### <a name="q-how-much-time-do-i-get-to-run-performance-tests"></a>Q: 성능 테스트는 얼마나 오래할 수 있습니까?
**A**: Preview 버전이 공개된 후 Visual Studio Team Services 계정으로 한 달에 20,000분에 해당하는 가상 사용자 시간(VUM)을 무료로 사용할 수 있습니다. VUM은 가상 사용자의 수를 테스트에 사용되는 시간(분)에 곱한 것입니다. 무료로 제공되는 양이 필요한 양보다 적다면 필요한 만큼만 구매하여 사용하면 됩니다.

#### <a name="q-where-can-i-check-how-many-vums-ive-used-so-far"></a>Q: 지금까지 사용한 VUM은 어떻게 확인할 수 있습니가?
**A**: Azure 포털에서 이 양을 확인할 수 있습니다.

![Team Services 계정으로 이동](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![사용한 VUM 확인](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### <a name="q-what-is-the-default-option-and-are-my-existing-tests-impacted"></a>Q: 기본 옵션은 무엇이며 기존 테스트에 영향을 주나요?
**A**: 성능 부하 테스트의 기본 옵션은 수동 테스트로, 여러 URL 테스트 옵션을 포털에 추가하기 전과 동일한 상태입니다.
기존 테스트는 구성된 URL을 계속 사용하고 이전과 같이 작동합니다.

#### <a name="q-what-features-not-supported-in-the-visual-studio-web-test-file"></a>Q: Visual Studio 웹 테스트 파일에서 지원되지 않는 기능은 무엇인가요?
**A**: 현재 이 기능은 웹 테스트 플러그 인, 데이터 원본 및 추출 규칙을 지원하지 않습니다. 웹 테스트 파일을 편집하여 이러한 항목을 제거해야 합니다. 향후 업데이트에서 이러한 기능에 대한 지원을 추가하려고 합니다.

#### <a name="q-does-it-support-any-other-web-test-file-formats"></a>Q: 지원되는 다른 웹 테스트 파일 형식이 있나요?
**A**: 현재 Visual Studio 웹 테스트 형식 파일만 지원됩니다.
다른 파일 형식에 대한 지원이 필요한지에 대한 귀하의 의견을 기다리고 있습니다.  [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com)을 알아보세요.

#### <a name="q-what-else-can-i-do-with-a-visual-studio-team-services-account"></a>Q: Visual Studio Team Services 계정을 사용하여 또 어떤 작업이 가능한가요?
**A**: 새 계정을 찾으려면 ```https://{accountname}.visualstudio.com```으로 이동합니다. 소프트웨어 코드를 공유하고, 빌드하고, 테스트하고, 작업을 추적하고, 발송하는 모든 작업이 모든 도구와 언어를 사용하여 클라우드 내에서 가능합니다. [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) 의 기능과 서비스가 팀이 더 쉽게 협력하며 지속적으로 배포하는 데 어떤 도움이 되는지 알아보십시오.

## <a name="see-also"></a>참고 항목
* [간단한 클라우드 성능 테스트 실행](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Apache Jmeter 성능 테스트 실행](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [클라우드 기반 부하 테스트 기록 및 재생](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [클라우드에서 앱 성능 테스트 실행](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)




<!--HONumber=Nov16_HO3-->


