<properties 
	pageTitle="MyDriving Azure IoT 예제 - 빠른 시작 | Microsoft Azure" 
	description="스트림 분석, 기계 학습, 이벤트 허브를 비롯하여 Microsoft Azure로 IoT 시스템을 설계하는 방법을 종합적으로 보여주는 앱을 시작합니다." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>

# MyDriving IoT 시스템: 빠른 시작

MyDriving는 장치에서 원격 분석을 수집하고 클라우드에서 데이터를 처리하며 기계 학습을 적용하여 적응 응답을 제공하는 일반적인 [사물 인터넷](iot-suite-overview.md)(IoT) 솔루션의 설계 및 구현을 보여 주는 시스템입니다. 데모에서는 사용자의 자동차 제어 시스템에서 정보를 수집하는 온보드 진단(OBD) 어댑터와 휴대폰에서 가져온 데이터를 사용하여 차량 주행에 대한 데이터를 로깅합니다. 이 데이터를 사용하여 사용자의 주행 스타일에 대한 피드백을 다른 사용자와 비교하여 제공합니다.


![](./media/iot-solution-get-started/image5.png)

MyDriving의 실제 목적은 사용자의 고유한 IoT 솔루션 만들기를 시작할 수 있도록 하는 것입니다. 하지만 그 전에 테스트 사용자 팀의 멤버로서 MyDriving 앱 자체를 사용해 보겠습니다. 이렇게 하면 아키텍처를 살펴보기 전에 소비자로서 내부 시스템 및 앱을 경험할 수 있습니다. 또한 테스트 사용자를 위한 앱의 알파 및 베타 배포판을 관리하는 뛰어난 방법인 HockeyApp도 소개합니다.

## 모바일 환경 사용

**필수 조건**:

Android, iOS 또는 Windows 10 장치

## Android 및 Windows Phone 10 설치

장치에서:

1.  **앱 개발을 허용합니다.**

    -   Android: **설정**, **보안**에서 **알 수 없는 원본**에서 앱을 허용합니다.

    -   Windows 10: **설정**, **업데이트**, **개발자용**에서 **개발자 모드**를 설정합니다.

2.  **베타 테스트 팀에 가입합니다.**

    HockeyApp을 통해 초기 릴리스의 앱을 테스트 사용자에게 손쉽게 배포할 수 있습니다.

    모바일 장치에서:

    -   <https://rink.hockeyapp.net>에서 HockeyApp에 **등록/로그인**합니다.
    
        (Windows 10을 사용하는 경우 Edge 브라우저를 사용합니다.)

        *빌드 2016 참석자* – Microsoft 단추 중 하나를 사용하여 컨퍼런스에 등록한 동일한 MSA 전자 메일로 로그인합니다. HockeyApp에 이미 등록되어 있습니다.

        ![](./media/iot-solution-get-started/image1.png)

3.  여기에서 앱을 **다운로드하여 설치**합니다.

    -   Android: <http://rink.io/spMyDrivingAndroid>

    -   Windows 10 Phone: <http://rink.io/spMyDrivingUWP>

        두 가지 항목이 있습니다. 신뢰할 수 있는 사용자에 인증서를 설치합니다. 그런 다음 앱을 설치합니다.
    
*Windows 10 Phone를 시작하는 데 문제가 있나요?* 휴대폰이 업데이트되었거나 너무 오래되었을 수 있습니다. 최신 업데이트가 있는지 확인하거나 다음을 설치합니다.

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx) 
   

## iOS 설치

### 빌드 2016 참석자

빌드 2016 참석자라면 HockeyApp에서 테스트 팀의 멤버로 앱을 다운로드합니다.

1.  IOS 장치에서 <https://rink.hockeyapp.net>에 로그인합니다. Microsoft 로그인 단추 중 하나를 사용하여 컨퍼런스에 등록한 동일한 MSA 전자 메일로 로그인합니다. (전자 메일 및 암호 필드를 사용하지 마십시오.)

    ![](./media/iot-solution-get-started/image1.png)

2.  HockeyApp 대시보드에서 MyDriving을 선택하여 다운로드합니다.

3.  HockeyApp의 베타 릴리스에 권한을 부여합니다.

    **설정** &gt;**일반** &gt;**프로필 및 장치 관리**로 이동합니다.

    **Bit Stadium GmbH** 인증서를 신뢰합니다.

### 빌드 2016 참석자가 아닌 경우

곧 iOS Store에 앱을 출시할 예정입니다.

지금은 앱 자체를 빌드 및 배포할 수 있습니다.

-   [GitHub에서] 코드를 다운로드합니다.

-   [Xamarin을 사용하여] 빌드 및 배포합니다.

자세한 내용은 [MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)에 나와 있습니다.

## OBD 어댑터 가져오기(선택 사항)

실제 사물 인터넷 시스템으로 만드는 부분입니다. 어댑터 없이도 앱을 사용할 수 있지만 실제 사물에서 보다 흥미로울 수 있으며 비용이 별로 들지 않습니다.

