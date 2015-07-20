
이 자습서는 Visual Studio 2013의 유니버설 Windows 앱 프로젝트인 [GetStartedWithMobileServices 앱](http://go.microsoft.com/fwlink/p/?LinkID=510826)을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1. [개발자 코드 샘플 사이트]에서 GetStartedWithMobileServices 샘플 앱의 JavaScript 버전을 다운로드합니다. 

3. Visual Studio 2013에서 다운로드한 프로젝트를 열고 솔루션 탐색기에서 js 폴더를 확장한 후 default.js 파일을 검사합니다.

   	추가된 **TodoItem** 개체가 메모리 내 `WinJS.Binding.List`에 저장됩니다.

4. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

5. 앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png)

   	저장된 텍스트가 표시됩니다.

6. Windows Phone 8.1 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭한 다음 **F5** 키를 눌러 Windows Phone 스토어 앱을 실행합니다.

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. 3단계와 4단계를 반복하여 샘플이 같은 방식으로 동작하는지 확인합니다.

<!---HONumber=July15_HO2-->