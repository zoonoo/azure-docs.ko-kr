---
title: "Azure Security Center에서 데이터 수집 활성화 | Microsoft Docs"
description: " Azure 보안 센터에서 데이터 수집을 활성화하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Azure 보안 센터에서 데이터 수집 활성화

> [!NOTE]
> 2017년 6월 초를 시작으로 Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집 및 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요. 이 문서의 정보는 Microsoft Monitoring Agent로 전환된 후의 Security Center 기능을 나타냅니다.
>
>

Security Center는 해당 보안 상태를 평가하고 보안 권장 사항을 제공하며 위협에 경고하기 위해 VM(가상 컴퓨터)에서 데이터를 수집합니다. Security Center에 처음 액세스하는 경우 구독의 모든 VM에서 데이터 수집을 사용하도록 설정할 수 있습니다. 데이터 수집이 사용하도록 설정되지 않으면 Security Center는 해당 구독에 대한 보안 정책에서 데이터 수집을 켜도록 권장합니다.

데이터 수집이 사용하도록 설정되면 Security Center는 지원되는 모든 기존 가상 컴퓨터 및 새로 만든 Azure 가상 컴퓨터에 Microsoft Monitoring Agent를 프로비전합니다. Microsoft Monitoring Agent는 다양한 보안 관련 구성을 검색합니다. 또한 운영 체제는 이벤트 로그 이벤트를 발생시킵니다. 이러한 데이터의 예: 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 프로세스 실행, 컴퓨터 이름, IP 주소, 로그인된 사용자 및 테넌트 ID입니다. Microsoft Monitoring Agent는 이벤트 로그 항목과 구성을 읽고 분석을 위해 작업 영역에 데이터를 복사합니다. 또한 Microsoft Monitoring Agent는 작업 영역에 크래시 덤프 파일을 복사합니다.

Security Center의 무료 계층을 사용하는 경우 보안 정책에서 데이터 수집 설정을 해제하여 가상 컴퓨터에서 데이터 수집을 사용하지 않도록 설정할 수 있습니다. 데이터 수집을 사용하지 않도록 설정하면 VM의 보안 평가가 제한됩니다. 자세한 내용은 [데이터 수집 비활성화](#disabling-data-collection)를 참조하세요. VM 디스크 스냅숏 및 아티팩트 컬렉션은 데이터 수집이 사용하지 않도록 설정된 경우에도 여전히 사용하도록 설정됩니다. 데이터 수집은 Security Center의 표준 계층에 대한 구독에 필요합니다.

> [!NOTE]
> Security Center의 무료 및 표준 계층에 대한 자세한 내용은 [가격 책정 계층](security-center-pricing.md)을 참조하세요.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 이 문서는 단계별 가이드가 아닙니다.
>
>

1. **권장 사항** 블레이드에서 **구독에 대한 데이터 수집 사용**을 선택합니다.  그러면 **데이터 수집 켜기** 블레이드가 열립니다.
   ![권장 사항 블레이드][2]
2. **데이터 수집 켜기** 블레이드에서 구독을 선택합니다. 해당 구독에 대한 **보안 정책** 블레이드가 열립니다.
3. **보안 정책** 블레이드에서 자동으로 로그를 수집하도록 **데이터 컬렉션**에서 **켜기**를 선택합니다. 데이터 컬렉션을 켜면 구독에 현재 포함된 VM과 지원되는 새 VM에 모니터링 확장이 프로비전됩니다.
4. **저장**을 선택합니다.
5. **확인**을 선택합니다.

## <a name="disabling-data-collection"></a>데이터 수집 비활성화
Security Center의 무료 계층을 사용하는 경우 보안 정책에서 데이터 수집을 해제하여 언제든지 가상 컴퓨터에서 데이터 수집을 사용하지 않도록 할 수 있습니다. 데이터 수집은 Security Center의 표준 계층에 대한 구독에 필요합니다.

1. **Security Center** 블레이드로 돌아가서 **정책** 타일을 선택합니다. 그러면 **보안 정책–구독별로 정책 정의** 블레이드가 열립니다.
   ![정책 타일 선택][5]
2. **보안 정책–구독별로 정책 정의** 블레이드에서 데이터 수집을 사용하지 않도록 설정하려는 구독을 선택합니다.
3. 해당 구독에 대한 **보안 정책** 블레이드가 열립니다.  데이터 컬렉션에서 **끄기** 를 선택합니다.
4. 상단 리본에서 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터 권장 사항 "데이터 수집 활성화"를 구현하는 방법을 보여 주었습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md) - Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/)-- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

