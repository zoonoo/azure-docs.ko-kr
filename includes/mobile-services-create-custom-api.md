1.  [Azure 관리 포털][1]에 로그인하여 **Mobile Services**를 클릭한 후 앱을 클릭합니다.
    
    ![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2.  **API** 탭을 클릭한 후 **Create a custom API**를 클릭합니다.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)
    
    **Create a new custom API** 대화 상자가 표시됩니다.

3.  **API name**에 *completeall*을 입력한 후 확인 단추를 클릭합니다.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)
    
    이제 새 API가 생성됩니다.


   
    > [WACOM.NOTE] 기본 권한이 설정되어 앱의 모든 사용자가 사용자 지정 API를 호출할 수 있습니다. 하지만 응용 프로그램 키가 안전하게 분산되거나 저장되지 않으므로 보안 인증으로 생각하면 안 됩니다. 이 때문에 데이터를 수정하거나 모바일 서비스에 영향을 미치는 작업은 인증된 사용자만 액세스하도록 제한하는 것을 고려해야 합니다.

4.  API 테이블에서 새 **completeall** 항목을 클릭합니다.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5.  **Script** 탭을 클릭하여 기존 코드를 다음의 코드로 바꾼 후 **Save**를 클릭합니다.
    
         exports.post = function(request, response) {
             var mssql = request.service.mssql;
             var sql = "UPDATE todoitem SET complete = 1 " + 
                 "WHERE complete = 0; SELECT @@ROWCOUNT as count";
             mssql.query(sql, {
                 success: function(results) {			
                     if(results.length == 1)							
                         response.send(200, results[0]);			
                 }
             })
         };
    
    이 코드에서는 **todoitem** 테이블에 직접 액세스하여 모든 항목에 완료 플래그를 설정하기 위해 [mssql 개체][2]를 사용합니다. **exports.post** 함수를 사용하기 때문에 작업을 수행하기 위해 클라이언트는 POST 요청을 보내고, 변경된 행의 수가 클라이언트에 정수 값으로 반환됩니다.

> [WACOM.NOTE] 
[Express.js 라이브러리][3](영문)를 사용하여 구현된 사용자 지정 함수에 [request][4]와 [response][5] 개체가 제공됩니다. 자세한 정보는 [사용자 지정
> API][6]를 참조하십시오.

다음 과정에서는 새 단추와 새 사용자 지정 API를 비동기적으로 호출하는 코드를 추가하는 빠른 시작 앱을 수정합니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: https://manage.windowsazure.com/
[2]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj554212.aspx
[3]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[4]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj554218.aspx
[5]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn303373.aspx
[6]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn280974.aspx