
다음으로, 등록을 시도하기 전에 사용자가 인증되도록 알림 등록 시기를 변경해야 합니다.


1. Eclipse의 Package Explorer에서 ToDoActivity.java 파일을 열고 `onCreate` 메서드를 찾습니다. `onCreate` 메서드의 다음 코드를 `createTable` 메서드의 시작 부분으로 이동합니다.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     `authenticate` 메서드가 완료되면 `createTable` 메서드가 호출됩니다. 전체 `createTable` 메서드는 다음과 유사합니다.

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	

<!---HONumber=62-->