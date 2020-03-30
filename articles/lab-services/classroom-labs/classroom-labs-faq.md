---
title: Azure 랩 서비스의 강의실 랩 — FAQ | 마이크로 소프트 문서
description: 이 문서에서는 Azure Lab 서비스의 강의실 랩에 대해 자주 묻는 FAQ(FAQ)에 대한 답변을 제공합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443502"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 랩 서비스의 강의실 랩 — 자주 묻는 질문(FAQ)
Azure Lab 서비스의 강의실 랩에 대한 가장 일반적인 몇 가지 질문에 대한 답변을 얻을 수 있습니다. 

## <a name="quotas"></a>할당량

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>사용자당 할당량또는 주당 또는 랩의 전체 기간당 할당량입니까? 
랩에 대해 설정한 할당량은 랩의 전체 기간 동안 각 학생에 대해 설정됩니다. 또한 [VM의 예약된 실행 시간은](how-to-create-schedules.md) 사용자에게 할당된 할당량에 포함되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다.  할당량에 대한 자세한 내용은 [사용자에 대한 할당량 설정을](how-to-configure-student-usage.md#set-quotas-for-users)참조하십시오.

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>교수가 학생 VM을 켜면 학생 할당량에 영향을 미칩니까? 
아니요. 그렇지 않습니다. 교수가 학생 VM을 켜면 학생에게 할당된 할당량에는 영향을 주지 않습니다. 

## <a name="schedules"></a>일정

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>일정이 설정되면 랩의 모든 VM이 자동으로 시작합니까? 
아니요. 모든 VM이 아닙니다. 일정에 따라 사용자에게 할당된 VM만 사용자에게 할당되지 않은 VM은 자동으로 시작되지 않습니다. 그것은 의도적으로 입니다. 

## <a name="lab-accounts"></a>랩 계정

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>주소 범위를 사용할 수 없기 때문에 랩을 만들 수 없는 이유는 무엇입니까? 
클래스룸 랩은 Azure 포털에서 랩 계정을 만들 때 지정한 IP 주소 범위 내에서 랩 VM을 만들 수 있습니다. 주소 범위가 제공되면 랩 VM에 512IP 주소를 할당한 후 생성된 각 랩입니다. 랩 계정의 주소 범위는 랩 계정으로 만들려는 모든 랩을 수용할 수 있을 만큼 충분히 커야 합니다. 

예를 들어 /19 - 10.0.0.0/19 블록이 있는 경우 이 주소 범위는 8192IP 주소와 16개의 랩(8192/512 = 16개의 랩)을 수용합니다. 이 경우 랩 생성은 17번째 랩 생성에 실패합니다.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH를 통해 랩 가상 컴퓨터에 연결하려면 조직의 방화벽 설정에서 열어야 하는 포트 범위는 무엇입니까?

포트는 다음과 같습니다: 49152-65535. 교실 실험실은 로드 밸러울러 뒤에 있습니다. 각 랩에는 단일 공용 IP 주소가 있으며 랩의 각 가상 컴퓨터에는 고유한 포트가 있습니다. 

또한 Azure 포털의 랩용 홈 페이지의 **가상 시스템 풀** 탭에서 각 가상 시스템의 개인 IP 주소를 볼 수도 있습니다. 랩을 다시 게시하면 랩의 공용 IP 주소는 변경되지 않지만 랩의 각 가상 시스템의 개인 IP 및 포트 번호는 변경될 수 있습니다. 자세한 내용은 Azure Lab [서비스에 대한 방화벽 설정입니다.](how-to-configure-firewall-settings.md)

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH를 통해 랩 가상 컴퓨터에 연결하려면 조직의 방화벽 설정에서 어떤 공용 IP 주소 범위를 열어야 합니까?
[Azure의](https://www.microsoft.com/download/details.aspx?id=56519)데이터 센터에 대한 공용 IP 주소 범위를 제공하는 공용 클라우드 - Azure IP 범위 및 서비스 태그를 참조하십시오. 랩 계정이 있는 지역의 IP 주소를 열 수 있습니다.

## <a name="virtual-machine-images"></a>가상 머신 이미지

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>랩 작성자는 새 랩을 만들 때 가상 컴퓨터 이미지 드롭다운에서 추가 이미지 옵션을 사용할 수 없는 이유는 무엇입니까?

관리자가 랩 계정에 랩 작성자로 추가하면 랩을 만들 수 있는 권한이 부여됩니다. 그러나 사용 가능한 가상 시스템 이미지 목록을 포함하여 랩 계정 내의 설정을 편집할 수 있는 권한이 없습니다. 추가 이미지를 사용하려면 랩 계정 관리자에게 문의하여 작업을 수행하거나 관리자에게 랩 계정에 기여자 역할로 추가하도록 요청합니다. 기여자 역할은 랩 계정의 가상 컴퓨터 이미지 목록을 편집할 수 있는 권한을 부여합니다.

## <a name="users"></a>사용자

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>강의실 랩에 몇 명의 사용자가 있을 수 있습니까?
최대 400명의 사용자를 교실 랩에 추가할 수 있습니다. 

## <a name="blog-post"></a>블로그 게시물
[Azure 랩 서비스 블로그를](https://azure.microsoft.com/blog/tag/azure-lab-services/)구독합니다.

## <a name="update-notifications"></a>알림 업데이트
랩 [서비스 업데이트를](https://azure.microsoft.com/updates/?product=lab-services) 구독하여 랩 서비스의 새로운 기능에 대한 최신 정보를 받아보세요.

## <a name="general"></a>일반
### <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
질문이 여기에 나열되지 않은 경우 저희에게 알려주시면 답변을 찾을 수 있도록 도와드리겠습니다.

- 이 FAQ의 끝에 질문을 게시합니다. 
- 더 많은 청중에게 도달하려면 Azure [Lab 서비스 - 스택 오버플로 포럼에](https://stackoverflow.com/questions/tagged/azure-lab-services)질문을 게시합니다. 
- 기능 요청의 경우 [Azure Lab 서비스-사용자 음성](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)에 요청 및 아이디어를 제출합니다.

