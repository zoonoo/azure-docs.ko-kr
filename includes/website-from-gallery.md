갤러리는 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 응용 프로그램을 사용할 수 있습니다. 갤러리에서 만든 웹 응용 프로그램은 Azure 관리 포털에 연결하는 데 사용되는 브라우저 외에 다른 소프트웨어를 설치할 필요가
없습니다.

이 자습서에서는 다음에 대해 알아봅니다.

* 갤러리를 통해 새 사이트를 만드는 방법

* Azure 포털을 통해 사이트를 배포하는 방법

기본 템플릿을 사용하는 WordPress 블로그를 만들게 됩니다. 다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![Wordpress 블로그](./media/website-from-gallery/wordpressgallery-09.png)
<div class="dev-callout"><strong>참고</strong>
<p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 몇 분 만에 무료 체험 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Azure 계정 만들기</a>(영문)를 참조하십시오.</p>
</div>

## 포털에서 웹 사이트 만들기

1.  [Azure 관리 포털][1]에 로그인합니다.

2.  대시보드의 왼쪽 아래에서 **New** 아이콘을 클릭합니다.
    
    ![새로 만들기](./media/website-from-gallery/wordpressgallery-01.png)

3.  **Web Site** 아이콘을 클릭하고 **From Gallery**를 클릭합니다.
    
    ![갤러리에서 만들기](./media/website-from-gallery/wordpressgallery-02.png)

4.  목록에서 WordPress 아이콘을 찾아 클릭한 후 **Next**를 클릭합니다.
    
    ![목록에서
    WordPress](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-php-web-site-gallery/20140818090955/includes/website-from-gallery/wordpressgallery-03.png)

5.  **Configure Your App** 페이지에서 모든 필드에 대해 값을 입력하거나 선택합니다.
* 선택한 URL 이름을 입력합니다.
* **Database** 필드에서 **Create a new MySQL database**를 선택한 채로 둡니다.
* 가장 가까운 지역을 선택합니다.
  
	![앱 구성](./media/website-from-gallery/wordpressgallery-04.png)

1.  그런 후 **Next**를 클릭합니다.

2.  **Create New Database** 페이지에서 새 MySQL 데이터베이스에 이름을 지정하거나 기본 이름을 사용할 수 있습니다. 가장 가까운 지역을 호스팅 위치로 선택합니다. 호스트된 MySQL 데이터베이스에 대한 ClearDB의 사용 약관에 동의하려면 화면 아래쪽의 상자를 선택합니다. 그런 다음 확인 표시를 클릭하여 사이트 만들기를 완료합니다.
    
    ![데이터베이스 만들기](./media/website-from-gallery/wordpressgallery-05.png)

**Complete**를 클릭하면 Azure는 빌드 및 배포 작업을 시작합니다. 웹 사이트를 만들고 배포하는 동안 작업 상태가 웹 사이트 페이지의 아래쪽에 표시됩니다. 모든 작업을 수행한 후 사이트가 성공적으로 배포되면 마지막 상태 메시지가 표시됩니다.

## WordPress 사이트 시작 및 관리

1.  **Web Site** 페이지에서 새 사이트를 클릭하여 그 사이트의 대시보드를 엽니다.
    
    ![대시보드 시작](./media/website-from-gallery/wordpressgallery-06.png)

2.  **Dashboard** 관리 페이지에서 아래로 스크롤하고 **Site Url** 아래의 왼쪽에 있는 링크를 클릭하여 사이트의 시작 페이지를 엽니다.
    
    ![사이트 URL](./media/website-from-gallery/wordpressgallery-07.png)

3.  WordPress에 필요한 해당 구성 정보를 입력하고, **Install WordPress**를 클릭하여 구성을 마무리하고 웹 사이트의 로그인 페이지를 엽니다.
    
    ![WordPress에
    로그인](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-php-web-site-gallery/20140818090955/includes/website-from-gallery/wordpressgallery-08.png)

4.  **Welcome** 페이지에서 지정한 사용자 이름과 암호를 입력하여 새 WordPress 웹 사이트에 로그인합니다.

5.  새 WordPress 사이트는 아래 사이트와 유사합니다.
    
    ![사용자 WordPress
    사이트](./media/website-from-gallery/wordpressgallery-09.png)



[1]: http://manage.windowsazure.com