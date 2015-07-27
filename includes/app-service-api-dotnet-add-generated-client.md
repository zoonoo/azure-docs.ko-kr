3. **솔루션 탐색기**에서 프로젝트(솔루션이 아님)를 마우스 오른쪽 단추로 클릭하고 **추가 > Azure API 앱 클라이언트**를 선택합니다. 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. **Azure API 앱 클라이언트 추가** 대화 상자에서 **Azure API 앱에서 다운로드**를 클릭합니다.

5. 드롭다운 목록에서 호출할 API 앱을 선택합니다.

7. **확인**을 클릭합니다.

	![생성 화면](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	마법사가 API 메타데이터 파일을 다운로드하고 API 앱을 호출하기 위한 형식화된 인터페이스를 생성합니다.

	![생성 발생](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	코드 생성이 완료되고 나면 **솔루션 탐색기**에 API 앱의 이름으로 만들어진 새 폴더가 표시됩니다. 이 폴더에는 클라이언트 클래스 및 데이터 모델을 구현하는 코드가 들어 있습니다.

	![생성 완료](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=July15_HO3-->