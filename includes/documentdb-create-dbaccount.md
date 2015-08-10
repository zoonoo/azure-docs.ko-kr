1.	온라인 [Microsoft Azure Preview 포털](https://portal.azure.com/)에 로그인합니다.
2.	점프 모음에서 **새로 만들기**를 클릭하고 **데이터+저장소**를 클릭한 다음 **Azure DocumentDB**를 클릭합니다. 

	![새로 만들기 단추, 만들기 블레이드의 데이터+저장소, 데이터+저장소 블레이드의 Azure DocumentDB가 강조 표시된 Azure Preview 포털의 스크린샷][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create database button highlighted][2]    -->
   

3. **새 DocumentDB 계정** 블레이드에서 DocumentDB 계정에 대한 구성을 원하는 대로 지정합니다.
 
	![새 DocumentDB 블레이드의 스크린샷][3]


	- **ID** 상자에 DocumentDB 계정을 식별하기 위한 이름을 입력합니다. **ID**의 유효성이 검사되면 **ID** 상자에 녹색 확인 표시가 나타납니다. **ID** 값은 URI 내의 호스트 이름이 됩니다. **ID**는 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다. 선택한 끝점 이름에 *documents.azure.com*이 추가되고, 그 결과가 DocumentDB 계정 끝점이 됩니다.

	- DocumentDB는 단일 표준 계정 계층을 지원하므로 **계정 계층** 렌즈가 잠깁니다. 자세한 내용은 [DocumentDB 가격](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)을 참조하세요.

	- **리소스 그룹**에서 DocumentDB 계정에 대한 리소스 그룹을 선택하거나 만듭니다. 기본적으로 새 리소스 그룹이 생성됩니다. 그러나 DocumentDB 계정을 추가할 기존 리소스 그룹을 선택할 수 있습니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.

	- **구독**에서 DocumentDB 계정에 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 계정이 기본적으로 선택됩니다.
 
	- **위치**를 사용하여 DocumentDB 계정을 호스트하는 지리적 위치를 지정합니다.

4.	새 DocumentDB 계정 옵션을 구성했으면 **만들기**를 클릭합니다. DocumentDB 계정을 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 시작 보드에서 진행률을 모니터링하면 됩니다. ![시작 보드 만들기 타일 - 온라인 데이터베이스 작성자의 스크린샷][4]
  
	그렇지 않은 경우 알림 허브에서 진행률을 모니터링할 수 있습니다.

	![DocumentDB 계정이 만들어지고 있음을 보여주는 데이터베이스 신속하게 만들기 - 온라인 데이터베이스 작성자 알림][5]

	![DocumentDB 계정을 만들어 리소스 그룹에 배포했음을 보여주는 알림 허브의 스크린샷][6]

5.	DocumentDB 계정이 생성되면 온라인 포털에서 기본 설정으로 사용할 수 있습니다. DocumentDB 계정의 기본 일관성은 **세션**으로 설정됩니다. **DocumentDB 계정**블레이드에서 **기본 일관성** 타일을 클릭하여 기본 일관성 설정을 조정할 수 있습니다.

    ![리소스 그룹 블레이드의 스크린샷][7]

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=July15_HO5-->