

1. Azure 관리 포털에 로그인하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 선택합니다.

2. **API** 탭을 클릭한 후 **만들기**를 클릭합니다. **Create a new custom API** 대화 상자가 표시됩니다.

3. **API 이름**에 _completeall_을 입력한 후 확인 단추를 클릭하여 새 API를 만듭니다.

	> [AZURE.NOTE]기본 권한이 있는 사용자는 누구나 앱 키를 사용하여 사용자 지정 API를 호출할 수 있습니다. 그러나 응용 프로그램 키는 안전하게 배포되거나 저장되지 않을 수 있기 때문에 보안 자격 증명으로 간주되지 않습니다. 보안을 강화하기 위해 인증된 사용자에게만 액세스를 허용하는 것이 좋습니다.

4. API 테이블에서 **completeall**을 클릭합니다.

5. **Script** 탭을 클릭하여 기존 코드를 다음의 코드로 바꾼 후 **저장**을 클릭합니다. 이 코드에서는 **todoitem** 테이블에 직접 액세스하여 모든 항목에 `complete` 플래그를 설정하기 위해 [mssql 개체]를 사용합니다. **exports.post** 함수를 사용하기 때문에 작업을 수행하기 위해 클라이언트는 POST 요청을 보내고, 변경된 행의 수가 클라이언트에 정수 값으로 반환됩니다.


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



> [AZURE.NOTE]<a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">Express.js 라이브러리</a>를 사용하여 구현된 사용자 지정 API 함수에 <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">request</a>와 <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">response</a> 개체가 제공됩니다. 자세한 정보는 <a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">사용자 지정 API</a>를 참조하세요.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[mssql 개체]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=July15_HO4-->