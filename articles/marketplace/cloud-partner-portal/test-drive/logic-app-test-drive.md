---
title: 논리 앱 Test Drive | Microsoft Docs
description: Azure만이 아닌 기타 리소스나 Dynamic AX/CRM 인스턴스와 연결되는 Test Drive를 빌드하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: d784941c43da13a2c1bd120599aa02fe14e5a5b4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457268"
---
<a name="logic-app-test-drive"></a>논리 앱 Test Drive
====================

이 문서에서는 AppSource에 제품을 등록했으며 Azure만이 아닌 기타 리소스나 Dynamic AX/CRM 인스턴스와 연결되는 Test Drive를 빌드하려는 게시자를 위한 정보를 설명합니다.

<a name="how-to-build-a-logic-app-test-drive"></a>논리 앱 Test Drive 빌드 방법
-----------------------------------

논리 앱 Test Drive에 대한 Test Drive 문서는 현재 [운영](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 관계](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) 관련 GitHub에서 계속 제공되고 있으므로 자세한 내용을 확인해 보시기 바랍니다.

<a name="how-to-publish-a-test-drive"></a>시험 사용 게시 방법
---------------------------

이 섹션에서는 Test Drive를 빌드한 후에 올바르게 게시하기 위해 작성해야 하는 각 필드를 안내합니다.

![Test Drive 기능을 사용하도록 설정](./media/azure-resource-manager-test-drive/howtopub1.png)

첫 번째로 설정해야 하는 가장 중요한 필드는 모든 필수 필드가 작성할 수 있도록 표시되는 양식을 테스트할지 여부를 선택하는 필드입니다. **아니요**를 선택하면 양식이 사용하지 않도록 설정되며, Test Drive를 사용하지 않도록 설정한 상태로 제품을 게시하면 프로덕션 환경에서 Test Drive가 제거됩니다.

*참고*: 사용자가 현재 사용 중인 Test Drive는 세션이 만료될 때까지 계속 실행됩니다.

### <a name="details"></a>세부 정보

다음 섹션에서는 시험 사용 제품에 대한 세부 정보를 작성합니다.

![Test Drive 세부 정보](./media/azure-resource-manager-test-drive/howtopub2.png)

**설명 -** *[필수 필드]* Test Drive의 내용에 대한 기본 설명을 작성합니다. 고객은 이 설명을 통해 제품과 관련하여 Test Drive에서 진행되는 시나리오를 확인할 수 있습니다. 

**사용자 매뉴얼 -** *[필수 필드]* Test Drive 환경의 심층 연습입니다. 고객은 이 매뉴얼을 열어 Test Drive 전반에 걸쳐 필요한 과정을 정확하게 진행할 수 있습니다. 따라서 쉽게 이해하고 진행할 수 있는 내용을 포함해야 합니다. 매뉴얼 파일은 .pdf 형식이어야 합니다.

**Test Drive 데모 비디오 -** \[권장 필드\] 사용자 매뉴얼과 마찬가지로 Test Drive 환경의 비디오 자습서를 포함하는 것이 가장 좋습니다. 고객은 Test Drive를 시작하기 전에나 진행하는 중에 이 비디오를 시청하고 Test Drive 전반에 걸쳐 필요한 과정을 정확하게 진행할 수 있습니다. 따라서 쉽게 이해하고 진행할 수 있는 내용을 포함해야 합니다.

- **이름** - 비디오의 제목입니다.
- **링크** - YouTube 또는 Vimeo의 포함 URL이어야 합니다. 포함 URL을 가져오는 방법의 예제는 다음과 같습니다.
- **썸네일** - 고품질 이미지(533x324픽셀)여야 합니다. 여기서 일부 시험 사용 경험의 스크린샷을 만드는 것이 좋습니다.

아래 그림에서 Test Drive 환경 진행 중에 고객에게 이러한 필드가 표시되는 방식을 확인할 수 있습니다.

