<properties
   pageTitle="게시자 계정 만들기 및 등록 | Microsoft Azure"
   description="Microsoft 판매자 계정을 만들고 승인 시 Azure 마켓플레이스에서 다양한 제품 유형을 판매할 수 있는 지침입니다."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/06/2015"
   ms.author="hascipio"/>

# Microsoft 판매자 계정 만들기
이 문서에서는 Azure 마켓플레이스에서 승인된 Microsoft 판매자가 되기 위해 필요한 계정 만들기 및 등록 과정을 안내합니다.

## 1\. Microsoft 계정 만들기
> [AZURE.WARNING]게시 프로세스를 시작하려면 Microsoft 계정을 만들어야 합니다. 이 계정은 **Microsoft 개발자 센터** 및 **Azure 게시 포털**에 등록 및 로그인하는 데 사용됩니다. Azure 마켓플레이스 제품에 대해 Microsoft 계정이 하나만 있으면 됩니다. 서비스 또는 제품에 한정될 필요가 없습니다.

사용자 이름을 구성하는 주소는 귀사의 도메인에 속하고 귀사의 IT 팀에서 제어해야 합니다(예: publishing@example.com)). 결제, 세금 정보 및 보고가 이 계정을 통해 전송됩니다.

  >[AZURE.WARNING]"Azure" 및 "Microsoft"와 같은 단어는 Microsoft 계정 등록에 대해 지원되지 않습니다. 계정 만들기 및 등록 프로세스를 완료하려면 이러한 단어를 사용하지 마십시오.

### 지침

1. 회사의 도메인 내에서 DL(배포 목록) 또는 SG(보안 그룹)를 만듭니다.
  - DL에 온보딩 팀을 추가합니다.
  - DL은 확인 메일을 수신하기 위해 라이브 상태여야 합니다.
  - DL에 대한 메일 주소로 marketplace@example.com을 사용합니다.
  - 내부 시스템에서 이 작업을 완료해야 합니다.
2. 새 Chrome Incognito 또는 Internet Explorer InPrivate 브라우징 세션을 열어 기존 계정에 로그인되지 않았는지 확인합니다.
3. DL 메일을 사용하여 Microsoft 계정을 등록합니다.
 - [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx)에서 Microsoft 계정을 등록할 수 있습니다.
 - 메일 주소로 marketplace@example.com을 사용합니다.
 - 이제 Microsoft 계정 ID는 marketplace@example.com입니다.
4. 등록할 때 유효한 전화번호를 사용하세요. 신원 확인이 필요할 경우 시스템에서 확인 코드를 문자 메시지 또는 자동 응답 통화로 전송합니다.
5. DL에 전송된 메일 주소를 확인합니다.
6. 이제 Microsoft 개발자 센터에서 새 Microsoft 계정을 사용할 준비가 되었습니다.

> [AZURE.TIP]DL을 사용하면 여러 사람이 지급 정보 보고에 중요한 메일 알림을 받을 수 있습니다. Microsoft 계정 소유권은 양도될 수 있으며 단일 사용자에게 한정되지도 않습니다.

## 2\. Microsoft 개발자 센터 계정 만들기
Microsoft 개발자 센터는 회사 정보를 등록하는 데 한 번 사용됩니다. 등록자는 회사의 정식 담당자여야 하며 신원을 확인할 수 있는 개인 정보를 제공해야 합니다. 등록자는 회사에서 공유되는 Microsoft 계정을 사용해야 하며 **동일한 계정을 Azure 게시 포털에서도 사용해야 합니다.** 계정 만들기를 시작하기 전에 회사에 Microsoft 개발자 센터 계정이 아직 없는지 확인해야 합니다. 프로세스 중에 회사 주소 정보, 은행 계좌 정보 및 세금 정보를 수집합니다. 이러한 정보는 일반적으로 재무 또는 업무 연락처에서 확인할 수 있습니다.

> [AZURE.IMPORTANT]제품 만들기 및 배포의 여러 단계를 진행하기 위해서는 다음 판매자 프로필 구성 요소를 완료해야 합니다.


| 판매자 프로필 | 초안 시작 | 스테이징 | 무료 게시 및 솔루션 템플릿 | 상업적 게시 |
|----|----|----|----|----|
|회사 등록 | 필수 | 필수 | 필수 | 필수 |
|세금 프로필 ID | 옵션 | 옵션 | 옵션 | 필수 |
|은행 계좌 | 옵션 | 옵션 | 옵션 | 필수 |


> [AZURE.NOTE]BYOL(사용자 라이선스 필요)은 가상 컴퓨터에만 지원되며 **무료** 제공됩니다.


### 회사 계정 등록
1. 새 Chrome Incognito 또는 Internet Explorer InPrivate 브라우징 세션을 열어 개인 계정에 로그인되지 않았는지 확인합니다.

2. [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)로 이동합니다.

3. 회사 등록 Microsoft 계정(예: marketplace@example.com))으로 로그인합니다.

    ![그리기][img-signin]

