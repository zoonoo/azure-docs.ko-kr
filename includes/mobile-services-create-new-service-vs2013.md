다음 단계에서는 Azure에 새 모바일 서비스를 만들고 프로젝트에 새 서비스에 액세스할 수 있게 하는 코드를 추가합니다. 모바일 서비스를 만들려면 먼저 Azure 구독 관리에서 Visual Studio로 publishsettings 파일을 가져와야 합니다. 이렇게 하면 Visual Studio를 통해 Azure에 연결하게 됩니다. 모바일 서비스를 만들 때에는 모바일 서비스가 앱 데이터를 저장하기 위해 사용할 Azure SQL 데이터베이스를 지정해야 합니다.

1.  Visual Studio 2013에서 솔루션 탐색기를 열어 마우스 오른쪽 단추로 프로젝트를 클릭한 후 **추가**를 클릭하고 **연결된 서비스...**를 클릭합니다.

    ![연결된 서비스 추가][연결된 서비스 추가]

2.  서비스 관리자 대화 상자에서 **Create service...**를 클릭한 후 Create Mobile Service 대화 상자에 있는 **Subscription**에서 **\<Import...\>**를 선택합니다.

    ![VS 2013에서 새 모바일 서비스 만들기][VS 2013에서 새 모바일 서비스 만들기]

3.  Import Azure Subscriptions에서 **구독 파일 다운로드**를 클릭하고 필요한 경우 Azure 계정에 로그인하고 브라우저가 파일 저장을 요청할 때 **저장**을 클릭합니다.

    ![VS에서 구독 파일 다운로드][VS에서 구독 파일 다운로드]

    <div class="dev-callout"><strong>참고</strong> <p>브라우저에 로그인 창이 표시되며, 로그인 창은 Visual Studio 창 뒤에 나타날 수도 있습니다. 다운로드한 .publishsettings 파일을 저장한 위치를 기억할 수 있도록 적어두는 것이 좋습니다. 프로젝트가 이미 Azure 구독 관리에 연결된 경우 이 단계를 건너뛸 수 있습니다.</p></div>

4.  **찾아보기**를 클릭하고 .publishsettings 파일을 저장한 위치로 이동하여 해당 파일을 선택하고 **열기**를 클릭한 후 **가져오기**를 클릭합니다.

    ![VS에서 구독 가져오기][VS에서 구독 가져오기]

    Visual Studio가 Azure 구독 관리에 연결할 때 필요한 데이터를 가져옵니다. 사용자 구독에 하나 이상의 모바일 서비스가 있는 경우 해당 서비스 이름이 나타납니다.

    <div class="dev-callout"><strong>보안 정보</strong> <p>다운로드한 .publishsettings 파일에는 다른 사람이 사용자 계정에 액세스할 수 있는 정보가 포함되어 있으므로 게시 설정을 가져온 후에는 파일을 삭제하는 것이 좋습니다. 연결된 다른 앱 프로젝트에 사용하기 위해 파일을 보관하려면 보안에 주의를 기울이세요.</p></div>

5.  **Create Mobile Service** 대화 상자로 돌아가서 **Subscription**과 모바일 서비스에 사용하려는 **Region**을 선택하고 모바일 서비스 **이름**을 입력합니다.

    <div class="dev-callout"><strong>참고</strong> <p>모바일 서비스 이름은 고유해야 합니다. 제공한 이름을 사용할 수 없는 경우 <strong>Name</strong> 옆에 빨간 X가 표시됩니다. </p></div>

6.  **Database**에서 **\<Create a free SQL Database\>**를 선택하고 **Server user name**, **Server password** 및 **Server password confirmation**을 입력한 후 **Create**를 클릭합니다.

    ![VS 2013에서 서비스 만들기 2][VS 2013에서 서비스 만들기 2]

    > [WACOM.NOTE]
    >  이 자습서에는 새 무료 SQL 데이터베이스 인스턴스와 서버를 만드는 과정이 포함되어 있으며, 이 새 데이터베이스는 다른 SQL 데이터베이스 인스턴스처럼 다시 사용하고 관리할 수 있습니다. 무료 데이터베이스 인스턴스는 한 개만 사용할 수 있습니다. 새 모바일 서비스로 같은 지역에 데이터베이스를 이미 가지고 있다면 기존 데이터베이스를 대신 선택할 수 있습니다. 기존 데이터베이스를 선택하는 경우에는 정확한 로그인 자격 증명을 제공해야 합니다. 정확하지 않은 자격 증명을 제공하면 모바일 서비스가 비정상 상태로 생성됩니다.

    모바일 서비스가 생성되고 나면 모바일 서비스 클라이언트 라이브러리에 대한 참조가 프로젝트에 추가되고 프로젝트 소스 코드가 업데이트됩니다.

  [연결된 서비스 추가]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [VS 2013에서 새 모바일 서비스 만들기]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [VS에서 구독 파일 다운로드]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [VS에서 구독 가져오기]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
  [VS 2013에서 서비스 만들기 2]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png
