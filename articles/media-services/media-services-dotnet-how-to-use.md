<properties 
	pageTitle=".NET을 사용하여 미디어 서비스 개발을 위한 컴퓨터를 설정하는 방법" 
	description="미디어 서비스 .NET SDK를 사용하는 미디어 서비스에 대한 일반적인 필수 구성 요소에 대해 알아봅니다. 또한 Visual Studio 앱을 만드는 방법에 대해서도 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/11/2015"
	ms.author="juliako"/>

#.NET을 사용한 미디어 서비스 개발 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

이 항목에서는.NET을 사용하여 미디어 서비스 응용 프로그램 개발을 시작하는 방법을 설명합니다.

**Azure 미디어 서비스 .NET SDK** 라이브러리를 사용하면 .NET을 사용하여 미디어 서비스를 프로그래밍 할 수 있습니다. .NET을 사용한 개발을 더욱 쉽게 도와주는 **Azure 미디어 서비스 .NET SDK 확장** 라이브러리가 제공 됩니다. 이 라이브러리의 확장 방법 및 도움 함수는.NET 코드를 단순화 하는 집합을 포함합니다. 두 라이브러리 모두 **NuGet** 및 **GitHub**를 통해 사용할 수 있습니다.
 

##필수 조건

-   신규 또는 기존 Azure 구독의 미디어 서비스 계정. [미디어 서비스 계정을 만드는 방법](media-services-create-account.md)(영문)을 참조하십시오.
-   운영 체제: Windows 7, Windows 2008 R2 또는 Windows 8.
-   .NET Framework 4.5.
-   Visual Studio 2013, Visual Studio 2012 또는 Visual Studio 2010 SP1(Professional, Premium, Ultimate 또는 Express) 
  

##Visual Studio 프로젝트 만들기 및 구성 

이 섹션에서는 Visual Studio에서 프로젝트를 생성하고 미디어 서비스 개발에 대해 설정하는 방법을 설명합니다. 이 경우 프로젝트는 C# Windows 콘솔 응용 프로그램이지만 미디어 서비스 응용 프로그램에 대해 생성할 수 있는 다른 프로젝트 유형에도 여기에 설명된 설정 방법이 그대로 적용됩니다(예: Windows Forms 응용 프로그램 또는 ASP.NET 웹 응용 프로그램).

이 섹션에서는 미디어 서비스 .NET SDK 추가를 위한 **NuGet**을 사용하는 방법과 기타 종속된 라이브러리를 보여줍니다.

또는 GitHub에서 최신 미디어 서비스 .NET SDK 비트를 가져오고([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) 및 [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), 솔루션을 빌드하고 클라이언트 프로젝트에 대한 참조를 추가합니다. 자동으로 모든 필수적인 종속을 다운로드 및 추출하려면 참고합니다.

1. Visual Studio 2013, Visual Studio 2012 또는 Visual Studio 2010 SP1에서 새 C# 콘솔 응용 프로그램을 만듭니다. **이름**, **위치** 및 **솔루션 이름**을 입력하고 확인을 클릭합니다. 

2. 솔루션을 빌드하십시오.

2. **NuGet**을 사용하여 설치하고 **Azure 미디어 서비스 .NET SDK 확장**을 추가합니다. 이 패키지를 설치하면 **미디어 서비스 .NET SDK**도 설치되고 다른 모든 필수 종속성이 추가됩니다.
	1. 최신 버전의 NuGet이 설치 되어있는지 확인하십시오. 자세한 내용 및 설치 지침은 [NuGet](http://nuget.codeplex.com/)을 참조하세요.
	
	2. 솔루션 탐색기에서 프로젝트의 이름을 마우스 오른쪽 단추를 클릭하고 NuGet 패키지 관리...를 선택합니다.
	
		NuGet 패키지 관리 대화 상자가 나타납니다.

	3. 온라인 갤러리에서 Azure 미디어 서비스 확장에 대한 검색하여 Azure 미디어 서비스 .NET SDK 확장을 선택한 다음 설치 단추를 클릭합니다.
 
		프로젝트가 수정되고 미디어 서비스 .NET SDK 확장, 미디어 서비스 .NET SDK 및 기타 종속 어셈블리에 대한 참조가 추가됩니다.

	4. 클리너 개발 환경의 수준을 올릴 NuGet 패키지 복원을 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [NuGet 패키지 복원"](http://docs.nuget.org/consume/package-restore)을 참조하세요.

3. **System.Configuration** 어셈블리에 참조를 추가합니다. 이 어셈블리는 구성 파일(예: App.config)에 액세스하는 데 사용되는 System.Configuration.**ConfigurationManager** 클래스를 포함합니다.

	참조 관리 대화 상자를 사용하여 참조를 추가하려면 다음과 같이 합니다.

	1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭합니다. 그런 다음 추가 및 참조를 선택합니다.

		참조 관리 대화 상자가 나타납니다.

	2. .NET framework 어셈블리에서 찾아 System.Configuration 어셈블리를 선택합니다.
	3. 확인을 누릅니다.


4. App.config 파일을 열고(기본적으로 추가되지 않은 경우 프로젝트에 파일 추가) *appSettings* 섹션을 파일에 추가합니다. Azure 미디어 서비스 계정 이름 및 계정 키의 값을 다음 예제와 같이 설정합니다.
	
	**계정 이름** 및 **계정 키** 정보를 얻으려면 **Azure 관리 포털**을 열고, 미디어 서비스 계정을 선택한 후 **키 관리** 단추를 클릭합니다.


	<pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>


5. 다음 코드를 사용하여 Program.cs 파일의 앞부분에 있는 기존 using 문을 덮어씁니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

이제 미디어 서비스 응용 프로그램 개발을 시작할 준비가 되었습니다.
 

<!---HONumber=August15_HO7-->