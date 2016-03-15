### 콘솔 응용 프로그램 만들기 및 어셈블리 가져오기

Visual Studio에서 새 콘솔 응용 프로그램을 만들고 Azure 저장소 클라이언트 라이브러리를 포함하는 NuGet 패키지를 설치하려면:

1. Visual Studio에서 **파일 > 새 프로젝트**를 선택한 다음 Visual C# 템플릿 목록에서 **Windows > 콘솔 응용 프로그램**을 선택합니다.
2. 콘솔 응용 프로그램 이름을 지정한 다음 **확인**을 클릭합니다.
3. 프로젝트가 만들어지면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 .NET용 Azure 저장소 클라이언트 라이브러리 패키지와 종속성을 설치합니다.

또한 이 문서의 코드 예제에서 [Microsoft Azure 구성 관리자 라이브러리](https://msdn.microsoft.com/library/azure/mt634646.aspx)를 사용하여 콘솔 응용 프로그램의 app.config 파일에서 저장소 연결 문자열을 검색합니다. 응용 프로그램이 Microsoft Azure 또는 데스크톱이나 모바일, 웹 응용 프로그램에서 실행되는지 여부와 관계없이 런타임 시 Azure 구성 관리자를 사용하여 연결 문자열을 검색할 수 있습니다.

Azure 구성 관리자 패키지를 설치하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "ConfigurationManager"를 검색하고 **설치**를 클릭하여 패키지를 설치합니다.

Azure 구성 관리자 사용은 선택 사항입니다. 또한 .NET Framework의 [ConfigurationManager 클래스](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)와 같은 API를 사용할 수 있습니다.

<!---HONumber=AcomDC_0309_2016-->