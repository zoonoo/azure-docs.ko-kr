이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1.  [모바일 서비스 iOS SDK][]를 아직 설치하지 않은 경우 지금 설치합니다.

2.  Xcode의 Project Navigator에서 Quickstart 폴더에 있는 TodoService.m 및 TodoService.h 파일을 모두 열고 다음과 같은 import 문을 추가합니다.

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  ToDoService.h 파일에서 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        @property (nonatomic, strong)   MSClient *client;

    이렇게 하면 서비스에 연결되는 MSClient를 나타내는 속성이 생성됩니다.

4.  TodoService.m 파일에서 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create an MSTable property for your items. 

    이 주석 뒤에 @interface 선언 안에 다음 코드 줄을 추가합니다.

        @property (nonatomic, strong)   MSTable *table;

    이렇게 하면 모바일 서비스 테이블을 나타내는 속성이 생성됩니다.

5.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

6.  **대시보드** 탭을 클릭하여 **사이트 URL**을 기록해 두고 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록해 둡니다.

    ![][]

    앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

7.  Xcode에서 TodoService.m을 열고 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Initialize the Mobile Service client with your URL and key.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    그러면 모바일 서비스 클라이언트 인스턴스가 만들어집니다.

8.  이 코드에서 **APPURL** 및 **APPKEY**의 값을 6단계에서 얻은 모바일 서비스의 URL 및 응용 프로그램 키로 바꿉니다.

9.  다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create an MSTable instance to allow us to work with the TodoItem table.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        self.table = [self.client getTable:@"TodoItem"];

    그러면 TodoItem 테이블 인스턴스가 만들어집니다.

10. 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    이렇게 하면 아직 완료되지 않은 모든 작업을 반환하는 쿼리가 생성됩니다.

11. 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Query the TodoItem table and update the items property with the results from the service.

    이 주석 및 후속 **completion** 블록 호출을 다음 코드로 바꿉니다.

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. **addItem** 메서드를 찾아 이 메서드의 본문에 다음 코드를 추가합니다.

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    이 코드는 삽입 요청을 모바일 서비스에 보냅니다.

13. **completeItem** 메서드를 찾아 이 메서드의 본문에 다음 코드를 추가합니다.

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    이 코드는 완료로 표시된 TodoItems를 제거합니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

  [모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  []: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
