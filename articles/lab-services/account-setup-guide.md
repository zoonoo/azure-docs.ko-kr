---
title: Azure Lab Services에 대 한 가속화 되는 랩 계정 설정 가이드
description: 이 가이드를 통해 관리자는 학교 내에서 사용할 랩 계정을 신속 하 게 설정할 수 있습니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659865"
---
# <a name="lab-account-setup-guide"></a>랩 계정 설정 가이드
Azure Lab Services 환경을 설정 하기 위해 관리자는 먼저 Azure 구독 내에서 **랩 계정을** 설정 해야 합니다. 랩 계정은 랩의 컨테이너 이며 설정 하는 데 몇 분 밖에 걸리지 않습니다.

이 가이드에는 세 가지 섹션이 포함 되어 있습니다.
-  첫 번째 섹션에서는 랩 계정을 설정 *하기 전에* 완료 해야 하는 필수 구성 요소에 대해 중점적으로 설명 합니다.
-  두 번째 섹션에서는 랩 계정 설정 계획에 대 한 지침을 제공 합니다.
-  세 번째 섹션에서는 랩 계정을 설정 하는 방법에 대 한 단계별 지침을 제공 합니다.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>랩 계정을 설정 하기 위한 필수 구성 요소
이 섹션에서는 랩 계정을 설정 하기 전에 완료 해야 하는 필수 구성 요소를 간략하게 설명 합니다.

