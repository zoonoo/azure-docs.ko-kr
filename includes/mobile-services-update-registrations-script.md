1.  관리 포털에서 **Data** 탭을 클릭한 후 **Registrations** 테이블을 클릭합니다.

    ![][]

2.  **Registrations**에서 **Script** 탭을 클릭하고 **Insert**를 선택합니다.

    ![][1]

    **Registrations** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registrations');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertChannelIfNotFound });
            function insertChannelIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

이렇게 하면 새 테이블에 등록 정보를 저장하는 새 삽입 스크립트가 등록됩니다.

  []: ./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png
  [1]: ./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png
