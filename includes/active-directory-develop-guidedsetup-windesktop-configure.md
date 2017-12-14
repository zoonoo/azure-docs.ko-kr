
## <a name="create-an-application-express"></a>응용 프로그램(Express) 만들기
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  안내식 설정 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>솔루션에 응용 프로그램 등록 정보 추가(고급)
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동해 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 메일을 입력합니다. 
3. 안내식 설정 옵션이 선택 취소되어 있는지 확인합니다.
4. `Add Platform`를 클릭한 다음 `Native Application`을 선택하고 [저장]을 누릅니다.
5. 응용 프로그램 ID의 GUID를 복사하고, Visual Studio로 이동한 다음 `App.xaml.cs`를 열어 `your_client_id_here`를 방금 등록한 응용 프로그램 ID로 바꿉니다.

```csharp
private static string ClientId = "your_application_id_here";
```