### <a name="obtain-an-azure-subscription"></a>Azure 구독 가져오기
랩 계정을 만들려면 학교에 대해 설정 된 Azure 구독에 대 한 액세스 권한이 필요 합니다. 학교에 하나 이상의 구독이 있을 수 있습니다. 구독을 사용 하 여 랩 계정을 비롯 한 모든 Azure 리소스 및 서비스에 대 한 청구 및 보안을 관리 합니다.  Azure 구독은 일반적으로 IT 부서에서 관리 합니다.  자세한 내용은 다음 항목을 참조 하세요.
 - [관리자 가이드-구독](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>필요한 vm 및 VM 크기의 수를 예상 합니다.
가상 머신 (Vm)의 수와 학교에 필요한 [vm 크기](./administrator-guide.md#vm-sizing) 를 예측 해야 합니다.  Labs\images.를 구성 하는 방법에 대 한 지침은 다음 블로그 게시물을 참조 하세요.  이 블로그 게시물은 필요한 vm 수와 VM 크기를 결정 하는 데에도 도움이 됩니다.
- [물리적 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

또한 실습 구조를 구성 하는 방법에 대 한 추가 지침을 설명 하는이 문서를 참조 하세요.
- [관리자 가이드-랩](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>구독 VM 제한 및 지역 VM 용량 이해
랩의 vm 수와 VM 크기를 예상 하는 경우 다음을 수행 해야 합니다.

- Azure 구독의 용량 한도가 랩에서 사용 하려는 vm 및 VM 크기의 수를 허용 하는지 확인 합니다.

- 사용 가능한 VM 용량이 충분 한 지역 내에서 랩 계정을 만듭니다.

자세한 내용은 [VM 구독 제한 및 지역별 용량](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)블로그 게시물을 참조 하세요.

### <a name="decide-how-many-lab-accounts-to-create"></a>만들 랩 계정 수 결정

신속 하 게 시작 하려면 자체 리소스 그룹 내에서 단일 랩 계정을 만듭니다.  나중에 필요에 따라 추가 랩 계정 (및 리소스 그룹)을 만들 수 있습니다. 예를 들어 궁극적으로 비용을 명확 하 게 구분 하는 방법으로 학과 당 하나의 랩 계정 및 리소스 그룹이 있을 수 있습니다.  랩 계정, 리소스 그룹 및 분리 비용에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [관리자 가이드-리소스 그룹](./administrator-guide.md#resource-group)
- [관리자 가이드-랩 계정](./administrator-guide.md#lab-account) 
- [Azure Lab Services에 대 한 Cost management](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>랩 계정의 설정 계획

랩 계정의 설정을 계획 하려면 아래 질문을 고려해 야 합니다.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>랩 계정의 소유자 및 기여자는 누구 인가요?

   학교 IT 관리자는 일반적으로 랩 계정의 소유자 및 기여자입니다. 랩 계정 내에 포함 된 모든 랩에 적용 되는 정책을 관리 하는 일을 담당 합니다. 랩 계정을 만드는 사람은 자동으로 소유자입니다. 구독과 연결 된 AD (Azure Active Directory) 테 넌 트에서 소유자와 참가자를 더 추가할 수 있습니다. 랩 계정 소유자 및 참가자 역할에 대 한 자세한 내용은 다음을 참조 하세요.
   -  [관리자 가이드-Id 관리](./administrator-guide.md#manage-identity)

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   랩 사용자는 Azure Lab Services 내에서 테 넌 트에서 액세스할 수 있는 가상 컴퓨터의 단일 목록만 볼 수 있습니다.

### <a name="who-will-be-allowed-to-create-labs"></a>랩을 만들 수 있는 사용자는 누구 인가요?

   IT 및 교직원 구성원이 랩을 만들도록 선택할 수 있습니다. 사용자가 랩을 만들면 랩의 소유자로 자동으로 할당 됩니다.  랩을 만들려면 사용자 (일반적으로 구독과 연결 된 Azure AD 테 넌 트에서)를 랩 계정 내의 랩 작성자 역할에 할당 해야 합니다.  랩 작성자 역할에 대 한 자세한 내용은 다음을 참조 하세요.
   -  [관리자 가이드-id 관리](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>랩을 소유 하 고 관리할 수 있는 사용자는 누구 인가요?

   또한 랩을 만들 수 있는 기능을 제공 *하지 않고도* IT 및 교직원 구성원이 랩을 own\manage 하도록 선택할 수 있습니다.  이 경우 구독의 Azure AD 테 넌 트에 있는 사용자에 게는 기존 랩에 대 한 소유자 또는 참가자가 할당 됩니다.  랩의 소유자 및 참여자 역할에 대 한 자세한 내용은 다음을 참조 하세요.
   - [관리자 가이드-id 관리](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>실습에서 공유할 수 있는 이미지를 저장 하 시겠습니까?
공유 이미지 갤러리는 이미지를 저장 하 고 공유 하는 데 사용할 수 있는 리포지토리입니다. 동일한 이미지를 필요로 하는 클래스의 경우 랩 작성자는 이미지를 만든 다음 공유 이미지 갤러리로 내보낼 수 있습니다.  공유 이미지 갤러리에 이미지를 내보낸 후에는이를 사용 하 여 새 랩을 만들 수 있습니다.

또한 실제 환경에서 이미지를 만든 다음 공유 이미지 갤러리로 가져올 수 있습니다.  이 프로세스에 대 한 자세한 내용은 다음 블로그 게시물을 참조 하세요. 
- [사용자 지정 이미지를 공유 이미지 갤러리로 가져오기](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

공유 이미지 갤러리를 사용 해야 하는 경우 랩 계정에 공유 이미지 갤러리를 만들거나 연결 해야 합니다. 또한 언제 든 지 랩 계정에 연결할 수 있으므로 나중에이 결정을 연기할 수 있습니다.  공유 이미지 갤러리에 대 한 자세한 내용은 다음을 참조 하세요.
- [관리자 가이드-공유 이미지 갤러리](./administrator-guide.md#shared-image-gallery)
- [관리자 가이드-공유 이미지 갤러리 가격](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>실습에서 사용 하는 Azure Marketplace 이미지는 무엇 인가요?
Azure Marketplace는 랩 작성자가 랩을 만들기 위해 이미지를 사용할 수 있도록 설정할 수 있는 수백 개의 이미지를 제공 합니다. 일부 이미지에는 랩에 이미 필요한 모든 항목이 포함 될 수 있습니다. 다른 경우에는 이미지를 시작 지점으로 사용 하 고, 랩 작성자는 추가 응용 프로그램 또는 도구를 설치 하 여이를 사용자 지정할 수 있습니다.

필요한 이미지를 모르는 경우 나중에 다시 돌아와서 사용 하도록 설정할 수 있습니다. 또한 사용할 수 있는 이미지를 확인 하는 가장 좋은 방법은 먼저 랩 계정을 만드는 것입니다. 그러면 사용 가능한 이미지와 해당 내용 목록을 검토할 수 있는 액세스 권한이 제공 됩니다.  Marketplace 이미지에 대 한 자세한 내용은 다음을 참조 하세요.
- [랩 작성자에 사용할 수 있는 Marketplace 이미지 지정](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>랩의 Vm에 다른 Azure 또는 온-프레미스 리소스에 대 한 액세스 권한이 있어야 하나요?
랩 계정을 설정 하면 가상 네트워크 (VNet)를 사용 하 여 랩 계정을 피어 링 할 수도 있습니다.  VNet 및 랩 계정 모두 동일한 지역에 있어야 합니다.  VNet과 피어 링 해야 하는지 여부를 결정 하려면 다음 시나리오를 고려 하세요.

- **라이선스 서버에 대 한 액세스 권한**
  
   Azure Marketplace 이미지를 사용 하는 경우 운영 체제 라이선스의 비용은 랩 서비스 가격에 제공 됩니다. 그러나 운영 체제 자체에 대 한 라이선스를 제공할 필요는 없습니다. 그러나 설치 된 추가 소프트웨어 및 응용 프로그램의 경우 적절 한 라이선스를 제공 해야 합니다.  라이선스 서버에 액세스 하려면:
   - 온-프레미스 라이선스 서버에 연결 하도록 선택할 수 있습니다.  온-프레미스 라이선스 서버에 연결 하려면 추가 설정이 필요 합니다.
   - 설정 하는 데 더 빠른 다른 옵션은 Azure VM에서 호스트 하는 라이선스 서버를 만드는 것입니다.  Azure VM은 랩 계정에 피어 링 하는 가상 네트워크 내에 있습니다.

- **파일 공유 또는 데이터베이스와 같은 다른 온-프레미스 리소스에 대 한 액세스**

   일반적으로 사이트 간 가상 네트워크 게이트웨이를 사용 하 여 온-프레미스 리소스에 대 한 액세스를 제공 하는 VNet을 만듭니다. 이러한 유형의 환경을 설정 하는 데 시간이 추가로 소요 됩니다.

- **VNet 외부에 있는 다른 Azure 리소스에 대 한 액세스**

   VNet 내에서 보안이 유지 *되지* 않는 Azure 리소스에 액세스 해야 하는 경우 피어 링을 수행 하지 않고 공용 인터넷을 통해 이러한 리소스에 액세스할 수 있습니다.

가상 네트워크에 대 한 자세한 내용은 다음을 참조 하세요.
- [아키텍처 기본 사항-Virtual Network](./classroom-labs-fundamentals.md#virtual-network)
- [가상 네트워크에 연결 하는 방법](./how-to-connect-peer-virtual-network.md)
- [Azure Lab Services에서 공유 리소스를 사용 하 여 랩을 만드는 방법](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>랩 계정 설정

계획을 완료 한 후에는 랩 계정을 설정할 준비가 된 것입니다.  이러한 동일한 단계는 [팀을 사용 하 Azure Lab Services](./lab-services-within-teams-overview.md)랩을 설정 하는 데에도 적용 됩니다.

1. **랩 계정을 만듭니다.** 지침은 [랩 계정 만들기](./tutorial-setup-lab-account.md#create-a-lab-account) 에 대 한 자습서를 참조 하세요.
   
    이름 지정에 대 한 자세한 지침은 다음 문서를 참조 하세요.

   - [리소스에 대 한 명명 지침](./administrator-guide.md#naming)

2. **랩 작성자 역할에 사용자를 추가 합니다.** 지침은 [랩 작성자 역할에 사용자 추가](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)를 참조 하세요.


3. **피어 가상 네트워크에 연결 합니다.** 자세한 내용은 [랩의 네트워크를 피어 가상 네트워크에 연결](./how-to-connect-peer-virtual-network.md)을 참조 하세요.

   [Lab vm 주소 범위를 구성 하는](./how-to-configure-lab-accounts.md)방법에 대 한 지침을 참조 해야 할 수도 있습니다.

4. **이미지를 사용 하도록 설정 하 고 검토 합니다.** 지침은 [랩 작성자를 위한 Azure Marketplace 이미지 사용](./specify-marketplace-images.md)을 참조 하세요.

   각 Azure Marketplace 이미지의 내용을 검토 하려면 이미지 이름을 선택 합니다. 예를 들어 다음 스크린샷은 Ubuntu Data Science VM 이미지에 대 한 세부 정보를 보여줍니다.

   ![Azure Marketplace 이미지 검토 스크린샷](./media/setup-guide/review-marketplace-images.png)

   공유 이미지 갤러리가 랩 계정에 연결 되어 있는 경우 랩 작성자가 사용자 지정 이미지를 공유할 수 있도록 설정 하려면 다음 스크린샷에 표시 된 것과 비슷한 단계를 완료 합니다.

   ![공유 이미지 갤러리에서 사용자 지정 이미지를 사용 하도록 설정 하는 스크린샷](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>다음 단계

랩 환경을 설정 하는 일반적인 다음 단계:

- [랩 계정 관리](how-to-manage-lab-accounts.md)
- [랩 설정 가이드](setup-guide.md)