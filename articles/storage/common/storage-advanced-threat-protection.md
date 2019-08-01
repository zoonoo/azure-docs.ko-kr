---
title: Advanced Threat Protection for Azure Storage
description: 계정 작업에서 잘못된 부분을 검색하여 잠재적으로 해로운 계정 액세스 시도에 대해 알리도록 Azure Storage Advanced Threat Protection을 구성합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: bb2d5733704b0b31dc010cec2a90e99e1be07b56
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592022"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection for Azure Storage

Azure Storage를 위한 Advanced Threat Protection은 스토리지 계정에 대한 비정상적이며 잠재적으로 유해한 액세스 또는 악용 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 레이어를 사용하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결 수 있습니다. 

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다.  이러한 보안 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합 되며, 의심 스러운 활동 및 위협 조사 및 해결 방법에 대 한 권장 사항을 포함 하 여 구독 관리자에 게 전자 메일을 통해 전송 됩니다.

> [!NOTE]
> * Azure Storage에 대 한 Advanced Threat Protection은 현재 Blob 저장소에만 사용할 수 있습니다.
> * 무료 30 일 평가판을 비롯 한 가격 정보는 [Azure Security Center 가격 책정 페이지]( https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.
> * Azure storage 용 ATP 기능은 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

위협 감지를 위해 Blob 저장소에 대 한 읽기, 쓰기 및 삭제 요청의 진단 로그를 Azure Storage 수집에 대 한 고급 위협 방지 Advanced Threat Protection에서 경고를 조사 하려면 스토리지 분석 로깅을 사용 하 여 관련 저장소 작업을 볼 수 있습니다. 자세한 내용은 how to [configure 스토리지 분석 logging](storage-monitor-storage-account.md#configure-logging)항목을 참조 하세요.

## <a name="set-up-advanced-threat-protection"></a>Advanced Threat Protection 설정 

### <a name="using-the-portal"></a>포털 사용

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal을 시작합니다.

2. 보호할 Azure Storage 계정의 구성 페이지로 이동합니다. **설정** 페이지에서 **Advanced Threat Protection**을 선택합니다.

3. **Advanced Threat Protection** 구성 블레이드에서 다음을 수행합니다.
    * Advanced *Threat Protection*을 **켭니다**.
    * **저장**을 클릭하여 새로운 또는 업데이트된 Advanced Threat Protection 정책을 저장합니다. (이미지의 가격은 예를 들어 목적 으로만 사용 됩니다.)

![Azure Storage Advanced Threat Protection 켜기](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Azure Security Center 사용

Azure Security Center 표준 계층을 구독할 때 Advanced Threat Protection은 모든 저장소 계정에 자동으로 설정 됩니다. 다음과 같이 특정 구독에서 저장소 계정에 대 한 고급 위협 방지를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Security Center** 를 시작 합니다.
1. 주 메뉴에서 **가격 책정 & 설정**을 클릭 합니다.
1. 저장소 계정에 대해 위협 방지를 사용 하거나 사용 하지 않도록 설정할 구독을 클릭 합니다.

    ![구독 선택](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. **가격 책정 계층**을 클릭합니다.
1. **리소스 유형별 가격 책정 계층 선택** 섹션의 **저장소 계정** 행에서 **사용** 또는 **사용 안 함**을 클릭 합니다.

    ![Security Center에서 ATP 사용](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. **Save**을 클릭합니다.

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

Azure Resource Manager 템플릿을 사용 하 여 Advanced Threat Protection이 설정 된 Azure Storage 계정을 배포 합니다. 자세한 내용은 [Advanced Threat Protection을 사용 하는 저장소 계정](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)을 참조 하세요.

### <a name="using-azure-policy"></a>Azure Policy 사용

Azure Policy를 사용 하 여 특정 구독 또는 리소스 그룹의 저장소 계정에서 Advanced Threat Protection을 사용 하도록 설정 합니다.

1. Azure **정책 정의** 페이지를 시작 합니다.

1. **저장소 계정에 Advanced Threat Protection 배포** 정책을 검색 합니다.

     ![검색 정책](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Azure 구독 또는 리소스 그룹을 선택 합니다.

    ![구독 또는 그룹 선택](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 정책을 할당 합니다.

    ![정책 정의 페이지](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>REST API 사용
Rest API 명령을 사용 하 여 특정 저장소 계정에 대 한 Advanced Threat Protection 설정을 만들거나 업데이트 하거나 가져옵니다.

* [Advanced Threat Protection-만들기](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection-Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

다음 PowerShell cmdlet을 사용 합니다.

  * [Advanced Threat Protection 사용](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Advanced Threat Protection 가져오기](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Advanced Threat Protection 사용 안 함](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>보안 비정상 탐색

저장소 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

* 변칙의 특성
* 스토리지 계정 이름입니다.
* 이벤트 시간
* 저장소 유형
* 가능한 원인 
* 조사 단계
* 수정 단계


또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Azure Security Center의 [보안 경고 타일](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)에서 현재 보안 경고를 검토 및 관리할 수 있습니다. 특정 경고를 클릭하면 현재 위협을 조사하고 향후 위협을 해결하기 위한 추가 세부 정보 및 조치가 제공됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>보호 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 저장소 계정 악용에 의해 생성됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

### <a name="anomalous-access-pattern-alerts"></a>비정상적인 액세스 패턴 경고

* **비정상적인 위치에서 액세스**: 이 경고는 누군가가 비정상적인 지리적 위치에서 저장소 계정에 액세스 했을 때 트리거됩니다.
가능한 원인:
   * 공격자가 저장소 계정에 액세스 했습니다.
   * 합법적인 사용자가 새 위치에서 저장소 계정에 액세스 했습니다.
 
* **응용 프로그램**이상: 이 경고는 비정상적인 응용 프로그램이이 저장소 계정에 액세스 했음을 나타냅니다. 가능한 원인:
   * 공격자가 새 응용 프로그램을 사용 하 여 저장소 계정에 액세스 했습니다.
   * 합법적인 사용자가 새 응용 프로그램/브라우저를 사용 하 여 저장소 계정에 액세스 했습니다.

* **익명 액세스**: 이 경고는이 계정에 대 한 최근 액세스 패턴과 비교 하 여이 계정에 대 한 익명 액세스 (예: 인증 없음)를 나타냅니다.
가능한 원인:
   * 공격자가 컨테이너에 대 한 공용 읽기 액세스를 악용 했습니다.
   * 합법적인 사용자 또는 응용 프로그램에서 컨테이너에 대 한 공용 읽기 권한을 사용 했습니다.

* 이상: 이 경고는 (익명화 프록시)의 활성 종료 노드인 것으로 알려진 IP 주소에서이 계정이 성공적으로 액세스 되었음을 나타냅니다. 이 경고의 심각도는 사용 된 인증 유형 (있는 경우) 및이가 이러한 액세스의 첫 번째 사례 인지를 고려 합니다.
가능한 원인:
   * 공격자가를 사용 하 여 저장소 계정에 액세스 했습니다.
   * 합법적인 사용자가을 사용 하 여 저장소 계정에 액세스 했습니다.


### <a name="anomalous-extractupload-alerts"></a>비정상적인 추출/업로드 경고

* **데이터 Exfiltration**: 이 경고는이 저장소 컨테이너의 최근 작업과 비교 하 여 비정상적으로 많은 양의 데이터가 추출 되었음을 나타냅니다. 가능한 원인:
   * 공격자가 컨테이너에서 많은 양의 데이터를 추출 했습니다. (예: 데이터 exfiltration/위반, 데이터의 무단 전송)
   * 합법적인 사용자 또는 응용 프로그램이 컨테이너에서 비정상적으로 많은 양의 데이터를 추출 했습니다. (예: 유지 관리 활동)

* **예기치 않은 삭제**: 이 경고는이 계정에 대 한 최근 작업과 비교 하 여 저장소 계정에서 예기치 않은 삭제 작업이 하나 이상 발생 했음을 나타냅니다. 가능한 원인:
   * 공격자가 저장소 계정에서 데이터를 삭제 했습니다.
   * 합법적인 사용자가 비정상적인 삭제를 수행 했습니다.

* **Azure 클라우드 서비스 패키지 업로드**: 이 경고는 Azure 클라우드 서비스 패키지 (.cspkg 파일)가이 계정의 최근 활동과 비교 하 여 비정상적인 방식으로 저장소 계정에 업로드 되었음을 나타냅니다. 가능한 원인: 
   * 공격자가 저장소 계정에서 Azure 클라우드 서비스로 악성 코드를 배포할 준비를 하 고 있습니다.
   * 합법적인 사용자가 합법적인 서비스 배포를 준비 하 고 있습니다.

### <a name="suspicious-storage-activities-alerts"></a>의심 스러운 저장소 활동 경고

* **액세스 권한 변경**: 이 경고는이 저장소 컨테이너의 액세스 권한이 비정상으로 변경 되었음을 나타냅니다. 가능한 원인: 
   * 공격자는 보안을 약화 하기 위해 컨테이너 권한을 변경 했습니다.
   * 합법적인 사용자가 컨테이너 권한을 변경 했습니다.

* **액세스 검사**: 이 경고는이 계정에 대 한 최근 작업과 비교 하 여 저장소 계정에 대 한 액세스 권한이 비정상적인 방식으로 검사 되었음을 나타냅니다. 가능한 원인: 
   * 공격자가 향후 공격을 위해 정찰을 수행 했습니다.
   * 합법적인 사용자가 저장소 계정에 대 한 유지 관리를 수행 했습니다.

* **데이터 탐색**: 이 경고는 저장소 계정의 blob 또는 컨테이너가이 계정의 최근 활동과 비교 하 여 비정상적인 방식으로 열거 되었음을 나타냅니다. 가능한 원인: 
   * 공격자가 향후 공격을 위해 정찰을 수행 했습니다.
   * 합법적인 사용자 또는 응용 프로그램 논리는 저장소 계정 내에서 데이터를 탐색 했습니다.






## <a name="next-steps"></a>다음 단계

* [Azure Storage 계정의 로그](/rest/api/storageservices/About-Storage-Analytics-Logging) 에 대해 자세히 알아보기

* [Azure Security Center](../../security-center/security-center-intro.md)에 대한 자세한 정보
