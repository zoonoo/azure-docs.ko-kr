<properties urlDisplayName="Monitor with AppDynamics" pageTitle="Azure에서 AppDynamics를 사용하는 방법" metaKeywords="" description="Azure용 AppDynamics 사용 방법에 대해 알아봅니다." metaCanonical="" services="cloud-services" documentationCenter="" title="Azure용 AppDynamics 사용 방법" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Azure용 AppDynamics 사용 방법

이 항목에서는 Azure용 AppDynamics를 시작하는 방법에 대해 설명합니다.

## 목차

-   [AppDynamics 정의][AppDynamics 정의]
-   [필수 조건][필수 조건]
-   [AppDynamics 계정 등록][AppDynamics 계정 등록]
-   [AppDynamics에서 .NET 에이전트 다운로드][AppDynamics에서 .NET 에이전트 다운로드]
-   [Azure 역할에 .NET 에이전트 추가 및 시작 수정][Azure 역할에 .NET 에이전트 추가 및 시작 수정]
-   [Azure에 AppDynamics 계측 응용 프로그램 게시][Azure에 AppDynamics 계측 응용 프로그램 게시]
-   [응용 프로그램 모니터링][응용 프로그램 모니터링]

## <span id="what"></span></a>AppDynamics 정의

AppDynamics는 다음을 지원하는 응용 프로그램 성능 모니터링 솔루션입니다.

-   프로덕션 환경에서 느리거나 중단된 사용자 요청 및 오류와 같은 문제 확인

-   이러한 문제의 근본 원인 격리 및 문제 해결

AppDynamics에는 다음과 같은 두 가지 구성 요소가 있습니다.

-   응용 프로그램 서버 에이전트: 응용 프로그램 서버 .NET 에이전트는 서버에서 데이터를 수집합니다. 모니터링할 모든 역할 인스턴스에서 별도의 에이전트를 실행합니다. AppDynamics 포털에서 해당 에이전트를 다운로드할 수 있습니다.

-   AppDynamics 컨트롤러: 에이전트가 Azure의 AppDynamics 컨트롤러 호스팅 서비스로 해당 정보를 보냅니다. 웹 브라우저 기반 콘솔에서 컨트롤러에 로그인하여 응용 프로그램을 모니터링하고 분석하고 문제를 해결할 수 있습니다.

    ![AppDynamics 다이어그램][AppDynamics 다이어그램]

## <span id="prereq"></span></a>필수 조건

-   Visual Studio 2010 이상
-   모니터링할 대상 Visual Studio 솔루션
-   Azure SDK
-   Azure 계정

## <span id="register"></span></a>AppDynamics 계정 등록

Azure 계정용 AppDynamics에 등록하려면

