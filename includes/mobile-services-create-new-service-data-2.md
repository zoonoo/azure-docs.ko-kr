새 모바일 서비스에서 앱 데이터를 저장하려면 먼저 연결된 SQL 데이터베이스 인스턴스에 새 테이블을 만들어야 합니다.

1. 관리 포털에서 **Mobile Services**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

   	**Create new table** 대화 상자가 표시됩니다.

3. **테이블 이름**에 _TodoItem_을 입력한 후 확인 단추를 클릭합니다.

  기본 사용 권한이 설정된 새 저장소 테이블 **TodoItem**이 생성됩니다. 이는 앱과 함께 배포되는 응용프로그램 키가 있다면 누구나 테이블의 데이터에 액세스하고 내용을 변경할 수 있다는 것을 의미합니다.

	>[AZURE.NOTE] The same table name is used in Mobile Services quickstart. However, each table is created in a schema that is specific to a given mobile service. This is to prevent data collisions when multiple mobile services use the same database.

4. 새 **TodoItem** 테이블을 클릭하고 데이터 행이 없는 것을 확인합니다.

5. **Columns** 탭을 클릭합니다. 다음의 기본 열이 자동으로 생성되었는지 확인합니다.
	
	<table border="1" cellpadding="10">
<tr>
<th>열 이름</th>
<th>유형</th>
<th>인덱스</th>
</tr>
<tr>
<td>id</td>
<td>string</td>
<td>Indexed</td>
</tr>
<tr>
<td>__createdAt</td>
<td>date</td>
<td>Indexed</td>
</tr>
<tr>
<td>__updatedAt</td>
<td>date</td>
<td><font color="transparent">-</font></td>
</tr>
<tr>
<td>__version</td>
<td>timestamp (MSSQL)</td>
<td><font color="transparent">-</font></td>
</tr> 	
</table>이 부분이 모바일 서비스의 테이블 최소 요구 사항입니다.

    > [AZURE.NOTE]모바일 서비스에서 동적 스키마를 사용하면 삽입 또는 업데이트 작업에서 JSON 개체를 모바일 서비스에 보낼 때 새 열이 자동으로 생성됩니다.

이제 새 모바일 서비스를 앱의 데이터 저장소로 사용할 준비가 되었습니다.

<!---HONumber=July15_HO4-->