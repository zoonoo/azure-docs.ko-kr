---
title: Azure Storage에서 위협 모니터링
description: 계정 작업에서 잘못된 부분을 검색하여 잠재적으로 해로운 계정 액세스 시도에 대해 알리도록 Azure Storage Advanced Threat Protection을 구성합니다.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995649"
---
# <a name="azure-storage-advanced-threat-protection"></a>Azure Storage Advanced Threat Protection

Azure Storage Advanced Threat Protection은 계정 작업에서 잘못된 부분을 검색하여 잠재적으로 해로운 계정 액세스 시도에 대해 알려줍니다. 이 보호 레이어를 사용하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결 수 있습니다.

작업에서 잘못된 부분이 발생하면 트리거되는 보안 경고를 정의하여 위협을 드러냅니다. 이러한 경고는 의심스러운 작업에 대한 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 작업을 포함하고 있는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합됩니다. 

> [!NOTE]
> Azure Storage Advanced Threat Protection은 현재 Blob 서비스에만 사용할 수 있습니다. 보안 경고는 Azure Security Center와 통합되어 이메일을 통해 구독 관리자에게 전달됩니다.

Azure Storage Advanced Threat Protection은 Blob 서비스에 대한 읽기, 쓰기 및 삭제 요청의 진단 로그를 수집하여 위협을 감지합니다. Advanced Threat Protection에서 경고를 조사하려면 Blob 서비스에 모든 수준의 로그를 사용하도록 [진단 로그를 구성](storage-monitor-storage-account.md#configure-logging)해야 합니다.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>포털에서 Advanced Threat Protection 설정

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal을 시작합니다.

2. 보호할 Azure Storage 계정의 구성 페이지로 이동합니다. **설정** 페이지에서 **Advanced Threat Protection**을 선택합니다.

3. **Advanced Threat Protection** 구성 블레이드에서 다음을 수행합니다.
    * Advanced *Threat Protection*을 **켭니다**.
    * **저장**을 클릭하여 새로운 또는 업데이트된 Advanced Threat Protection 정책을 저장합니다.

![Azure Storage Advanced Threat Protection 켜기](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>잘못된 부분 탐색

저장소 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

* 잘못된 부분의 특성
* 저장소 계정 이름
* 저장소 유형
* 이벤트 시간

또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Azure Security Center의 [보안 경고 타일](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)에서 현재 보안 경고를 검토 및 관리할 수 있습니다. 특정 경고를 클릭하면 현재 위협을 조사하고 향후 위협을 해결하기 위한 추가 세부 정보 및 조치가 제공됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>보호 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 저장소 계정 악용에 의해 생성됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

* **평소와 다른 위치에서 액세스**: 저장소 계정에 대한 액세스 패턴에 변화가 있을 경우 이 경고가 트리거됩니다. 사용자가 평소와 다른 지리적 위치에서 저장소 계정에 액세스하는 경우를 예로 들 수 있습니다. 일부 경우에서 경고는 합법적인 작업(새 응용 프로그램 또는 개발자 유지 관리 작업)을 검색합니다. 또 어떤 경우에는 경고가 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.

* **평소와 다른 데이터 추출**: 저장소 계정의 데이터 추출 패턴에 변화가 있을 경우 이 경고가 트리거됩니다. 사용자가 저장소 계정에서 비정상적으로 많은 양의 데이터에 액세스하는 경우를 예로 들 수 있습니다. 어떤 경우에는 경고가 합법적인 작업(유지 관리 작업)을 검색합니다. 또 어떤 경우에는 경고가 악의적인 작업(데이터 반출/위반, 데이터의 무단 전송)을 검색합니다.

* **평소와 다른 익명 액세스**: 저장소 계정에 대한 액세스 패턴에 변화가 있을 경우 이 경고가 트리거됩니다. 누군가가 저장소 계정에 익명으로 액세스하는 경우를 예로 들 수 있습니다. 어떤 경우에는 경고가 공용 읽기 액세스를 사용하여 합법적인 액세스를 검색합니다. 또 어떤 경우에는 경고가 컨테이너 및 해당 Blob에 대한 공용 읽기 액세스를 악용하는 무단 액세스를 검색합니다.

* **예기치 않은 삭제:** 저장소 계정에서 하나 이상의 예기치 않은 삭제 작업이 발생하는 경우 저장소 계정의 기록 분석에 따라 이 경고가 트리거됩니다. 누군가가 새 응용 프로그램을 사용하여 새 IP 주소에서 *DeleteBlob* 작업을 수행한 경우를 예로 들 수 있습니다. 어떤 경우에는 경고가 합법적인 작업(관리자가 사업 출장 중에 다른 브라우저를 사용)을 검색합니다. 또 어떤 경우에는 경고가 악의적인 작업(공격자가 데이터 삭제)을 검색합니다. 
 
* **액세스 권한 변경:** 저장소 계정에 대한 액세스 권한이 예기치 않게 변경되면 이 경고가 트리거됩니다. 누군가가 새 응용 프로그램을 사용하여 새 IP 주소에서 저장소 계정에 대한 액세스 권한을 변경한 경우를 예로 들 수 있습니다. 어떤 경우에는 경고가 합법적인 작업(관리자가 사업 출장 중에 다른 브라우저를 사용)을 검색합니다. 또 어떤 경우에는 경고가 악의적인 작업(예를 들어 공격자가 액세스 권한을 확보한 계정의 권한을 높임)을 검색합니다. 

* **Azure 클라우드 서비스 패키지 업로드:** Azure 클라우드 서비스 패키지(*.cspkg* 파일)가 예기치 않게 저장소 계정에 업로드되는 경우 이 경고가 트리거됩니다. 새 IP 주소에서 *.cspkg* 파일이 업로드된 경우를 예로 들 수 있습니다. 어떤 경우에는 경고가 합법적인 작업을 검색합니다. 또 어떤 경우에는 경고가 악의적인 작업(예를 들어 악성 서비스를 배포하기 위한 준비 과정으로 클라우드 서비스를 업로드)을 검색합니다.    
   

## <a name="next-steps"></a>다음 단계

* [Azure Storage 계정에 로그인](/rest/api/storageservices/About-Storage-Analytics-Logging)에 대한 자세한 정보

* [Azure Security Center](../../security-center/security-center-intro.md)에 대한 자세한 정보