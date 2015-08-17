
이제 메모리 내 로컬 컬렉션 대신 Azure 모바일 서비스에 항목을 저장하도록 앱을 업데이트합니다.

* **TodoService.h**에서 다음 줄을 찾습니다.

```
// TODO - create an MSClient property
```

이 주석은 다음 줄로 바꿉니다. 이렇게 하면 서비스에 연결되는 `MSClient`을(를) 나타내는 속성이 만들어집니다.

```
@property (nonatomic, strong)   MSClient *client;
```


* **TodoService.m**에서 다음 줄을 찾습니다.

```
// TODO - create an MSTable property for your items
```

이 주석은 `@interface` 선언 내에서 다음 줄로 바꿉니다. 이렇게 하면 모바일 서비스 테이블을 나타내는 속성이 생성됩니다.

```
@property (nonatomic, strong)   MSTable *table;
```


* 관리 포털에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다. **대시보드** 탭을 클릭하고 **사이트 URL** 값을 기록해 둡니다. 이제 **키 관리**를 클릭하고 **응용 프로그램 키**를 기록해 둡니다. 앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.


* **TodoService.m**에서 다음 줄을 찾습니다.

```
// Initialize the Mobile Service client with your URL and key.
```

이 주석 뒤에 다음 코드 줄을 추가합니다. `APPURL` 및 `APPKEY`을(를) 이전 단계에서 받은 사이트 URL 및 응용 프로그램 키로 바꿉니다.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* **TodoService.m**에서 다음 줄을 찾습니다.

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

이 주석은 다음 줄로 바꿉니다. 그러면 TodoItem 테이블 인스턴스가 만들어집니다.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* **TodoService.m**에서 다음 줄을 찾습니다.

```
// Create a predicate that finds items where complete is false
```

이 주석은 다음 줄로 바꿉니다. 이렇게 하면 아직 완료되지 않은 모든 작업을 반환하는 쿼리가 생성됩니다.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* 다음 줄을 찾습니다.

```
// Query the TodoItem table and update the items property with the results from the service
```

이 주석 및 후속 **completion** 블록 호출을 다음 코드로 바꿉니다.

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* **addItem** 메서드를 찾아 이 메서드의 본문을 다음 코드로 바꿉니다. 이 코드는 삽입 요청을 모바일 서비스에 보냅니다.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* **completeItem** 메서드를 찾고 주석으로 처리된 다음 코드 줄을 찾습니다.

```
// Update the item in the TodoItem table and remove from the items array on completion
```

메서드 본문의 해당 지점에서 메서드 끝까지를 다음 코드로 바꿉니다. 이 코드는 완료로 표시된 todo 항목을 제거합니다.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* TodoListController.m에서 **onAdd** 메서드를 찾아 다음 코드로 덮어씁니다.

```
- (IBAction)onAdd:(id)sender
{
    if (itemText.text.length  == 0)
    {
        return;
    }

    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
    UITableView *view = self.tableView;
    [self.todoService addItem:item completion:^(NSUInteger index)
    {
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        [view insertRowsAtIndexPaths:@[ indexPath ]
                    withRowAnimation:UITableViewRowAnimationTop];
    }];

    itemText.text = @"";
}
```

<!---HONumber=August15_HO6-->