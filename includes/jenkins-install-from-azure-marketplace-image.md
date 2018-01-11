1. 브라우저에서 [Azure Marketplace image for Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)(Jenkins용 Azure Marketplace 이미지)를 엽니다.

1. **지금 가져오기**를 선택합니다.

    ![[지금 가져오기]를 선택하여 Jenkins Marketplace 이미지에 대한 설치 프로세스를 시작합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 가격 정보와 사용 조건 정보를 검토한 후 **계속**을 선택합니다.

    ![Jenkins Marketplace 이미지 가격 및 사용 조건 정보입니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. **만들기**를 선택하여 Azure Portal에서 Jenkins 서버를 구성합니다. 

    ![Jenkins Marketplace 이미지를 설치합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. **기본 사항** 탭에서 다음 값을 지정합니다.

    - **이름** - `Jenkins`를 입력합니다.
    - **사용자** - Jenkins가 실행 중인 가상 머신에 로그인할 때 사용할 사용자 이름을 입력합니다.
    - **인증 형식** - **암호**를 선택합니다.
    - **암호** - Jenkins가 실행 중인 가상 머신에 로그인할 때 사용할 암호를 입력합니다.
    - **암호 확인** - Jenkins가 실행 중인 가상 머신에 로그인할 때 사용할 암호를 다시 입력합니다.
    - **Jenkins release type**(Jenkins 릴리스 형식) - **LTS**를 선택합니다.
    - **구독** - Jenkins를 설치할 Azure 구독을 선택합니다.
    - **리소스 그룹** - **새로 만들기**를 선택하고 Jenkins 설치를 구성하는 리소스 컬렉션의 논리 컨테이너 역할을 하는 리소스 그룹 이름을 입력합니다.
    - **위치** - **미국 동부**를 선택합니다.

    ![[기본] 탭에 Jenkins에 대한 인증 및 리소스 그룹 정보를 입력합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. **확인**을 선택하여 **설정** 탭으로 이동합니다. 

1. **설정** 탭에서 다음 값을 지정합니다.

    - **크기** - Jenkins 가상 머신에 적합한 크기 지정 옵션을 선택합니다.
    - **VM 디스크 유형** - HDD(하드 디스크 드라이브) 또는 SSD(반도체 드라이브)를 지정하여 가상 머신에 허용된 저장소 디스크 유형을 나타냅니다.
    - **공용 IP 주소** - IP 주소 이름은 기본적으로 이전 페이지에서 지정한 Jenkins에 접미사 -IP가 붙습니다. 옵션을 선택하여 이 기본값을 변경할 수 있습니다.
    - **도메인 이름 레이블** - Jenkins 가상 머신에 대한 정규화된 URL 값을 지정합니다.

    ![[설정] 탭에서 Jenkins에 대한 가상 머신 설정을 입력합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. **확인**을 선택하여 **요약** 탭으로 이동합니다.

1. **요약** 탭이 표시되면 입력된 정보의 유효성을 검사합니다. **유효성 검사 통과** 메시지가 표시되면 **확인**을 선택합니다. 

    ![[요약] 탭이 표시되고 선택한 옵션의 유효성을 검사합니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. **만들기** 탭이 표시되면 **만들기**를 선택하여 Jenkins 가상 머신을 만듭니다. 서버가 준비되면 Azure Portal에 알림이 표시됩니다.

    ![Jenkins가 준비되었음을 알리는 메시지입니다.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)