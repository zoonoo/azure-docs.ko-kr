Azure 마켓플레이스에서 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 앱을 사용할 수 있습니다. Azure 마켓플레이스에서 만든 웹 앱은 [Azure Preview 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에 연결하는 데 사용되는 브라우저 외에 다른 소프트웨어를 설치하지 않아도 됩니다.

이 자습서에서는 다음에 대해 알아봅니다.

- Azure 마켓플레이스를 통해 새 웹 앱을 만드는 방법

- Azure Preview 포털을 통해 웹 앱을 배포하는 방법
 
기본 템플릿을 사용하는 WordPress 블로그를 만들게 됩니다. 다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.


![Wordpress 블로그][13]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 포털에서 웹 앱 만들기

1. Azure Preview 포털에 로그인합니다.

2. **마켓플레이스** 아이콘을 클릭하거나 대시보드 왼쪽 아래에 있는 **새로 만들기** 아이콘을 클릭하고 맨 아래에서 **웹 + 모바일**, **Azure 마켓플레이스**를 순서대로 선택하여 Azure 마켓플레이스를 엽니다.
	
	![새로 만들기][5]
	
3. **웹 앱**을 선택합니다. **WordPress**를 검색한 다음 **WordPress** 아이콘을 클릭합니다.

	![목록에서 WordPress][7]
	
5. WordPress 앱에 대한 설명을 읽은 후 **만들기**를 선택합니다.

6. **웹 앱**을 클릭하고 웹 앱을 구성하는 데 필요한 값을 입력합니다.
	
   ![앱 구성][8]

7. **데이터베이스**을 클릭하고 MySQL 데이터베이스를 구성하는 데 필요한 값을 입력합니다. 

   ![데이터베이스 구성][database]

8. 필요한 경우 **구독**을 클릭하고 사용할 구독을 지정합니다. 

7. 웹 앱 정의를 완료한 후 **만들기**를 클릭하고 새 웹 앱이 작성되는 동안 기다립니다.

   앱이 작성되면 웹 앱과 데이터베이스가 포함된 리소스 그룹이 표시됩니다.

   ![그룹 표시][resourcegroup]

## WordPress 웹 앱 시작 및 관리
	
1. 새 웹 앱을 클릭하여 앱에 대한 세부 정보를 확인합니다.

   ![대시보드 시작][10]

2. **Essentials** 페이지에서 **찾아보기**를 클릭하거나 **Url** 아래 링크를 클릭하여 웹 앱 시작 페이지를 엽니다.

   ![사이트 URL][browse]

3. WordPress를 설치하지 않은 경우 WordPress에서 필요한 적절한 구성 정보를 입력하고 **WordPress 설치**를 클릭하여 구성을 완료하고 웹 앱의 로그인 페이지를 엽니다.

4. **로그인**을 클릭하고 자격 증명을 입력합니다.  

5. 아래 웹 앱과 유사한 새 WordPress 웹 앱이 만들어집니다.   

	![사용자 WordPress 사이트][13]






[5]: ./media/website-from-gallery/startmarketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/selectwordpress.png
[8]: ./media/website-from-gallery/configureweb.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/seewebapp.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/configuredb.png
[resourcegroup]: ./media/website-from-gallery/showgroup.png
[browse]: ./media/website-from-gallery/browse.png

<!---HONumber=54-->