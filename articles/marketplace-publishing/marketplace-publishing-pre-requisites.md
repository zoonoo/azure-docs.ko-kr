<properties
   pageTitle="Azure 마켓플레이스용 제품을 만들기 위한 비기술적인 필수 조건 | Microsoft Azure"
   description="제품을 만들고 다른 사람이 구입할 수 있도록 Azure 마켓플레이스에 배포하기 위한 요구 사항을 이해합니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="01/07/2016"
  ms.author="hascipio; v-divte"/>

# Azure 마켓플레이스용 제품을 만들기 위한 일반적인 필수 조건
제품 만들기 프로세스를 진행하는 데 필요한 일반적인 비즈니스 프로세스 중심 필수 조건을 이해합니다.

## Microsoft에 판매자로 등록되어 있는지 확인
Microsoft에 판매자 계정을 등록하는 방법에 대한 자세한 지침은 [계정 만들기 및 등록](marketplace-publishing-accounts-creation-registration.md)을 참조하세요.

- **이미 등록**된 경우 회사에서 계정을 소유한 사용자나 등록에 사용된 자격 증명을 알아봅니다.
- **게시 계정의 소유자가 아닌** 경우에는 게시 계정의 소유자가 사용자의 Microsoft 계정을 [게시 포털](https://publish.windowsazure.com)에 공동 관리자로 추가하도록 합니다. **게시자** 탭의 **관리자** 링크를 사용합니다.
- Azure 게시 프로세스의 관련자가 이 주소로 전송되는 메일을 받는지 확인합니다. 게시 프로세스를 완료하려면 이 메일을 모니터링하고 응답해야 합니다.
- 한 명의 사용자와 연결된 계정을 사용하지 마세요. 해당 사용자가 퇴사하는 경우 SKU 관련 정보에 액세스하고 SKU를 게시하는 기능에 영향을 줄 수 있기 때문입니다.

> [AZURE.IMPORTANT]무료(또는 사용자 라이선스 필요) 제품만 게시하려는 경우에는 회사 세금 및 은행 정보를 작성하지 않아도 됩니다.

> 판매자로 활동하려면 회사 등록을 완료해야 합니다. 하지만, 회사가 Microsoft 개발자 계정의 세금 및 은행 정보를 준비하는 동안 개발자는 [게시 포털](https://publish.windowsazure.com)에서 가상 컴퓨터 이미지를 만들어서 인증을 받고 Azure 스테이징 환경에서 테스트할 수 있습니다. Azure 마켓플레이스에 제품을 게시하는 마지막 단계에서만 판매자 계정 승인을 완료하면 됩니다.

> 판매자 등록을 완료하는 데 문제가 있으면 다음과 같이 지원 티켓을 기록합니다. 1. [지원](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=ko-KR&pesid=15635&ccsid=635847950577064286)에 문의 2. **개발자 센터**를 선택합니다. 3. **게시자 프로필** 선택 4. 연락 방법 선택.


## Azure "종량제" 구독 구입
이 구독은 VM 이미지를 만들고 [Azure 마켓플레이스](http://azure.microsoft.com/marketplace)에 이미지를 넘겨주는 데 사용됩니다. 기존 구독이 없는 경우 https://account.windowsazure.com/signup?offer=ms-azr-0003p에서 등록하세요.

## "판매" 국가
> [AZURE.WARNING]Microsoft Azure 마켓플레이스에서 서비스를 판매하려면, 승인된 “판매" 국가 중 한 곳에 사용자의 등록 법인이 속해 있는지 확인해야 합니다. 이 제한은 지급액 및 세금 때문에 있습니다. Microsoft는 가까운 장래에 이 국가 목록을 확장하기 위해 적극 노력하고 있으니 기대해 주세요. 전체 목록은 [Azure 마켓플레이스 참가 정책](http://go.microsoft.com/fwlink/?LinkID=526833)의 섹션 1b를 참조하세요.

## 다음 단계
다음은 각 제품 유형에 대한 기술적인 필수 조건입니다. Azure 마켓플레이스용으로 만들 제품 유형에 해당하는 문서로 연결되는 링크를 클릭하세요.

| 가상 컴퓨터 이미지 | 개발자 서비스 | 데이터 서비스 | 솔루션 템플릿 |
|-----|-----|-----|-----|
| [VM 기술적인 필수 조건](marketplace-publishing-vm-image-creation-prerequisites.md) | 개발자 서비스 기술적인 필수 조건 | [데이터 서비스 기술적인 필수 조건](marketplace-publishing-data-service-creation-prerequisites.md) | [솔루션 템플릿 기술적인 필수 조건](marketplace-publishing-solution-template-creation-prerequisites.md) |

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0121_2016-->