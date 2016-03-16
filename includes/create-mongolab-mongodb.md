#Azure에서 MongoDB 데이터베이스를 만드는 방법

이 문서에서는 [MongoLab]을 사용하여 [Azure 스토어]에서 MongoDB 데이터베이스를 만드는 방법을 보여 줍니다. [MongoLab]은 Azure 데이터 센터에서 MongoDB 데이터베이스를 실행 및 관리하고 모든 응용 프로그램에서 연결할 수 있게 해 주는 MasS(MongoDB-as-a-Service) 공급자입니다.

[Azure 스토어]에서 MongoDB 데이터베이스를 만들려면 다음을 수행합니다.

1. [Azure 관리 포털][portal]에 로그인합니다.
2. 페이지 맨 아래에 있는 **+NEW**를 클릭하고 **STORE**를 선택합니다.

	![스토어에서 추가 기능 선택](./media/create-mongolab-mongodb/select-store.png)

3. **MongoLab**을 선택하고 프레임 맨 아래에 있는 화살표를 클릭합니다.

	![MongoLab 선택](./media/create-mongolab-mongodb/select-mongo-db.png)

4. 데이터베이스 이름을 입력하고 지역을 선택한 후 프레임 맨 아래에 있는 화살표를 클릭합니다.

	![스토어에서 MongoLab 데이터베이스 구매](./media/create-mongolab-mongodb/purchase-mongodb.png)

5. 확인 표시를 클릭하여 구매를 완료합니다.

	![구매 검토 및 완료](./media/create-mongolab-mongodb/complete-mongolab-purchase.png)

6. 데이터베이스가 만들어진 후 관리 포털의 **ADD-ONS** 탭에서 관리할 수 있습니다.

	![Azure 포털에서 MongoLab 데이터베이스 관리](./media/create-mongolab-mongodb/manage-mongolab-add-on.png)

7. 페이지 맨 아래에 있는 **CONNECTION INFO**를 클릭하여 데이터베이스 연결 정보를 가져올 수 있습니다(위에 표시됨).

	![MongoLab 연결 정보](./media/create-mongolab-mongodb/mongolab-conn-info.png)

[MongoLab]: https://mongolab.com/home
[waws]: /manage/services/web-sites/
[Azure 스토어]: ../articles/overview.md
[portal]: http://windows.azure.com/

<!---HONumber=August15_HO6-->