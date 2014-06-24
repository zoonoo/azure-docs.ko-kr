새 모바일 서비스에 데이터를 저장하려면 서비스에 새 저장소 테이블을 만들어야 합니다. 다음 단계는 Visual Studio
2013을 사용하여 모바일 서비스에 새 테이블을 만드는 방법을 안내합니다. 그런 다음 로컬 컬렉션 대신 Azure에 항목을
저장하는 모바일 서비스를 사용하도록 앱을 업데이트합니다.

1.  서버 탐색기에서 **Azure Mobile Services**를 확장하고 오른쪽 마우스 단추로 모바일 서비스를 클릭하고
    **테이블 만들기**를 클릭한 후 **테이블 이름**에 `TodoItem`을 입력합니다.
    
    ![VS 2013에서 테이블
    만들기](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2.  **Advanced**를 확장하고 테이블 작업 권한이 기본 설정인 **Anybody with the Application
    Key**임을 확인한 후 **Create**를 클릭합니다.
    
    ![VS 2013에서 테이블 만들기
    2](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013-2.png)
    
    이 과정을 진행하면 서버에 TodoItem 테이블이 생성됩니다. 응용 프로그램 키가 있는 사용자는 누구나 이 테이블에
    액세스할 수 있고 인증을 받지 않아도 테이블의 데이터를 변경할 수 있습니다.

	<div class="dev-callout">

**참고**

응용 프로그램 키는 응용 프로그램과 함께 분산됩니다. 이 키는 안전하게 분산된 것이 아니므로 보안 토큰으로 생각하면 안
됩니다. 모바일 서비스 데이터에 안전하게 액세스하려면 먼저 사용자 인증을 완료해야 합니다. 자세한 정보는 [사용
권한][1]을 참조하십시오.
</div>




[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193161.aspx
