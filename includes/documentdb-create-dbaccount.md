1.	[Azure 미리 보기 포털](https://portal.azure.com/)에 로그인합니다.
2.	표시줄에서 **새로 만들기**를 클릭하고 **데이터 + 저장소**를 선택한 후에 **DocumentDB**를 클릭합니다. 

	![만들기 블레이드에서 **새로 만들기** 단추, **데이터 + 저장소**가 강조 표시되며 데이터 + 저장소 블레이드에서 **DocumentDB**가 강조 표시된 Azure 미리 보기 포털의 스크린샷][1]   

	시작 보드에서 Azure 마켓플레이스를 검색하여 **데이터 + 분석**을 선택한 다음 **DocumentDB**를 선택하고 **만들기**를 클릭해도 됩니다.  
	
	![DocumentDB 타일이 강조 표시된 마켓 플레이스 블레이드를 표시하며 만들기 단추가 강조 표시된 DocumentDB 블레이드를 표시하는 Azure 미리보기 포털의 스크린샷][2]   
   

3. **새 DocumentDB(미리 보기)** 블레이드에서 DocumentDB 계정에 대해 원하는 구성을 지정합니다. 
 
	![새 DocumentDB (Preview) 블레이드의 스크린샷][3] 


	- **ID** 상자에 DocumentDB 계정을 식별하기 위한 이름을 입력합니다. 이 값은 URI 내의 호스트 이름이 됩니다. ID는 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3~50자여야 합니다. 선택한 끝점 이름에 documents.azure.com이 추가되고 이 결과는 DocumentDB 계정 끝점이 됩니다.

	- DocumentDB 미리 보기는 단일 표준 가격 책정 계층을 지원하므로 **가격 책정 계층** 렌즈가 잠깁니다. 자세한 내용은 [DocumentDB 가격](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)을 참조하세요.

	- **옵션 구성** 렌즈는 DocumentDB 계정에 할당된 초기 용량을 지정하는 데 사용됩니다.  DocumentDB는 용량 단위를 사용하여 DocumentDB 계정 크기를 조정할 수 있습니다. 각 용량 단위에는 예약된 데이터베이스 저장소 및 처리량이 포함됩니다.  기본적으로, 1 용량 단위가 프로비전됩니다.  언제든지 [미리 보기 관리 포털](https://portal.azure.com/#gallery/Microsoft.DocumentDB)을 통해 DocumentDB 계정에 사용 가능한 용량 단위 수를 조정할 수 있습니다. DocumentDB 계정 용량 및 처리량에 대한 자세한 내용은 [DocumentDB 용량 및 성능 관리][documentdb-manage] 문서를 참조하세요.

	- **리소스 그룹**에서 DocumentDB 계정에 대한 리소스 그룹을 선택하거나 만듭니다.  기본적으로 새 리소스 그룹이 생성됩니다.  그러나 DocumentDB 계정을 추가할 기존 리소스 그룹을 선택할 수 있습니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](azure-preview-portal-using-resource-groups.md).를 참조하세요.

	- **구독**에서 DocumentDB 계정에 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 계정이 자동으로 선택됩니다.*
 
	- **위치**를 사용하여 DocumentDB 계정이 호스트되는 지리적 위치를 지정합니다.   

3.	새 DocumentDB 계정 옵션을 구성했으면 **만들기**를 클릭합니다.  DocumentDB 계정을 만드는 데 몇 분 정도 걸릴 수 있습니다.  상태를 확인하려면 시작 보드에서 진행률을 모니터링하면 됩니다.  
	![시작 보드에서 만들기 타일의 스크린샷][4]  
  
	그렇지 않은 경우 알림 허브에서 진행률을 모니터링할 수 있습니다.  

	![DocumentDB 계정이 만들어지고 있음을 보여주는 알림 허브의 스크린샷][5]  

	![DocumentDB 계정을 만들어 리소스 그룹에 배포했음을 보여주는 알림 허브의 스크린샷][6]

4.	DocumentDB 계정이 생성되면 기본 설정으로 사용할 수 있습니다.

	*DocumentDB 계정의 기본 일관성이 세션으로 설정됩니다.  [미리 보기 관리 포털](https://portal.azure.com/#gallery/Microsoft.DocumentDB)을 통해 기본 일관성 설정을 조정할 수 있습니다.*  
	![리소스 그룹 블레이드의 스크린샷][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[방법: DocumentDB 계정 만들기]: #Howto
[다음 단계]: #NextSteps
[documentdb-manage]:../articles/documentdb-manage.md

<!--HONumber=49-->