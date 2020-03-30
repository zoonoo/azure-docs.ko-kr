---
title: 포함 파일
description: 포함 파일
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321789"
---
**테스트 드라이브** 탭을 사용하면 데모(또는 "테스트 드라이브")를 설정하여 고객이 제품을 구매하기 전에 제품을 시험해 볼 수 있습니다. 이 기사에서 자세히 알아보기 [테스트 드라이브란 무엇입니까?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). 오퍼에 대한 테스트 드라이브를 더 이상 제공하지 않으려면 **제안 설정** 페이지로 돌아가서 **테스트 드라이브 사용 을 선택**취소합니다.

### <a name="technical-configuration"></a>기술 구성
다음과 같은 유형의 테스트 드라이브를 사용할 수 있으며 각 드라이브에는 고유한 기술 구성 요구 사항이 있습니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [전원 BI(기술](#technical-configuration-not-required-for-power-bi-test-drives) 구성이 필요하지 않음)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 리소스 관리자 테스트 드라이브에 대한 기술 구성

솔루션을 구성하는 모든 Azure 리소스를 포함하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. Azure 리소스 관리자 [테스트 드라이브](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)설정에 대해 자세히 알아봅니다.

- **지역(필수):** 현재 테스트 드라이브를 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 일반적으로 가장 많은 고객이 예상되는 지역에서 테스트 드라이브를 사용할 수 있도록 하여 가장 좋은 성능을 위해 가장 가까운 지역을 선택할 수 있도록 해야 합니다. 구독이 선택한 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인해야 합니다.

- **인스턴스:** 사용 가능한 인스턴스 유형(핫 또는 콜드) 및 사용 가능한 인스턴스 수를 선택하여 쿠폰을 사용할 수 있는 지역 수를 곱합니다.

**핫**: 이 유형의 인스턴스가 배포되고 선택한 지역당 액세스를 기다리고 있습니다. 고객은 배포를 기다리지 않고 테스트 드라이브의 *Hot* 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객은 전체 배포를 기다리지 않으려므로 *핫* *인스턴스를* 사용할 수 없는 경우 고객 사용량이 중단되는 것을 매우 권장합니다.

**Cold**: 이 유형의 인스턴스는 지역별 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 테스트 드라이브를 요청할 때 배포하기 위해 전체 테스트 드라이브 리소스 관리자 템플릿을 사용해야 하므로 *콜드* 인스턴스는 *Hot* 인스턴스보다 로드 속도가 훨씬 느립니다. 장단점은 테스트 드라이브 기간 동안만 비용을 지불하면 *핫* 인스턴스와 마찬가지로 Azure 구독에서 항상 실행되는 *것은 아닙니다.*

- **테스트 드라이브 Azure 리소스 관리자 템플릿:** Azure 리소스 관리자 템플릿이 포함된 .zip을 업로드합니다.  빠른 시작 문서에서 Azure 리소스 관리자 템플릿 만들기 [및 Azure 포털을 사용 하 여 Azure 리소스 관리자 템플릿 을 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)하는 방법에 대해 자세히 알아봅니다.

- **테스트 드라이브** 기간(필수): 테스트 드라이브가 활성 상태로 유지되는 시간을 # 시간으로 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간은 전체 시간(예: "2" 시간, "1.5"이 유효하지 않음)에 의해서만 설정할 수 있습니다.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 테스트 드라이브를 위한 기술 구성

Microsoft는 이러한 유형의 테스트 드라이브를 사용하여 서비스 프로비저닝 및 배포를 호스팅하고 유지 관리하여 테스트 드라이브를 설정하는 복잡성을 제거할 수 있습니다. 이러한 유형의 호스팅된 테스트 드라이브에 대한 구성은 테스트 드라이브가 비즈니스 센트럴, 고객 참여 또는 운영 대상을 대상으로 하는지 여부에 관계없이 동일합니다.

- **최대 동시 테스트** 드라이브(필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정합니다. 각 동시 사용자는 테스트 드라이브가 활성화되어 있는 동안 Dynamics 365 라이선스를 사용하므로 최대 제한 집합을 지원할 수 있는 충분한 라이센스가 있는지 확인해야 합니다. 권장 값은 3~5입니다.

- **테스트 드라이브** 기간(필수): 시간 수를 정의하여 테스트 드라이브가 활성 상태로 유지되는 시간을 입력합니다. 이 많은 시간이 지나면 세션이 종료되고 더 이상 라이선스 중 하나를 사용하지 않습니다. 제품의 복잡성에 따라 2~24시간 의 가치를 제공하는 것이 좋습니다. 이 기간은 전체 시간(예: "2" 시간, "1.5"이 유효하지 않음)에 의해서만 설정할 수 있습니다.  시간이 부족하고 테스트 드라이브에 다시 액세스하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스** URL(필수): 고객이 테스트 드라이브를 시작할 URL입니다. 일반적으로 샘플 데이터가 설치된 앱을 실행하는 Dynamics 365 인스턴스의 URL입니다(예: `https://testdrive.crm.dynamics.com`).

- **인스턴스 웹 API** URL(필수): Microsoft 365 계정에 로그인하고 **설정** \&gt으로 이동하여 Dynamics 365 인스턴스에 대한 웹 API URL을 검색합니다. **사용자 정의** \&GT; **개발자 리소스** \&gt; **인스턴스 웹 API(서비스 루트 URL)**- 여기에 있는 `https://testdrive.crm.dynamics.com/api/data/v9.0`URL을 복사합니다(예: ).

- **역할** 이름(필수): 사용자 지정 Dynamics 365 테스트 드라이브에 정의한 보안 역할 이름을 제공합니다. 이는 테스트 드라이브(예: 테스트 드라이브 역할)동안 사용자에게 할당됩니다.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>로직 앱 테스트 드라이브를 위한 기술 구성

모든 사용자 지정 제품은 다양한 복잡한 솔루션 아키텍처를 포함하는 이러한 유형의 테스트 드라이브 배포 템플릿을 사용해야 합니다. 로직 앱 테스트 드라이브 설정에 대한 자세한 내용은 GitHub의 [운영](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여를](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) 참조하십시오.

- **지역(필수,** 단일 선택 드롭다운 목록): 현재 테스트 드라이브를 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. Logic 앱의 리소스는 선택한 지역에 배포됩니다. Logic App에 특정 지역에 저장된 사용자 지정 리소스가 있는 경우 해당 지역이 여기에서 선택되어 있는지 확인합니다. 이 작업을 수행하는 가장 좋은 방법은 포털의 Azure 구독에 Logic 앱을 로컬로 완전히 배포하고 이 옵션을 선택하기 전에 제대로 작동하는지 확인하는 것입니다.

- **최대 동시 테스트** 드라이브(필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정합니다. 이러한 테스트 드라이브는 이미 배포되어 있으므로 고객은 배포를 기다리지 않고도 즉시 액세스할 수 있습니다.

- **테스트 드라이브** 기간(필수): 테스트 드라이브가 활성 상태로 유지되는 시간을 # 시간으로 입력합니다. 이 기간이 끝나면 테스트 드라이브가 자동으로 종료됩니다.

- **Azure 리소스 그룹** 이름(필수): Logic App 테스트 드라이브가 저장된 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 이름을 입력합니다.

- **Azure 논리 앱** 이름(필수): 사용자에게 테스트 드라이브를 할당하는 논리 앱의 이름을 입력합니다. 이 논리 응용 프로그램은 위의 Azure 리소스 그룹에 저장 해야 합니다.

- **프로비저닝 해제 논리 앱** 이름(필수): 고객이 완료되면 테스트 드라이브를 프로비저닝해제하는 Logic 앱의 이름을 입력합니다. 이 논리 응용 프로그램은 위의 Azure 리소스 그룹에 저장 해야 합니다.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 테스트 드라이브에 기술 구성이 필요하지 않습니다.

대화형 Power BI 시각적 개체를 보여 주려는 제품은 임베디드 링크를 사용하여 사용자 지정 대시보드를 테스트 드라이브로 공유할 수 있으며 추가 기술 구성이 필요하지 않습니다. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱 설정에 대해 자세히 알아보세요.

### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

대신 테스트 드라이브를 배포하려면 별도의 고유한 Azure 구독을 만들고 제공하십시오. (Power BI 테스트 드라이브는 필요하지 않습니다).

- **Azure 구독 ID(Azure** 리소스 관리자 및 논리 앱에 필요): 구독 ID를 입력하여 리소스 사용량 보고 및 청구에 대한 Azure 계정 서비스에 대한 액세스 권한을 부여합니다. 아직 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure 포털에](https://portal.azure.com/) 로그인하고 왼쪽 메뉴의 구독 탭으로 이동하여 Azure 구독 **ID를** 찾을 수 있습니다. 탭을 선택하면 구독 ID가 표시됩니다(예: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD 테넌트** ID(필수): Azure Active Directory(AD) [테넌트 ID를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)입력합니다. 이 ID를 찾으려면 [Azure 포털에](https://portal.azure.com/)로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 **속성선택을** 선택한 다음 나열된 **디렉터리 ID** 번호(예: 50c464d3-4930-493c-963c-1e951d15360e)를 찾습니다. 도메인 이름 URL을 사용하여 조직의 테넌트 ID를 [https://www.whatismytenantid.com](https://www.whatismytenantid.com)조회할 수도 있습니다.

- **Azure AD 테넌트 이름(동적** 365에 필요): Azure Active Directory(AD) 이름을 입력합니다. 이 이름을 찾으려면 오른쪽 상단 모서리에 있는 [Azure Portal에](https://portal.azure.com/)로그인하면 테넌트 이름이 계정 이름 아래에 나열됩니다.

- **Azure AD 앱** ID(필수): Azure Active Directory(AD) [응용 프로그램 ID를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)입력합니다. 이 ID를 찾으려면 [Azure 포털에](https://portal.azure.com/)로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **앱 등록을**선택한 다음 나열된 **응용 프로그램 ID** 번호(예: 50c464d3-4930-493c-963c-1e951d15360e)를 찾습니다.

- **Azure AD 앱 클라이언트** 보안(필수): Azure AD 응용 프로그램 [클라이언트 보안](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)을 입력합니다. 이 값을 찾으려면 Azure [포털에](https://portal.azure.com/)로그인합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택하고 **앱 등록을**선택한 다음 테스트 드라이브 앱을 선택합니다. 그런 다음 **인증서 및 비밀을**선택하고 새 클라이언트 **비밀을**선택하고 설명을 입력하고 **만료**되지 **않는 것을** 선택한 다음 **추가를**선택합니다. 값을 복사해야 합니다. (이 작업을 수행 하기 전에 페이지에서 멀리 이동 하지 마십시오, 또는 다른 값에 대 한 액세스 권한이 없습니다.)

다음 섹션으로 이동하기 전에 **저장해야** 합니다!

### <a name="test-drive-listings-optional"></a>테스트 드라이브 목록(선택 사항)

**테스트 드라이브** 탭아래에 있는 테스트 **드라이브 목록** 옵션에는 테스트 드라이브를 사용할 수 있는 언어(및 시장)가 표시되며, 현재 영어(미국)가 사용할 수 있는 유일한 위치입니다. 또한 이 페이지에는 언어별 목록의 상태와 추가된 날짜/시간이 표시됩니다. 각 언어/시장에 대한 테스트 드라이브 세부 정보(설명, 사용자 설명, 비디오 등)를 정의해야 합니다.

- **설명(필수):** 테스트 드라이브, 시연할 목표, 사용자가 실험할 목표, 탐색할 기능 및 사용자가 오퍼를 획득할지 여부를 결정하는 데 도움이 되는 관련 정보를 설명합니다. 이 필드에는 최대 3,000자의 텍스트를 입력할 수 있습니다. 

- **액세스 정보(Azure** 리소스 관리자 및 논리 테스트 드라이브에 필요): 이 테스트 드라이브에 액세스하고 사용하기 위해 고객이 알아야 할 사항을 설명합니다. 오퍼를 사용하기 위한 시나리오와 고객이 테스트 드라이브 전체에서 기능에 액세스하기 위해 알아야 할 사항을 정확히 알아보십시오. 이 필드에는 최대 10,000자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서(필수):** 테스트 드라이브 환경을 심층분석해 보십시오. 사용자 설명서는 고객이 테스트 드라이브를 경험하면서 얻을 수 있는 것을 정확히 다루어야 하며, 고객이 가질 수 있는 질문에 대한 참조 역할을 해야 합니다. 파일은 PDF 형식으로 되어 있어야 하며 업로드 후 최대 255자(최대 255자)의 이름을 지정해야 합니다.

- **동영상 추가(선택** 사항): 동영상은 YouTube 또는 Vimeo에 업로드하고 여기에 링크 및 미리보기 이미지(533 x 324 픽셀)로 참조할 수 있으므로 고객이 제품의 기능을 성공적으로 사용하고 혜택을 강조하는 시나리오를 이해하는 방법을 포함하여 테스트 드라이브를 더 잘 이해할 수 있도록 정보를 볼 수 있습니다.
  - **이름(필수)**
  - **URL(유튜브 또는 비메오만 해당)** (필수)
  - **썸네일 (533 x 324px)**: 이미지 파일은 PNG 형식이어야 합니다.

이 필드를 완료한 후 **저장을** 선택합니다.
