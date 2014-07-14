새 모바일 서비스에서 앱 데이터를 저장하려면 먼저 연결된 SQL 데이터베이스 인스턴스에 새 테이블을 만들어야 합니다.

1.  관리 포털에서 **Mobile Services**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  **Data** 탭을 클릭한 후 **+Create**를 클릭합니다.
    
       ![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)
        
       **Create new table** 대화 상자가 표시됩니다.

3.  **Table name**에 *TodoItem*을 입력한 후 확인 단추를 클릭합니다.

 	![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png) 

	기본 사용 권한이 설정된 새 저장소 테이블 **TodoItem**이 생성됩니다. 이는 앱과 함께 분산되는 응용 프로그램 키가 있다면 누구나 테이블의 데이터에 액세스하고 내용을 변경할 수 있다는 것을 의미합니다.

> [WACOM.NOTE] 
> 모바일 서비스 빠른 시작에 같은 테이블 이름이 사용됩니다. 하지만 각 테이블은 주어진 모바일 서비스에 특정된 스키마에 생성됩니다. 이렇게 하면 여러 모바일 서비스가 같은 데이터베이스를 사용할 때 발생하는 데이터 충돌을 예방할 수 있습니다.

1.  새 **TodoItem** 테이블을 클릭하고 데이터 행이 없는 것을 확인합니다.

2.  **Columns** 탭을 클릭합니다. 다음의 기본 열이 자동으로 생성되었는지 확인합니다.
    
    <table  border="1" cellpadding="10">
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
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
    
     	<tr>
     	<td>__version</td>
    
     	<td>timestamp (MSSQL)</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
     	
     	</table>

	이 부분이 모바일 서비스의 테이블 최소 요구 사항입니다.     	
	<div  class="dev-callout">
	<b>참고</b>
	<p>모바일 서비스에서 동적 스키마를 사용하면 삽입 또는 업데이트 작업에서 JSON 개체를 모바일 서비스에 보낼 때 새 열이 자동으로 생성됩니다.</p>
	</div>

이제 새 모바일 서비스를 앱의 데이터 저장소로 사용할 준비가 되었습니다.