온보드 진단(OBD)은 튜닝을 위해 정비소에서 사용하고 이상한 소음 및 경로 램프를 진단하는 자동차의 기능입니다. 자동차가 아주 오래되지 않았다면 대시보드 아래 소켓이 있습니다. 적절한 커넥터로 엔진의 성능 메트릭을 얻을 수 있으며 확실히 조정할 수 있습니다. OBD 커넥터는 일상적인 장소에서 저렴하게 구입할 수 있습니다. Bluetooth 또는 WiFi를 사용하여 휴대폰에 앱을 연결합니다.

하지만 이 경우 자동차를 클라우드에 연결하려고 합니다. 물론 OBD에서 휴대폰으로는 직접 연결되지만 앱은 릴레이로 작동합니다. 자동차의 원격 분석은 MyDriving IoT Hub로 바로 전송되고 여기에서 데이터가 처리되어 주행 기록을 기록하고 주행 스타일을 평가합니다.

### OBD 장치 연결


1.  자동차에 OBD 커넥터가 있는지 확인하세요(구형 자동차가 아니라면 있음). 차량 실내 어딘가에 있습니다(일반적으로 대시보드 아래, 플랩 뒤에 있음).

2.  OBD 어댑터를 가져옵니다. 다음 유형을 사용했습니다.

    다음을 사용하는 경우:

    -   **Android 또는 Windows** 휴대폰. **Bluetooth 가능 OBD II** 어댑터가 필요합니다. [BAFX Products 34t5 Bluetooth OBDII Scan Tool]을 사용했습니다.

    -   **iOS** 휴대폰. **WiFi 가능** OBD 어댑터가 필요합니다. [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]를 사용했습니다.

3.  OBD 어댑터와 함께 제공되는 지침에 따라 휴대폰에 연결합니다.

    -   Bluetooth 어댑터는 설정 페이지에서 휴대폰과 페어링되어야 합니다.

    -   WiFi 어댑터에는 192.168.xxx.xxx 범위의 주소를 포함해야 합니다.

4.  자동차가 여러 대 있는 경우 각각 별도의 어댑터를 가져올 수 있습니다(최대 3개).

OBD 어댑터가 없는 경우 앱은 휴대폰의 GPS 수신기에서 백 엔드로 위치 및 속도 데이터를 계속 보내고 OBD를 시뮬레이션할지 묻는 메시지를 표시합니다.

[MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)의 섹션 2.1 "IoT 장치"에서 사용자 고유의 OBD 장치를 만드는 옵션 및 앱이 OBD 어댑터에서 가져온 데이터를 사용하는 방법에 대해 자세히 알아볼 수 있습니다.

## 앱 사용

앱을 **시작**합니다. 진행 방법을 안내하는 초기 빠른 시작이 있습니다.

-   **사용자의 주행을 추적합니다.** 기록 단추(화면 맨 아래 큰 빨간색 원)를 탭하여 주행을 시작하고 한 번 더 탭하여 종료합니다.


    ![](./media/iot-solution-get-started/image2.png)

-   주행이 시작될 때마다 OBD 장치가 없는 경우 시뮬레이터를 사용할지 묻는 메시지가 표시됩니다.

-   주행이 끝나면 중지 단추를 클릭하고 요약이 표시됩니다.

    ![](./media/iot-solution-get-started/image3.png)

-   **주행 검토:**

    ![](./media/iot-solution-get-started/image4.png)

-   **프로필 검토:**

    ![](./media/iot-solution-get-started/image5.png)

-   앱에 있는 단추를 사용하여 **테스트 피드백을 보내거나** 휴대폰을 흔들어주세요. 그러면 스크린샷에 자동으로 연결되어 말하는 내용을 알 수 있습니다. 안타깝게 충돌이 있는 경우 HockeyApp에서 충돌 로그를 수집하여 알려 줍니다.

## 사용자 의견 

사용자가 자신의 IoT 시스템을 시작할 수 있도록 MyDriving을 작성했으므로 실제로 얼마나 잘 작동하는지 의견을 듣고 싶습니다. 어려운 점이나 극복할 과제가 있는 경우, 시나리오에 보다 적합하게 할 수 있는 확장 지점이 있는 경우, 특정 요구를 수행하는 데 더 효율적인 방법이 있는 경우, MyDriving 또는 이 문서에 대한 기타 개선 사항이 있는 경우 알려 주세요.

MyDriving 앱 자체 내에서 기본 제공되는 HockeyApp 피드백 메커니즘을 사용할 수 있습니다. iOS 및 Android에서는 휴대폰을 흔들거나 피드백 메뉴 명령을 사용합니다. [HockeyApp 포털]을 통해서도 피드백을 제공할 수 있습니다.

또한 [GitHub에 문제점]을 제출하거나 아래에 의견을 남길 수도 있습니다(ko-KR 버전).

여러분의 소중한 의견을 기다리고 있습니다.

## 다음 단계

-   [MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)를 살펴보고 전체 MyDriving 시스템을 어떻게 설계 및 빌드했는지 이해합니다.

-   Azure Resource Manager 스크립트를 사용하여 [사용자 고유의 시스템을 만들고 배포](iot-solution-build-system.md)합니다. [MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)는 가장 많은 사용자 지정 항목을 만들 영역도 안내합니다.

  [GitHub에서]: https://github.com/Azure-Samples/MyDriving
  [Xamarin을 사용하여]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [HockeyApp 포털]: https://rink.hockeyapp.org
  [GitHub에 문제점]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0406_2016-->