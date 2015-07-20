모바일 서비스의 오프라인 기능을 지원하기 위해 `IMobileServiceSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MobileServiceClient.SyncContext`을(를) 초기화했습니다. 이 경우 로컬 저장소는 SQLite 데이터베이스입니다.

모바일 서비스에 대한 일반 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `IMobileServiceSyncTable.PullAsync` 및 `MobileServiceClient.SyncContext.PushAsync` 메서드를 사용했습니다.

*  서버의 변경 내용을 푸시하기 위해 `IMobileServiceSyncContext.PushAsync()`을(를) 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다.

    CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.
   
* 서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `IMobileServiceSyncTable.PullAsync`을(를) 호출했습니다.

    끌어오기 작업에서는 항상 푸시 작업을 먼저 수행합니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다.

    클라이언트에 저장된 데이터를 필터링하기 위해 쿼리를 지정할 수 있는 `PullAsync()`의 오버로드도 있습니다. 쿼리가 전달되지 않으면 `PullAsync()`은(는) 해당 테이블(또는 쿼리)에 모든 행을 끌어옵니다. 쿼리를 전달하여 앱에서 동기화해야 하는 변경 사항만 필터링할 수 있습니다.

* 증분 동기화를 사용하려면 쿼리 ID를 `PullAsync()`(으)로 전달합니다. 쿼리 ID는 마지막 풀링 작업의 결과에서 마지막으로 업데이트된 타임스탬프를 저장하는 데 사용됩니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 쿼리에 매개 변수가 있으면 동일한 매개 변수 값이 쿼리 ID의 일부여야 합니다.

    예를 들어 userid를 필터링할 경우 이는 쿼리 ID의 일부여야 합니다.

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    증분 동기화를 옵트아웃하려면 `null`을(를) 쿼리 ID로 전달합니다. 이 경우 `PullAsync`에 대한 모든 호출에서 모든 레코드가 검색되므로 이는 잠재적으로 비효율적입니다.

* 모바일 서비스 데이터베이스에서 삭제된 레코드를 장치 로컬 저장소에서 제거하려면 [일시 삭제]를 사용해야 합니다. 그렇지 않으면 앱이 주기적으로 `IMobileServiceSyncTable.PurgeAsync()`에 대해 호출하여 로컬 저장소를 제거합니다.

<!---HONumber=July15_HO2-->