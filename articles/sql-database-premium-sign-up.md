<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL Database" pageTitle="Sign up for Azure Premium for SQL Database" metaKeywords="" description="Describes how to sign up for the Premium for SQL Database preview, request your Premium database quota, and then upgrade a database to Premium in Azure SQL Database." metaCanonical="" services="cloud-services" documentationCenter="" title="Sign up for the preview of Premium for Azure SQL Database" authors="karaman" solutions="" manager="" editor="tysonn" />

Azure SQL 데이터베이스용 Premium의 미리 보기에 등록
===================================================

이 자습서에서는 SQL 데이터베이스용 Premium의 미리 보기에 참가하는 데 필요한 단계에 대해 알아봅니다.

Azure SQL 데이터베이스는 SQL 데이터베이스용 Premium이라는 새 서비스의 제한된 미리 보기를 릴리스했습니다. Premium 데이터베이스는 클라우드 응용 프로그램의 더욱 예측 가능한 성능을 위해 예약된 리소스를 제공합니다.

[이 항목에서 설명하는 기능은 미리 보기에서만 사용 가능합니다. 이 항목은 시험판 설명서이며 향후 릴리스에서 변경될 수 있습니다.]

목차
----

-   [1단계: SQL 데이터베이스용 Premium의 미리 보기에 등록](#SignUp)
-   [2단계: Premium 데이터베이스 할당량 요청](#Quota)
-   [3단계: Premium 데이터베이스 만들기](#Upgrade)

1단계: SQL 데이터베이스용 Premium의 미리 보기에 등록
----------------------------------------------------

이 기능을 사용하는 첫 번째 단계는 SQL 데이터베이스용 Premium 미리 보기에 대한 구독에 등록하는 것입니다.

1.  Microsoft 계정을 사용하여 [Azure 미리 보기 기능 페이지](http://account.windowsazure.com/PreviewFeatures)에 로그인합니다.

    **참고** - Azure 계정 관리자만 계정 포털에 액세스할 수 있습니다. 구독에 대한 계정 관리자가 아닌 경우, 해당 관리자가 구독에 대한 등록 프로세스를 완료하도록 하십시오. Azure 계정 및 구독에 대한 자세한 내용은 [구매 옵션](http://account.windowsazure.com/PreviewFeatures)을 참조하십시오.

    ![Image1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2.  미리 보기 기능 목록에서 **SQL 데이터베이스용 Premium** 항목을 찾은 다음 이 항목과 연결된 **지금 시도** 단추를 클릭합니다.

    ![Image2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)

3.  미리 보기에 대해 등록할 구독을 선택합니다.

    ![Image3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

    활성화된 유료 Azure 구독만 미리 볼 수 있습니다. 여러 구독을 등록할 수 있지만, 각 구독은 한 번만 등록할 수 있습니다.

    미리 보기 구독에 등록함으로써 추가 요금이 발생하지는 않지만, 활성화되고 Premium 할당량이 부여되면 Premium 데이터베이스 생성 또는 업그레이드의 경우 [SQL 데이터베이스 가격 정보](http://www.windowsazure.com/en-us/pricing/details/sql-database/)에 설명된 가격을 따릅니다.

    등록 요청의 현재 상태가 미리 보기 기능 목록에 반영됩니다.

    ![Image4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4.  요청은 현재 용량 및 요구에 따라 승인됩니다. 구독이 활성화되기까지 최대 2일이 걸릴 수 있으며, 시간이 더 오래 걸리는 항목은 사용량이 많거나 공용 미리 보기 용량이 충족되었음을 나타냅니다.

5.  미리 보기에 대해 구독이 활성화되면 전자 메일 알림을 받게 됩니다.

2단계: Premium 데이터베이스 할당량 요청
---------------------------------------

미리 보기에 대해 구독이 활성화되면 Premium 데이터베이스를 생성하려는 각 서버에 대해 Premium 데이터베이스 할당량을 요청해야 합니다. 용량이 제한되어 있으므로 Premium 데이터베이스를 만들려는 서버에 대한 할당량만 요청하고 보류 중인 불필요한 요청은 취소합니다.

1.  Microsoft 계정을 사용하여 [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.

    **참고** - 구독이 미리 보기에 대해 등록되면 계정 관리자, 서비스 관리자 및 구독 공동 관리자가 할당량을 요청할 수 있습니다.

2.  **SQL 데이터베이스** 확장에서 **서버** 목록으로 이동합니다.
3.  Premium 데이터베이스 할당량을 요청하려는 서버를 선택합니다.
4.  상단 탐색 모음에서 번개 아이콘을 클릭하여 선택한 서버에 대해 **빠른 시작**으로 이동합니다.
5.  **Premium Database** 섹션에서 **Request Premium Database Quota**를 클릭합니다.

    ![Image6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)

    요청을 제출하면 할당량을 받을 때까지 최대 5일이 걸릴 수 있습니다. 시간이 더 오래 걸리는 항목은 사용량이 많거나 미리 보기 용량이 충족되었음을 나타냅니다.

    Premium 데이터베이스 할당량 요청에 대한 추가 참고 사항:

    -   무료 평가판을 구독하는 고객은 할당량을 사용할 수 없습니다.
    -   할당량은 처음에 제한되어 있으며, 현재 용량 및 사용 가능한 용량에 따라 요청할 수 있습니다.
    -   Microsoft는 15일 후 미사용 할당량을 회수할 수 있습니다.
    -   구독의 각 논리 서버에 대해 하나의 할당량 요청만 제출할 수 있습니다.
    -   처음에 할당량은 논리 서버당 데이터베이스 하나로 제한됩니다.
    -   데이터베이스 할당량 요청은 무료이지만, Premium Edition 데이터베이스 생성 또는 기존 Web 또는 Business Edition 데이터베이스를 Premium으로 업그레이드하면 데이터베이스 비용이 증가합니다.

6.  서버의 **빠른 시작** 페이지에서 할당량 요청 상태를 볼 수 있습니다.

    ![Image7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)

7.  Premium 데이터베이스 할당량을 요청하고 할당량을 사용할 수 있게 되면 전자 메일 알림을 받습니다.
8.  할당량이 부여되면 서버의 **빠른 시작** 또는 **대시보드** 탭에서 서버의 남은 Premium 데이터베이스 할당량을 볼 수 있습니다.

    ![Image8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

3단계: Premium 데이터베이스 만들기
----------------------------------

할당량을 받으면 새 Premium Edition 데이터베이스를 만들거나 기존 Web 또는 Business 데이터베이스를 Premium으로 업그레이드하여 예약된 용량과 더욱 예측 가능한 성능을 사용할 수 있습니다.

![Image9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)

![Image10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)

자세한 내용은 [Premium 데이터베이스 관리](http://go.microsoft.com/fwlink/p/?LinkID=311927)(영문)를 참조하십시오.

**참고:** 24시간 이내에 Premium 상태 또는 데이터베이스의 예약 크기를 한 번만 변경할 수 있습니다.

다음 단계
---------

Premium 데이터베이스에 대한 추가 정보는 다음을 참조하십시오.

-   [Premium 데이터베이스 관리(영문)](http://go.microsoft.com/fwlink/p/?LinkID=311927)
-   [SQL 데이터베이스용 Premium 지침(영문)](http://go.microsoft.com/fwlink/p/?LinkId=313650)

