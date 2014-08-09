<properties  linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<h1><a name="setup-dev"> </a><span  class="short header">미디어 서비스 개발을 위해 컴퓨터 설정</span></h1>


이 섹션에는 Media Services SDK for .NET을 사용하는 미디어 서비스 개발에 대한 일반적인 필수 조건이 들어 있습니다. 또한 개발자에게 Media Services SDK 개발을 위해 Visual Studio 응용 프로그램을 생성하는 방법을 보여 줍니다.

### 필수 조건

* 신규 또는 기존 Azure 구독의 미디어 서비스 계정. [미디어 서비스 계정을 만드는 방법][1](영문)을 참조하십시오.
* 운영 체제: Windows 7, Windows 2008 R2 또는 Windows 8
* .NET Framework 4.5 또는 .NET Framework 4
* Visual Studio 2012 또는 Visual Studio 2010 SP1(Professional, Premium,
  Ultimate 또는 Express)
* [windowsazure.mediaservices Nuget][2] 패키지를 사용하여 Azure SDK for .NET을
  설치합니다. 다음 섹션에서는 **Nuget**을 사용하여 Azure SDK를 설치하는 방법을 설명합니다.

<h2><a name="setup-account"></a><span  class="short header">미디어 서비스용 Azure 계정 설정</span></h2>


미디어 서비스 계정을 설정하려면 Azure 관리 포털을 사용하십시오(권장). [미디어 서비스 계정을 만드는 방법](영문)을
참조하십시오. 관리 포털에서 계정을 만들면 미디어 서비스 개발을 위해 컴퓨터를 설정할 준비가 됩니다.

### Visual Studio에서 응용 프로그램 생성

이 섹션에서는 Visual Studio에서 프로젝트를 생성하고 미디어 서비스 개발에 대해 설정하는 방법을 설명합니다. 이 경우
프로젝트는 C# Windows 콘솔 응용 프로그램이지만 미디어 서비스 응용 프로그램에 대해 생성할 수 있는 다른 프로젝트 유형에도
여기에 설명된 설정 방법이 그대로 적용됩니다(예: Windows Forms 응용 프로그램 또는 ASP.NET 웹 응용 프로그램).

1.  Visual Studio 2012 또는 Visual Studio 2010 SP1에서 새 C# **콘솔 응용 프로그램**을
    만듭니다. **이름**, **위치** 및 **솔루션 이름**을 입력하고 **확인**을 클릭합니다.
2.  **System.Configuration** 어셈블리에 참조를 추가합니다. **System.Configuration**에
    참조를 추가하려면 **솔루션 탐색기**에서 **참조** 노드를 마우스 오른쪽 단추로 클릭하고 **참조 추가...**를
    선택합니다. **참조 관리** 대화 상자에서 **System.Configuration**을 선택하고 **확인**을
    클릭합니다.
3.  [windowsazure.mediaservices Nuget][2] 패키지를 사용하여 **Azure SDK for
    .NET.** (Microsoft.WindowsAzure.StorageClient.dll), **Azure Media
    Services SDK for
    .NET**(Microsoft.WindowsAzure.MediaServices.Client.dll) 및 **WCF Data
    Services 5.0 for OData V3**(Microsoft.Data.OData.dll) 라이브러리에 참조를
    추가합니다.
^

    Nuget을 사용하여 참조를 추가하려면 다음과 같이 합니다. Visual Studio **주 메뉴**에서 **도구** -> **라이브러리 패키지 관리자** -> **패키지 관리자 콘솔**을 선택합니다. 콘솔 창에 **Install-Package windowsazure.mediaservices**를 입력하고 **Enter** 키를 누릅니다.

1.  다음 코드를 사용하여 Program.cs 파일의 앞부분에 있는 기존 using 문을 덮어씁니다.

        using System;     	
        using System.Linq;
    	using System.Configuration;
    	using System.IO;
    	using System.Text;
    	using System.Threading;
    	using System.Threading.Tasks;
    	using System.Collections.Generic;
    	using Microsoft.WindowsAzure;
    	using Microsoft.WindowsAzure.MediaServices.Client;

이제 미디어 서비스 응용 프로그램 개발을 시작할 준비가 되었습니다.

<h2><a name="setup-account"></a><span  class="short header">미디어 서비스에 연결</span></h2>


미디어 서비스 프로그래밍에서 수행하는 거의 모든 작업에는 서버 컨텍스트 개체에 대한 참조가 필요합니다. 서버 컨텍스트는 모든
미디어 서비스 프로그래밍 개체에 대해 프로그래밍 방식의 액세스를 제공합니다.

서버 컨텍스트에 대한 참조를 가져오려면 다음 코드 예제처럼 컨텍스트 형식의 새 인스턴스를 반환합니다. 계정 설정 프로세스 중 받은
미디어 서비스 계정 이름 및 계정 키를 생성자에게 전달합니다.

    static CloudMediaContext GetContext()
    {
        // Gets the service context. 
        return new CloudMediaContext(_accountName, _accountKey);} 

**CloudMediaContext** 유형의 모듈 수준 변수를 정의하여 **GetContext**와 같은 메서드에서 반환된 서버
컨텍스트에 대한 참조를 유지하는 것이 대개 유용합니다. 이 항목의 나머지 코드 예제에서는 **_context**라는 변수를
사용하여 서버 컨텍스트를 참조합니다.

<h2>다음 단계</h2>


이제 미디어 서비스 프로그래밍을 위해 컴퓨터를 설정하고 Visual Studio 솔루션을 만들었습니다. [암호화된 자산을 만들어 저장소에 업로드하는 방법][1] 항목으로 이동하십시오. 
[미디어 서비스 계정을 만드는 방법]: http://go.microsoft.com/fwlink/?linkid=256662 
[암호화된 자산을 만들어 저장소에 업로드하는 방법]:http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409



[1]: http://www.windowsazure.com/ko-kr/manage/services/media-services/how-to-create-a-media-services-account/
[2]: http://nuget.org/packages/windowsazure.mediaservices