4. "계정 보호 지원" 마법사를 완료하여 전화 번호 또는 메일 주소를 통해 신원을 확인합니다.

    ![그리기][img-verify]

5. "등록 계정 정보" 섹션에서 드롭다운 메뉴의 **계정 국가/지역**을 선택합니다.

    > [AZURE.WARNING]**"판매" 국가:** Azure 마켓플레이스에서 서비스를 판매하기 위해서는 등록된 법인이 승인된 위의 “판매” 국가 중 하나에 속해 있어야 합니다. 이 제한은 지급액 및 세금 때문에 있습니다. Microsoft는 가까운 장래에 이 국가 목록을 확장하기 위해 적극 노력하고 있으니 기대해 주세요. 자세한 내용은 [마켓플레이스 참가 정책](http://go.microsoft.com/fwlink/?LinkID=526833)을 참조하세요.

6. "계정 유형"을 **개인** 또는 **회사**로 선택합니다.

    <!-- Insert screenshot -->> [AZURE.IMPORTANT]계정 유형에 대한 이해를 돕고 여러분을 위한 적합한 선택이 무엇인지 알려면 [계정 유형, 위치 및 수수료](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx) 페이지를 참조하세요.

7. **게시자 표시 이름**을 입력합니다. 이는 일반적으로 여러분의 회사 이름입니다.

    > [AZURE.TIP]현재 Azure 게시 포털은 게시자 표시 이름을 사용하지 않습니다. 하지만 등록 프로세스를 완료하려면 입력해야 합니다.

8. 계정에 대한 연락처 정보를 입력합니다.

    > [AZURE.IMPORTANT]개발자 센터에서 승인된 회사에 대한 확인 프로세스에 사용되므로 정확한 연락처 정보를 제공해야 합니다.

9. 회사 계정의 경우 여러분의 조직을 대신하여 계정을 만들 권한이 부여된 사실을 확인해줄 수 있는 **회사 승인자**에 대한 연락처 정보도 제공해야 합니다. 작업을 마쳤으면 **다음**을 클릭하여 **"지급 섹션"**으로 이동합니다.

10. 결제 정보를 입력하여 해당 계정에 대한 비용을 지불합니다. 등록 비용을 부담하는 프로모션 코드를 가지고 있는 경우 여기에 프로모션 코드를 입력할 수 있습니다. 그렇지 않은 경우 신용 카드 정보(또는 지원되는 시장에서 PayPal)를 제공합니다. 작업을 마쳤으면 **다음**을 클릭하여 **"검토 화면"**으로 이동합니다.

11. 계정 정보를 검토하고 모든 정보가 올바른지 확인합니다. 그런 다음 **Microsoft Azure 마켓플레이스 판매자 계약**의 약관을 읽고 동의합니다. 확인란을 선택하여 이러한 약관을 읽고 동의했음을 나타냅니다.

12. **마침**을 클릭하여 등록을 확인합니다. 개발자 메일 주소로 확인 메시지를 보냅니다.

13. 무료 제품만 게시할 계획인 경우 **Azure 마켓플레이스 게시 포털로 이동**을 클릭하여 이 문서의 섹션 3, [게시 포털에서 계정 등록](#3-register-your-account-in-the-publishing-portal)을 건너뛰어도 됩니다.

14. 상업용 제품을 게시할 계획인 경우 개발자 센터 계정에서 세금 및 은행 정보를 완료해야 하는 **계정 정보 업데이트**를 클릭합니다.

> [AZURE.IMPORTANT]세금 및 은행 정보를 완료하지 않으면 스테이징에서 제품을 제대로 테스트할 수 없고 제품을 프로덕션으로 푸시할 수 없습니다.

나중에 세금 및 은행 정보를 업데이트하려는 경우 섹션 3, [게시 포털에서 계정 등록](#3-register-your-account-in-the-publishing-portal)으로 이동했다가 Azure 게시 포털의 링크를 사용하여 나중에 다시 돌아올 수 있습니다.

### 세금 및 은행 정보 추가
 구매를 위한 상업용 제품을 게시하려는 경우 지급액 및 세금 정보를 추가하고 개발자 센터에서 확인을 위해 제출해야 합니다. 무료 제품만(또는 BYOL 제품) 게시하는 경우 이 정보를 추가할 필요가 없습니다. 나중에 추가해도 되지만 세금 정보를 확인하는 데 시간이 소요됩니다. 구매를 위한 상업용 제품을 제공할 것을 알고 있다면 가능한 빨리 추가하는 것이 좋습니다.

**은행 정보**

1. Microsoft 계정을 사용하여 [Microsoft 개발자 센터](http://dev.windows.com/registration?accountprogram=azure)에 로그인합니다.

2. 왼쪽 메뉴에서 **지급 계좌**를 클릭하고 **지불 방법 선택** 아래에서 **은행 계좌** 또는 **PayPal**을 클릭합니다.

    > [AZURE.IMPORTANT]마켓플레이스에서 고객이 구매하는 상업용 제품인 경우 이 계좌로 해당 구매에 대한 지급액을 받게 됩니다.

3. 결제 정보를 입력하고 만족할 경우 **저장**을 클릭합니다.

    > [AZURE.IMPORTANT]지급 계좌를 업데이트하거나 변경해야 하는 경우 위의 동일한 단계를 따라 현재 정보를 새 정보로 바꿉니다. 지급 계좌를 변경하면 한 번의 지불 주기까지 지불을 연기할 수 있습니다. 이러한 지불 연기는 처음 지급 계좌를 설정할 때와 마찬가지로 계정 변경 내용을 확인해야 하기 때문에 발생합니다. 계정 확인 작업이 끝난 후 여전히 전체 금액에 대해 비용을 지불해야 하는 경우 현재 지불 주기에 대한 모든 지불 대금이 다음 주기에 추가됩니다.

4. **다음**을 클릭합니다.

**세금 정보**

1. Microsoft 계정을 사용하여(필요한 경우) [Microsoft 개발자 센터](http://dev.windows.com/registration?accountprogram=azure)에 로그인합니다.

2. 왼쪽 메뉴에서 **세금 프로필**을 클릭합니다.

3. **세금 양식 작성** 페이지에서 영주권이 있는 국가 또는 지역을 선택한 후 주요 시민권을 보유한 국가 또는 지역을 선택합니다. **다음**을 클릭합니다.

4. 세금 세부 정보를 입력하고 **다음**을 클릭합니다.

> [AZURE.WARNING]Microsoft 개발자 센터 계정에서 세금 및 은행 계좌 정보를 완료하지 않으면 상업용 제품을 프로덕션으로 푸시할 수 없습니다.

## 3\. 게시 포털에서 계정 등록
Azure 게시 포털은 제품을 게시하고 관리하는 데 사용됩니다. 게시 포털에서 제품 만들기 프로세스를 안내하는 유용한 정보를 얻게 됩니다.

> [AZURE.WARNING]여기서는 반드시 Microsoft 개발자 센터 등록에 사용된 것과 동일한 회사 Microsoft 계정을 사용해야 합니다. 마스터 게시자 계정을 만든 후에 지원하기 위해 추가 사용자를 추가할 수 있습니다.

1.	새 Chrome Incognito 또는 Internet Explorer InPrivate 검색 세션을 열어 개인 계정에 로그인되지 않았는지 확인합니다.

2.	[http://publish.windowsazure.com](http://publish.windowsazure.com)으로 이동합니다.

3.	회사 등록 Microsoft 계정(즉, marketplace@example.com))으로 로그인하고 필요에 따라 공동 관리자를 추가할 수 있습니다.

  >[AZURE.TIP]참가 정책은 [Azure 웹 사이트](http://azure.microsoft.com/support/legal/marketplace/participation-policies/)에 설명되어 있습니다.

  > 개발자 센터 등록에 문제가 있으면 아래와 같이 지원 티켓을 기록하세요. 1. [지원](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=ko-KR&pesid=15635&ccsid=635847950577064286)에 문의합니다. 2. **개발자 센터**를 선택합니다. 3. **프로필**을 선택합니다. 4. 연락 방법 선택






## 다음 단계
이제 계정을 만들고 등록했으므로 Azure 마켓플레이스에 게시할 아티팩트 유형(가상 컴퓨터, 개발자 서비스, 데이터 서비스 또는 솔루션 템플릿)을 클릭합니다. 각 제품을 게시하는 방법에 대해 알아보려면 다음 문서 중 하나를 방문하세요.

|| 가상 컴퓨터 이미지 | 개발자 서비스 | 데이터 서비스 | 솔루션 템플릿 | |----|-----|-----|-----|-----| |**2단계: 제품 만들기** |[일반 비기술 필수 조건](marketplace-publishing-pre-requisites.md)| [일반 비기술 필수 조건](marketplace-publishing-pre-requisites.md)| [일반 비기술 필수 조건](marketplace-publishing-pre-requisites.md)| [일반 비기술 필수 조건](marketplace-publishing-pre-requisites.md)| || [VM 이미지 필수 기술][link-single-vm-prereq] | 개발자 서비스 기술 필수 조건 | [데이터 서비스 기술 필수 조건](marketplace-publishing-data-service-creation-prerequisites.md) | [솔루션 템플릿 기술 필수 조건](marketplace-publishing-solution-template-creation-prerequisites.md) | || [VM 이미지 게시 가이드][link-single-vm] | 개발자 서비스 게시 가이드 | [데이터 서비스 게시 가이드](marketplace-publishing-data-service-creation.md) | [솔루션 템플릿 게시 가이드](marketplace-publishing-solution-template-creation.md) | || [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] | [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] | [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] | [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging] |

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=AcomDC_1210_2015-->