---
title: Azure Lab Services에 대 한 가속화 되는 랩 계정 설정 가이드
description: 이 가이드를 통해 관리자는 학교 내에서 사용할 랩 계정을 신속 하 게 설정할 수 있습니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879584"
---
# <a name="lab-account-setup-guide"></a>랩 계정 설정 가이드

첫 번째 단계로 관리자는 Azure 구독 내에서 랩 계정을 설정 해야 합니다. 랩 계정은 교실 랩의 컨테이너 이며 설정 하는 데 몇 분 밖에 걸리지 않습니다.

## <a name="understand-your-schools-lab-account-requirements"></a>학교 랩 계정 요구 사항 이해

학교 요구 사항에 따라 랩 계정을 구성 하는 방법을 이해 하려면 다음 질문을 고려해 야 합니다.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Azure 구독에 대 한 액세스 권한이 있나요?

랩 계정을 만들려면 학교에 대해 구성 된 Azure 구독에 대 한 액세스 권한이 필요 합니다. 학교에 하나 이상의 구독이 있을 수 있습니다. 구독을 사용 하 여 랩 계정을 비롯 한 모든 Azure 리소스 및 서비스에 대 한 청구 및 보안을 관리 합니다.

### <a name="how-many-lab-accounts-need-to-be-created"></a>생성 해야 하는 랩 계정은 몇 개입니까?

신속 하 게 시작 하려면 단일 랩 계정을 만든 다음 나중에 필요에 따라 추가 랩 계정을 만듭니다. 예를 들어 부서 마다 랩 계정이 하나만 있을 수 있습니다.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>랩 계정의 소유자 및 기여자는 누구 인가요?

관리자는 일반적으로 랩 계정의 소유자 및 기여자입니다. 랩 계정 내에 포함 된 모든 랩에 적용 되는 정책을 관리 하는 일을 담당 합니다. 랩 계정을 만드는 사람은 자동으로 소유자입니다. 일반적으로 구독과 연결 된 Azure Active Directory (Azure AD) 테 넌 트에서 추가 소유자 및 참가자를 추가할 수 있습니다. 이는 랩 계정 수준에서 소유자 또는 참가자 역할을 할당 하 여 랩 계정을 관리 하는 데 도움이 될 수 있습니다.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>랩을 만들고 관리할 수 있는 사용자는 누구 인가요?

관리자와 교직원 구성원이 랩을 만들고 관리 하도록 선택할 수 있습니다. 이러한 사용자 (일반적으로 구독과 연결 된 Azure AD 테 넌 트)는 랩 계정 내의 랩 작성자 역할에 할당 됩니다.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>랩 작성자가 랩에서 공유할 수 있는 이미지를 저장 하는 기능을 제공 하 시겠습니까?

공유 이미지 갤러리는 이미지를 저장 하 고 공유 하는 데 사용할 수 있는 리포지토리입니다. 동일한 이미지를 필요로 하는 클래스가 여러 개 있는 경우 랩 작성자는 이미지를 한 번 만들고 실습에서 공유할 수 있습니다. 그러나 시작 하려면 나중에 언제 든 지 추가할 수 있으므로 공유 이미지 갤러리가 반드시 필요한 것은 아닙니다.

이 질문에 "예" 라고 대답 한 경우 공유 이미지 갤러리를 만들거나 랩 계정에 연결 해야 합니다. "알 수 없음"에 답변 한 경우 나중에이 결정을 연기할 수 있습니다.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>교실 labs에서 사용 하는 Azure Marketplace 이미지는 무엇 인가요?

Azure Marketplace는 랩 작성자가 랩을 만들기 위해 이미지를 사용할 수 있도록 설정할 수 있는 수백 개의 이미지를 제공 합니다. 일부 이미지에는 랩에 이미 필요한 모든 항목이 포함 될 수 있습니다. 다른 경우에는 이미지를 시작 지점으로 사용 하 고, 랩 작성자는 추가 응용 프로그램 또는 도구를 설치 하 여이를 사용자 지정할 수 있습니다.

사용 해야 하는 이미지를 모르는 경우 나중에 다시이를 사용 하도록 설정할 수 있습니다. 또한 사용할 수 있는 이미지를 확인 하는 가장 좋은 방법은 먼저 랩 계정을 만드는 것입니다. 그러면 사용 가능한 이미지와 해당 내용 목록을 검토할 수 있도록 액세스 권한이 제공 됩니다.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>랩의 가상 컴퓨터에 다른 Azure 또는 온-프레미스 리소스에 대 한 액세스 권한이 있어야 하나요?

