---
title: Azure Lab Services의 클래스룸 랩 - FAQ | Microsoft Docs
description: 이 문서는 Azure Lab Services의 클래스룸 랩에 대한 질문과 대답(FAQ)을 제공합니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ca35d70bc1106e46df4e3c68889b03679fd54b86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443301"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure Lab Services의 클래스룸 랩 - 질문과 대답(FAQ)
Azure Lab Services의 클래스룸 랩에 관해 가장 자주 묻는 질문에 대한 답변을 확인합니다. 

## <a name="quotas"></a>할당량

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>사용자당 할당량, 주당 할당량, 랩의 전체 기간당 할당량 중 무엇인가요? 
랩에 설정하는 할당량은 랩의 전체 기간 동안 각 학생에게 할당됩니다. 그리고 [VM의 예약된 실행 시간](how-to-create-schedules.md)은 사용자에게 할당된 할당량에 합산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다.  할당량에 대한 자세한 내용은 [사용자에 대한 할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)을 참조하세요.

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>교육자가 학생 VM을 켜면 학생 할당량에 영향이 있나요? 
아니요. 그렇지 않습니다. 교육자가 학생 VM을 켜도 학생에게 할당된 할당량에 영향을 주지 않습니다. 

## <a name="schedules"></a>일정

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>일정이 설정되면 랩의 모든 VM이 자동으로 시작되나요? 
아니요. 모든 VM이 그런 것은 아닙니다. 일정에 따라 사용자에게 할당되는 VM만 그렇습니다. 사용자에게 할당되지 않은 VM이 자동으로 시작되지는 않습니다. 기본적으로 그렇습니다. 

## <a name="lab-accounts"></a>랩 계정

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>주소 범위를 사용할 수 없는 경우에 랩을 만들 수 없는 이유는 무엇인가요? 
클래스룸 랩은 랩 생성자가 Azure Portal에서 랩 계정을 만들 때 지정한 IP 주소 범위 내에서 랩 VM을 만들 수 있습니다. 주소 범위가 지정된 후에 생성된 각 랩에는 랩 VM의 512개 IP 주소가 할당됩니다. 랩 계정의 주소 범위는 랩 계정에서 만들려는 랩을 모두 수용할 수 있을 만큼 커야 합니다. 

예를 들어 /19 - 10.0.0.0/19 블록이 있는 경우 이 주소 범위는 8192개 IP 주소 및 16개 랩(8192/512 = 16개 랩)을 수용합니다. 이 경우 17번째 랩을 만들면 오류가 발생합니다.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH를 통해 랩 가상 머신에 연결하려면 조직의 방화벽 설정에서 어떤 포트 범위를 열어야 하나요?

열어야 할 포트는 49152–65535입니다. 클래스룸 랩은 부하 분산 장치 뒤에 있습니다. 각 랩에는 단일 공용 IP 주소가 있으며 랩의 각 가상 머신에는 고유한 포트가 있습니다. 

Azure Portal에서 랩 홈페이지의 **가상 머신 풀** 탭에서 각 가상 머신의 개인 IP 주소를 볼 수도 있습니다. 랩을 다시 게시하는 경우 랩의 공용 IP 주소는 변경되지 않지만 랩에서 각 가상 머신의 개인 IP 및 포트 번호는 변경될 수 있습니다. 자세한 정보는 다음 문서에서 확인할 수 있습니다. [Azure Lab Services에 대한 방화벽 설정](how-to-configure-firewall-settings.md)

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH를 통해 랩 가상 머신에 연결하려면 조직의 방화벽 설정에서 어떤 공용 IP 주소 범위를 열어야 하나요?
Azure의 데이터 센터에 대한 공용 IP 주소 범위를 제공하는 [Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. 랩 계정이 있는 지역에 대한 IP 주소를 열 수 있습니다.

## <a name="virtual-machine-images"></a>가상 머신 이미지

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>랩 작성자가 새 랩을 만들 때 가상 머신 이미지 드롭다운에서 추가 이미지 옵션을 사용하도록 설정할 수 없는 이유는 무엇인가요?

관리자가 랩 계정에 랩 작성자로 추가한 사용자에게는 랩을 만들 수 있는 권한이 부여됩니다. 하지만 사용하도록 설정된 가상 머신 이미지의 목록을 포함하여 랩 계정 내의 설정을 편집할 수 있는 권한은 없습니다. 추가 이미지를 사용하도록 설정하려면 랩 계정 관리자에게 문의하거나 관리자에게 랩 계정에 기여자 역할로 추가해 달라고 요청하세요. 기여자 역할은 랩 계정에서 가상 머신 이미지 목록을 편집할 수 있는 권한을 부여합니다.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>가상 머신에 추가 디스크를 연결할 수 있나요?
아니요. 클래스룸 랩에서 VM에 추가 디스크를 연결하는 것은 불가능합니다. 

## <a name="users"></a>사용자

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>클래스룸 랩에 추가할 수 있는 사용자는 몇 명인가요?
클래스룸 랩에 최대 400명의 사용자를 추가할 수 있습니다. 

## <a name="blog-post"></a>블로그 게시물
[Azure Lab Services 블로그](https://aka.ms/azlabs-blog)를 구독합니다.

## <a name="update-notifications"></a>업데이트 알림
Lab Services의 새로운 기능에 대한 최신 정보를 받아 보려면 [Lab Services 업데이트](https://azure.microsoft.com/updates/?product=lab-services)를 구독합니다.

## <a name="general"></a>일반
### <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
질문이 여기에 나열되어 있지 않은 경우 답변을 찾는 데 도움을 드릴 수 있도록 알려주세요.

- 이 FAQ의 끝에 질문을 게시합니다. 
- 더 광범위한 대상에 도달하기 위해 [Azure Lab Services - 기술 커뮤니티 포럼](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices)에 질문을 게시하세요. 
- 기능 요청의 경우 요청 내용과 아이디어를 [Azure Lab Services - 사용자 의견](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)으로 제출해 주세요.

