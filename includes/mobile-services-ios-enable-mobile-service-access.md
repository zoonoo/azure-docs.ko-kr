
이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1. [모바일 서비스 iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)를 아직 설치하지 않은 경우 지금 설치합니다. 설치했으면 WindowsAzureMobileServices.framework 디렉터리를 복사하여 다운로드한 프로젝트에 포함된 WindowsAzureMobileServices.framework 디렉터리를 덮어씁니다. 이렇게 하면 최신 Azure 모바일 서비스 SDK를 사용할 수 있습니다.

2. TodoService.h 파일에서 다음과 같이 주석으로 처리된 코드 줄을 찾습니다.

        // TODO - create an MSClient proeprty

   	이 주석 뒤에 다음 코드 줄을 추가합니다.

        @property (nonatomic, strong)   MSClient *client;

   	이렇게 하면 서비스에 연결되는 MSClient를 나타내는 속성이 생성됩니다.

3. TodoService.m 파일에서 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // TODO - create an MSTable property for your items

   	이 주석 뒤에 @interface 선언 안에 다음 코드 줄을 추가합니다.

        @property (nonatomic, strong)   MSTable *table;

   	이렇게 하면 모바일 서비스 테이블을 나타내는 속성이 생성됩니다.

4. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

5. **대시보드** 탭을 클릭하여 **사이트 URL**을 기록한 후 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록합니다.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

6. Xcode에서 TodoService.m을 열고 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Initialize the Mobile Service client with your URL and key.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    그러면 모바일 서비스 클라이언트 인스턴스가 만들어집니다.

7. 이 코드에서 **APPURL** 및 **APPKEY**의 값을 6단계에서 얻은 모바일 서비스의 URL 및 응용 프로그램 키로 바꿉니다.

8. 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create an MSTable instance to allow us to work with the TodoItem table.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        self.table = [self.client tableWithName:@"TodoItem"];

    그러면 TodoItem 테이블 인스턴스가 만들어집니다.

9. 다음과 같은 주석 처리된 코드 줄을 찾습니다.

 	    // Create a predicate that finds items where complete is false

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    이렇게 하면 아직 완료되지 않은 모든 작업을 반환하는 쿼리가 생성됩니다.

10. 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Query the TodoItem table and update the items property with the results from the service

   	이 주석 및 후속 **completion** 블록 호출을 다음 코드로 바꿉니다.

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. **addItem** 메서드를 찾아 이 메서드의 본문을 다음 코드로 바꿉니다.

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    이 코드는 삽입 요청을 모바일 서비스에 보냅니다.

12. **completeItem** 메서드를 찾고 주석으로 처리된 다음 코드 줄을 찾습니다.

        // Update the item in the TodoItem table and remove from the items array on completion

    메서드 본문의 해당 지점에서 메서드 끝까지를 다음 코드로 바꿉니다.

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	이 코드는 완료로 표시된 TodoItems를 제거합니다.

13. TodoListController.m에서 **onAdd** 메서드를 찾아 다음 코드로 덮어씁니다.

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


이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

<!--HONumber=42-->