랩 계정을 설정 하는 경우 가상 네트워크를 피어 링 하는 옵션도 있습니다. 필요한 지 여부를 결정 하려면 다음 질문을 고려 하십시오.

- **라이선스 서버에 대 한 액세스를 제공 해야 하나요?**
  
   Azure Marketplace 이미지를 사용 하려는 경우 운영 체제 라이선스의 비용은 lab services 가격 책정에 제공 됩니다. 따라서 운영 체제 자체에 대 한 라이선스를 제공할 필요가 없습니다. 그러나 설치 된 추가 소프트웨어 및 응용 프로그램의 경우 적절 한 라이선스를 제공 해야 합니다.

- **랩 Vm이 파일 공유 또는 데이터베이스와 같은 다른 온-프레미스 리소스에 액세스 해야 하나요?**

   일반적으로 사이트 간 가상 네트워크 게이트웨이를 사용 하 여 온-프레미스 리소스에 대 한 액세스를 제공 하는 가상 네트워크를 만듭니다. 가상 네트워크를 구성 하지 않은 경우이에 대 한 추가 시간을 투자 해야 합니다.

- **랩 Vm에서 가상 네트워크 내에 있는 다른 Azure 리소스에 액세스 해야 하나요?**

   가상 네트워크 내에서 보안이 유지 *되지* 않는 Azure 리소스에 액세스 해야 하는 경우 피어 링을 수행 하지 않고 공용 인터넷을 통해 이러한 리소스에 액세스할 수 있습니다.

하나 이상의 질문에 "예"로 대답 한 경우 랩 계정을 가상 네트워크에 피어 링 해야 합니다. "알 수 없음"에 답변 하면 나중에이 결정을 연기할 수 있습니다. 랩 계정을 만든 후 항상 가상 네트워크를 피어 링 하도록 선택할 수 있습니다.

## <a name="set-up-your-lab-account"></a>랩 계정 설정

랩 계정에 대 한 요구 사항을 이해 하면 설정할 준비가 된 것입니다.

1. **랩 계정을 만듭니다.** 지침은 [랩 계정 만들기](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) 에 대 한 자습서를 참조 하세요.

   랩 계정을 만들 때 관련 된 Azure 리소스를 숙지 하는 것이 도움이 될 수 있습니다. 자세한 내용은 다음 아티클을 참조하세요.

   - [구독](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [리소스 그룹](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [랩 계정](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [교실 랩](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [지역 및 위치 선택](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [리소스에 대 한 명명 지침](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **랩 작성자 역할에 사용자를 추가 합니다.** 지침은 [랩 작성자 역할에 사용자 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)를 참조 하세요.

   또한 랩 계정 및 랩을 관리 하는 사용자에 게 할당할 수 있는 여러 역할에 대 한 자세한 내용은 [id 관리 가이드](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)를 참조 하세요.

3. **피어 가상 네트워크에 연결 합니다.** 자세한 내용은 [랩의 네트워크를 피어 가상 네트워크에 연결](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)을 참조 하세요.

   [Lab vm 주소 범위를 구성 하는](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)방법에 대 한 지침을 참조 해야 할 수도 있습니다.

4. **이미지를 사용 하도록 설정 하 고 검토 합니다.** 지침은 [랩 작성자를 위한 Azure Marketplace 이미지 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)을 참조 하세요.

   각 Azure Marketplace 이미지의 내용을 검토 하려면 이미지 이름을 선택 합니다. 예를 들어 다음 스크린샷은 Ubuntu Data Science VM 이미지에 대 한 세부 정보를 보여줍니다.

   ![Azure Marketplace 이미지 검토 스크린샷](../media/setup-guide/review-marketplace-images.png)

   랩 계정에 연결 된 공유 이미지 갤러리가 있고 랩 작성자가 사용자 지정 이미지를 공유할 수 있도록 설정 하려는 경우 다음 스크린샷에 표시 된 것과 비슷한 단계를 완료 합니다.

   ![공유 이미지 갤러리에서 사용자 지정 이미지를 사용 하도록 설정 하는 스크린샷](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>다음 단계

- [랩 계정 관리](how-to-manage-lab-accounts.md)

- [교실 랩 설치 가이드](setup-guide.md)
