<properties urlDisplayName="Set Up Computer for Media Services" pageTitle="미디어 서비스용 컴퓨터 설정 방법 - Azure" metaKeywords="" description="Media Services SDK for .NET을 사용하는 미디어 서비스에 대한 일반적인 필수 구성 요소에 대해 알아봅니다. 또한 Visual Studio 앱을 만드는 방법에 대해서도 알아봅니다." metaCanonical="" services="media-services" documentationCenter="" title="미디어 서비스 개발을 위해 컴퓨터 설정" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="setup-dev"> </a><span class="short header">미디어 서비스 개발을 위해 컴퓨터 설정</span>

이 섹션에는 Media Services SDK for .NET을 사용하는 미디어 서비스 개발에 대한 일반적인 필수 조건이 들어 있습니다. 또한 개발자에게 Media Services SDK 개발을 위해 Visual Studio 응용 프로그램을 생성하는 방법을 보여 줍니다.

### 필수 조건

-   신규 또는 기존 Azure 구독의 미디어 서비스 계정. [미디어 서비스 계정을 만드는 방법][미디어 서비스 계정을 만드는 방법](영문)을 참조하세요.
-   운영 체제: Windows 7, Windows 2008 R2 또는 Windows 8
-   .NET Framework 4
-   Visual Studio 2013, Visual Studio 2012 또는 Visual Studio 2010 SP1(Professional, Premium, Ultimate 또는 Express)
-   [windowsazure.mediaservices Nuget][windowsazure.mediaservices Nuget] 패키지를 사용하여 Azure SDK for .NET을 설치합니다. 다음 섹션에서는 **Nuget**을 사용하여 Azure SDK를 설치하는 방법을 설명합니다.

## <a name="setup-account"></a><span class="short header">미디어 서비스용 Azure 계정 설정</span>

미디어 서비스 계정을 설정하려면 Azure 관리 포털을 사용하세요(권장). [미디어 서비스 계정을 만드는 방법](영문)을 참조하세요. 관리 포털에서 계정을 만들면 미디어 서비스 개발을 위해 컴퓨터를 설정할 준비가 됩니다.

### Visual Studio에서 응용 프로그램 생성

이 섹션에서는 Visual Studio에서 프로젝트를 생성하고 미디어 서비스 개발에 대해 설정하는 방법을 설명합니다. 이 경우 프로젝트는 C# Windows 콘솔 응용 프로그램이지만 미디어 서비스 응용 프로그램에 대해 생성할 수 있는 다른 프로젝트 유형에도 여기에 설명된 설정 방법이 그대로 적용됩니다(예: Windows Forms 응용 프로그램 또는 ASP.NET 웹 응용 프로그램).

1.  Visual Studio 2013, Visual Studio 2012 또는 Visual Studio 2010 SP1에서 새 C# **콘솔 응용 프로그램**을 만듭니다. **이름**, **위치** 및 **솔루션 이름**을 입력하고 **확인**을 클릭합니다.
2.  대상 프레임워크를 .NET Framework 4로 설정합니다. 이렇게 하려면 Visual Studio 프로젝트에서 마우스 오른쪽 단추를 클릭하고 속성을 선택한 다음 응용 프로그램 탭에서 대상 프레임워크로 .NET Framework 4를 설정합니다.
3.  **System.Configuration** 어셈블리에 참조를 추가합니다. **System.Configuration**에 참조를 추가하려면 **솔루션 탐색기**에서 **참조** 노드를 마우스 오른쪽 단추로 클릭하고 **참조 추가...**를 선택합니다. **참조 관리** 대화 상자에서 **System.Configuration**을 선택하고 **확인**을 클릭합니다.
4.  [windowsazure.mediaservices Nuget][windowsazure.mediaservices Nuget] 패키지를 사용하여 **Azure SDK for .NET.**에 대한 참조를 추가합니다. 그러면 기타 모든 종속 어셈블리도 설치됩니다.

    Nuget을 사용하여 참조를 추가하려면 다음과 같이 합니다. Visual Studio **주 메뉴**에서 **도구** -\> **라이브러리 패키지 관리자** -\> **패키지 관리자 콘솔**을 선택합니다. 콘솔 창에서 **Install-Package windowsazure.mediaservices**를 입력하고 **Enter** 키를 누릅니다.

5.  다음 코드를 사용하여 Program.cs 파일의 앞부분에 있는 기존 using 문을 덮어씁니다.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure.MediaServices.Client;

이제 미디어 서비스 응용 프로그램 개발을 시작할 준비가 되었습니다.

## <a name="setup-account"></a><span class="short header">미디어 서비스에 연결</span>

미디어 서비스 프로그래밍에서 수행하는 거의 모든 작업에는 서버 컨텍스트 개체에 대한 참조가 필요합니다. 서버 컨텍스트는 모든 미디어 서비스 프로그래밍 개체에 대해 프로그래밍 방식의 액세스를 제공합니다.

서버 컨텍스트에 대한 참조를 가져오려면 다음 코드 예제처럼 컨텍스트 형식의 새 인스턴스를 만듭니다. 계정 설정 프로세스 중 받은 미디어 서비스 계정 이름 및 계정 키를 생성자에게 전달합니다.

    // Create and cache the Media Services credentials in a static class variable.
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);

    // Use the cached credentials to create CloudMediaContext.
    _context = new CloudMediaContext(_cachedCredentials);

**CloudMediaContext** 유형의 모듈 수준 변수를 정의하여 서버 컨텍스트에 대한 참조를 유지하는 것이 대개 유용합니다. 자세한 내용은 [Media Services SDK for .NET을 사용하여 미디어 서비스에 연결][Media Services SDK for .NET을 사용하여 미디어 서비스에 연결]을 참조하세요.

이 항목의 나머지 코드 예제에서는 \*\*\_context\*\*라는 변수를 사용하여 서버 컨텍스트를 참조합니다.

## 다음 단계

이제 미디어 서비스 프로그래밍을 위해 컴퓨터를 설정하고 Visual Studio 솔루션을 만들었습니다. [암호화된 자산을 만들어 저장소에 업로드하는 방법][암호화된 자산을 만들어 저장소에 업로드하는 방법] 항목으로 이동하세요.
[미디어 서비스 계정을 만드는 방법]: ../media-services-create-account/
[암호화된 자산을 만들어 저장소에 업로드하는 방법]: ../media-services-create-encrypted-asset-upload-storage/

  [미디어 서비스 계정을 만드는 방법]: ../media-services-create-account/
  [windowsazure.mediaservices Nuget]: http://nuget.org/packages/windowsazure.mediaservices
  [Media Services SDK for .NET을 사용하여 미디어 서비스에 연결]: http://msdn.microsoft.com/ko-kr/library/azure/jj129571.aspx