![Test Drive 필드의 표시 방식](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>기술 정보 구성

다음 섹션에서는 Test Drive 논리 앱을 구성하고 Test Drive 인스턴스의 구체적인 작동 방식과 관련한 필드를 작성합니다.

![Test Drive 기술 정보 구성](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **지역** - *[필수 필드]* Test Drive 논리 앱 리소스가 배포된 지역을 선택합니다.

    *참고:* 논리 앱이 특정 지역에 저장된 사용자 지정 리소스를 포함하는 경우 여기서 해당 지역을 선택해야 합니다. 이 필드에 지역을 입력하기 전에 **포털에서 Azure 구독에 논리 앱을 로컬로 완전히 배포한 다음 논리 앱이 작동하는지 확인**하는 방식이 가장 효율적입니다.

- **최대 동시 Test Drive 수** - *[필수 필드]* 선택한 지역에 이미 배포되어 있으며 액세스 대기 중인 Test Drive 인스턴스의 수입니다. 고객은 배포를 기다리지 않고 이 시험 사용에 즉시 액세스할 수 있습니다.

    *참고:* 학생 N명이 모두 Test Drive를 진행하도록 할 웨비나/수업을 진행하고 있다면 핫 인스턴스 N개를 게시한 다음, 수업이 끝난 후 원래 핫 인스턴스 수로 다시 게시하는 것이 좋습니다.

- **Test Drive 기간(시간) -** *[필수 필드]* Test Drive를 활성 상태로 유지할 기간(\#시간 단위)입니다. 이 기간이 끝나면 Test Drive는 자동으로 종료됩니다.

- **Azure 리소스 그룹 이름 -** *[필수 필드]* 논리 앱 Test Drive가 저장되는 리소스 그룹 이름을 입력합니다.

- **논리 앱 이름 할당 -** *[필수 필드]* 고객이 Test Drive를 가져오기 전에 Test Drive에서 사용자를 할당하는 데 사용한 논리 앱의 이름을 입력합니다. 위의 필드에 지정한 리소스 그룹에 이 파일을 저장해야 합니다.

- **프로비전 해제 논리 앱 이름 -** *[필수 필드]* Test Drive에서 생성된 모든 리소스의 프로비전을 해제할 논리 앱 이름을 입력합니다. 위의 필드에 지정한 리소스 그룹에 이 파일을 저장해야 합니다.

- **액세스 정보 -** *[필수 필드]* 고객이 Test Drive를 가져오면 액세스 정보가 고객에게 표시됩니다. 이러한 지침은 Test Drive Resource Manager 템플릿의 유용한 출력 매개 변수를 공유하기 위한 것입니다. 출력 매개 변수를 포함하려면 **{{outputname}}** 과 같이 이중 중괄호를 사용합니다. 그러면 매개 변수가 해당 위치에 올바르게 삽입됩니다. 프런트 엔드에서 렌더링될 수 있도록 여기서는 HTML 문자열 서식을 사용하는 것이 좋습니다.

### <a name="test-drive-deployment-subscription-details"></a>시험 사용 배포 구독 세부 정보

마지막 섹션에서는 Azure 구독과 Azure AD(Active Directory)를 연결하여 Test Drive를 자동으로 배포하기 위한 필드를 작성합니다.

![Test Drive 배포 구독 세부 정보](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure 구독 ID** *[필수 필드]* Azure 서비스 및 Azure Portal 액세스 권한을 부여합니다. 해당 구독에서 리소스 사용량이 보고되며 서비스 대금이 청구됩니다. **별도**의 Test Drive 전용 Azure 구독이 없으면 새로 만드세요. Azure Portal에 로그인하여 왼쪽 메뉴의 구독으로 이동하면 Azure 구독 ID를 확인할 수
있습니다(예: “a83645ac-1234-5ab6-6789-1h234g764ghty”).

![Azure 구독](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD 테넌트 ID** *[필수 필드]* 테넌 트 ID가 이미 있는 경우 다음 그림과 같이 속성 -\> 디렉터리 ID에서 확인할 수 있습니다.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

ID가 없으면 Azure Active Directory에서 새 테넌트를 만듭니다.

![Azure Active Directory 속성 화면](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Azure Active Directory 테넌트](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD 앱 ID** *[필수 필드]* 다음 단계에서는 새 응용 프로그램을 만들고 등록합니다. 이 애플리케이션을 사용하여 Test Drive 인스턴스에서 작업을 수행합니다.

1. 새로 만든 디렉터리 또는 이미 있는 디렉터리로 이동하여 필터 창에서 Azure Active Directory를 선택합니다.
2. “앱 등록”을 검색한 다음, “추가”를 클릭합니다.
3. 애플리케이션 이름을 입력합니다.
4. 유형으로 “웹앱/API”를 선택합니다.
5. 로그온 URL에는 아무 값이나 입력합니다. 해당 필드는 사용되지 않습니다.
6. 만들기를 클릭합니다.
7. 애플리케이션을 만든 후 속성 -\> 애플리케이션을 다중 테넌트로 설정으로 이동하여 저장을 클릭합니다.

저장을 클릭합니다. 마지막 단계에서는 등록된 이 앱의 애플리케이션 ID를 가져와 아래 그림에 표시된 시험 사용 필드에 붙여넣습니다.

![Azure Active Directory 애플리케이션 ID](./media/azure-resource-manager-test-drive/subdetails7.png)

애플리케이션을 사용하여 구독을 배포할 것이므로 구독의 참가자로 애플리케이션을 추가해야 합니다. 이 작업을 수행하는 지침은 다음과 같습니다.

1. 구독 블레이드로 이동한 다음, 시험 사용 전용으로 사용할 적절한 구독을 선택합니다.
1. **액세스 제어(IAM)** 를 클릭합니다.
1. **역할 할당** 탭을 클릭합니다.  ![Azure Active Directory, 새 액세스 제어 보안 주체 추가](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. **역할 할당 추가**를 클릭합니다.
1. 역할을 **기여자**로 설정합니다.
1. Azure AD 애플리케이션의 이름을 입력하고 역할을 할당할 애플리케이션을 선택합니다.
    ![Azure Active Directory 권한](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. **저장**을 클릭합니다.

**Azure AD 앱 키 -** *[필수 필드]* 마지막 필드에서는 인증 키를 생성합니다. 키 아래에 키 설명을 추가하고 기간을 만료 기한 제한 없음으로 설정한 후에 저장을 선택합니다. 만료된 키를 사용해서는 **안 됩니다**. 만료된 키를 사용하면 프로덕션 환경에서 시험 사용이 중단됩니다. 이 값을 복사한 다음 필요한 Test Drive 필드에 붙여넣습니다.

![Azure Active Directory 키 섹션](./media/azure-resource-manager-test-drive/subdetails8.png)

<a name="next-steps"></a>다음 단계
----------

이제 모든 시험 사용 필드를 작성했으므로 제품을 **다시 게시**합니다. Test Drive가 인증 프로세스를 통과하면 제품 **미리 보기**에서 고객 환경을 포괄적으로 테스트해야 합니다. UI에서 Test Drive를 시작하여 Test Drive가 올바르게 완전히 배포되는지 확인합니다.

Test Drive는 고객용으로 프로비전되므로 어떤 부분도 삭제해서는 안 됩니다. 고객이 Test Drive 사용을 완료하고 나면 Test Drive 서비스에서 이러한 리소스 그룹을 자동으로 정리하기 때문입니다.

미리 보기 제품에 문제가 없는 것으로 확인되면 제품을 **라이브로 제공**할 수 있습니다. 제품을 게시하고 나면 전체 종단 간 환경을 확인하기 위해 Microsoft에서 최종 검토 프로세스를 진행합니다. 제품이 거부되는 경우에는 엔지니어링 연락처에게 수정해야 하는 사항을 설명하는 제품 관련 알림이 전송됩니다.

추가 문의 사항이 있거나 문제 해결 관련 자문을 받거나 Test Drive의 효율성을 개선하려는 경우 [FAQ, 문제 해결 및 모범 사례](./marketing-and-best-practices.md)를 참조하세요.
