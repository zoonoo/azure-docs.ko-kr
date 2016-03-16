## API 앱 클라이언트 생성 

Visual Studio의 API 앱 도구를 사용하면 데스크톱, 스토어 및 모바일 앱에서 Azure API 앱을 호출하는 C# 코드를 쉽게 생성할 수 있습니다.

1. Visual Studio에서 [API 앱 만들기](../article/app-service-api/app-service-dotnet-create-api-app.md) 항목의 API 앱이 포함된 솔루션을 엽니다. 

2. **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.

	![새 프로젝트 추가](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. **새 프로젝트 추가** 대화 상자에서 다음 단계를 수행합니다.

	1. **Windows 데스크톱** 범주를 선택합니다.
	
	2. **콘솔 응용 프로그램** 프로젝트 템플릿을 선택합니다.
	
	3. 프로젝트의 이름을 지정합니다.
	
	4. **확인**을 클릭하여 기존 솔루션에 새 프로젝트를 생성합니다.
	
	![새 프로젝트 추가](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. 새로 만든 콘솔 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Azure API 앱 클라이언트**를 선택합니다.

	![새 클라이언트 추가](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. **Microsoft Azure API 앱 클라이언트 추가** 대화 상자에서 다음 단계를 수행합니다.

	1. **다운로드** 옵션을 선택합니다. 
	
	2. 드롭다운 목록에서 이전에 만든 API 앱을 선택합니다.
	
	3. **확인**을 클릭합니다.

	![생성 화면](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	마법사가 API 메타데이터 파일을 다운로드하고 API 앱을 호출하기 위한 형식화된 인터페이스를 생성합니다.

	![생성 발생](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	코드 생성이 완료되고 나면 솔루션 탐색기에 API 앱의 이름으로 만들어진 새 폴더가 표시됩니다. 이 폴더에는 클라이언트 및 데이터 모델을 구현하는 코드가 들어 있습니다.

	![생성 완료](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. 프로젝트 루트에서 **Program.cs** 파일을 열고 **Main** 메서드를 다음 코드로 바꿉니다.

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## API 앱 클라이언트 테스트

API 앱을 코딩했으면 코드를 테스트해야 합니다.

1. **솔루션 탐색기**를 엽니다.

2. 이전 섹션에서 만든 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭합니다.

3. 콘솔 응용 프로그램의 상황에 맞는 메뉴에서 **디버그 > 새 인스턴스 시작**을 선택합니다.

4. 콘솔 창이 열리고 모든 연락처가 표시됩니다.

	![콘솔 앱 실행](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. **Enter** 키를 눌러 콘솔 창을 해제합니다.

<!---HONumber=Oct15_HO3-->