1.  Azure 마켓플레이스(<https://datamarket.azure.com/browse/Applications>)에서 AppDynamics에 대해 **평가판 사용** 또는 **등록**을 클릭합니다.

    **등록**을 선택한 경우 전체 기능이 있는 무료 버전의 Azure용 AppDynamics Pro를 받으며, 30일 후에는 기능이 제한된 무료 버전의 Azure용 AppDynamics Lite로 다운그레이드됩니다. 이 옵션에서는 신용 카드를 제공하지 않아도 됩니다. Azure용 AppDynamics Pro로 언제든 업그레이드할 수 있습니다.

    **평가판 사용**을 선택한 경우 전체 기능이 있는 무료 버전의 Azure용 AppDynamics Pro를 받습니다. 이 옵션에서는 신용 카드를 제공해야 합니다. 30일 후에 구독을 취소하지 않으면 Azure용 AppDynamics Pro를 계속 사용할 수 있도록 신용 카드 계정으로 비용이 청구됩니다.

    모니터링할 역할 인스턴스마다 하나의 에이전트 라이선스가 필요합니다. 예를 들어 2개의 웹 역할 인스턴스와 2개의 작업자 역할 인스턴스를 실행하는 사이트에는 4개의 에이전트 라이선스가 필요합니다.

2.  등록 페이지에서 사용자 정보, 암호, 전자 메일 주소, 회사 이름 및 Azure에서 응용 프로그램을 게시할 때 모니터링할 해당 응용 프로그램의 이름을 지정합니다.

3.  **지금 등록**을 클릭합니다.

    등록 페이지에서 입력한 주소로 전자 메일을 수신하면 AppDynamics 자격 증명 및 계정에 할당된 AppDynamics 컨트롤러 URL(호스트 및 포트)을 받습니다. 이 정보를 저장합니다.

    다른 제품의 AppDynamics 자격 증명이 이미 있는 경우 해당 자격 증명을 사용하여 로그인할 수 있습니다.

    또한 AppDynamics 계정 홈페이지도 받습니다.

    AppDynamics 계정 홈페이지로 이동합니다.

    AppDynamics 계정 홈페이지에는 다음이 포함되어 있습니다.

    -   컨트롤러 URL: 이 URL에서 AppDynamics 컨트롤러 호스팅 서비스의 계정으로 로그인

    -   AppDynamics 자격 증명: 계정 이름 및 액세스 키

    -   AppDynamics 다운로드 사이트에 대한 링크: 이 링크에서 AppDynamics .NET 에이전트 다운로드

    -   사용 중인 Pro 평가판의 남은 일수

    -   AppDynamics 등록 동영상 및 설명서에 대한 링크

    웹 브라우저에서 해당 URL을 입력하고 AppDynamics 자격 증명으로 로그인하여 AppDynamics 계정 홈페이지에 언제든 액세스할 수 있습니다.

## <span id="download"></span></a>AppDynamics에서 .NET 에이전트 다운로드

1.  AppDynamics 다운로드 사이트로 이동합니다. URL은 시작 전자 메일과 AppDynamics 계정 홈페이지에 나와 있습니다.

2.  AppDynamics 계정 이름 및 액세스 키를 사용하여 로그인합니다.

3.  이름이 AppDynamicsdotNetAgentSetup64.msi인 파일을 다운로드합니다. 해당 파일을 실행하지 마세요.

## <span id="addagent"></span></a>Azure 역할에 .NET 에이전트 추가 및 시작 수정

이 단계에서는 AppDynamics에서 모니터링할 Visual Studio 솔루션의 역할을 계측합니다. Azure용 AppDynamics를 사용하기 위해 기존 Windows 마법사 스타일의 설치 절차를 수행할 필요는 없습니다.

1.  Visual Studio에서 새 Azure 프로젝트를 만들거나 기존 Azure 프로젝트를 엽니다.

2.  새 프로젝트를 만든 경우 웹 역할 및/또는 작업자 역할 프로젝트를 솔루션에 추가합니다.

3.  모니터링할 각 웹 및 작업자 역할 프로젝트에 다운로드한 .NET 에이전트 .msi 파일을 추가합니다.

    각 *역할 프로젝트*에는 연결된 단일 .NET 에이전트 .msi 파일이 있지만, 프로젝트의 각 *역할 인스턴스*마다 별도의 .NET 에이전트 라이선스가 필요합니다.

4.  모니터링할 각 웹 및 작업자 역할 프로젝트에 이름이 startup.cmd인 텍스트 파일을 추가하고 해당 파일에 다음 줄을 붙여넣습니다.

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  모니터링할 각 웹 및 작업자 역할에 대해 AppDynamics 에이전트 .msi 파일 및 startup.cmd 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사**로 설정합니다.

    ![항상 복사][항상 복사]

6.  Azure 프로젝트의 ServiceDefinition.csdef 파일에서, 각 WorkerRole 및 WebRole 요소의 매개 변수와 함께 startup.cmd를 호출하는 시작 작업 요소를 추가합니다.

    다음 줄을 추가합니다.

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    설명:

    -   *your controller host* 및 *your controller port*는 계정에 할당된 컨트롤러 호스트 및 포트이며, *your account name* 및 *your access key*는 AppDynamics에서 할당한 자격 증명입니다. 이 정보는 AppDynamics에 등록할 때 수신한 전자 메일에 나와 있으며 AppDynamic 홈페이지에서도 확인할 수 있습니다. [AppDynamics 계정 등록][AppDynamics 계정 등록]을 참조하세요.

    -   *your application name*은 선택한 응용 프로그램의 이름입니다. 이 이름으로 AppDynamics 컨트롤러 인터페이스에서 응용 프로그램을 식별합니다.

    ServiceDefinition.csdef 파일은 다음과 유사합니다.

    ![서비스 정의][서비스 정의]

## <a name="publish"></a>Azure에 AppDynamics 계측 응용 프로그램 게시

각 AppDynamics 계측 역할 프로젝트에 대해 다음을 수행합니다.

1.  Visual Studio에서 역할 프로젝트를 선택합니다.

2.  Azure에 게시를 선택합니다.

## <a name="monitor"></a>응용 프로그램 모니터링

1.  시작 전자 메일과 AppDynamics 계정 홈페이지에 나와 있는 URL에서 AppDynamics 컨트롤러에 로그인합니다.

2.  응용 프로그램에 몇몇 요청을 전송하여 모니터링할 일부 트래픽을 발생시킨 후 몇 분 정도 기다립니다.

3.  AppDynamics 컨트롤러에서 응용 프로그램을 선택합니다.

4.  응용 프로그램을 모니터링합니다.

## <a name="learn"></a>자세한 정보

설명서 및 동영상에 대한 링크는 AppDynamics 계정 홈페이지를 참조하세요.

이 문서의 최신 업데이트는 <http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure>(영문)의 위키 버전에 있습니다.

  [AppDynamics 정의]: #what
  [필수 조건]: #prereq
  [AppDynamics 계정 등록]: #register
  [AppDynamics에서 .NET 에이전트 다운로드]: #download
  [Azure 역할에 .NET 에이전트 추가 및 시작 수정]: #addagent
  [Azure에 AppDynamics 계측 응용 프로그램 게시]: #publish
  [응용 프로그램 모니터링]: #monitor
  [AppDynamics 다이어그램]: ./media/cloud-services-how-to-appdynamics/addiagram.png
  [항상 복사]: ./media/cloud-services-how-to-appdynamics/adcopyalways.png
  [서비스 정의]: ./media/cloud-services-how-to-appdynamics/adscreen.png
