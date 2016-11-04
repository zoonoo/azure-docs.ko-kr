---
title: Azure 보안 센터의 시스템 업데이트 적용 | Microsoft Docs
description: 이 문서에서는 Azure 보안 센터 권장 사항 **시스템 업데이트 적용** 및 **시스템 업데이트 후 다시 부팅**을 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Azure 보안 센터의 시스템 업데이트 적용
Azure 보안 센터는 누락된 운영 체제 업데이트에 대해 매일 Windows 및 Linux 가상 컴퓨터(VM)를 모니터링합니다. 보안 센터는 Windows VM에서 구성된 서비스에 따라 Windows 업데이트 또는 WSUS(Windows Server Update Services)에서 사용 가능한 보안 및 중요 업데이트의 목록을 검색합니다. 보안 센터는 또한 Linux 시스템에서 최신 업데이트를 확인합니다. VM이 시스템 업데이트를 누락하는 경우 보안 센터는 시스템 업데이트 적용을 권장합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.
> 
> 

## 권장 사항 구현
1. **권장 사항** 블레이드에서 **시스템 업데이트 적용**을 선택합니다. ![시스템 업데이트 적용][1]
2. 시스템 업데이트를 누락한 VM의 목록을 표시하는 **시스템 업데이트 적용** 블레이드가 열립니다. VM을 선택합니다. ![VM 선택][2]
3. 해당 VM에 대한 누락된 업데이트의 목록을 표시하는 블레이드가 열립니다. 시스템 업데이트를 선택합니다. 이 예제에서는 KB3156016을 선택해 보겠습니다. ![누락된 보안 업데이트][3]
4. **보안 업데이트** 블레이드의 단계를 따라 누락된 업데이트를 적용합니다. ![보안 업데이트][4]

## 시스템 업데이트 후 다시 부팅
1. **권장 사항** 블레이드로 돌아갑니다. 시스템 업데이트를 적용한 후 **시스템 업데이트 후 다시 부팅**이라는 새 항목이 생성되었습니다. 이 항목을 통해 시스템 업데이트 적용 프로세스를 완료하려면 VM을 다시 부팅해야 한다는 것을 알 수 있습니다. ![시스템 업데이트 후 다시 부팅][5]
2. **시스템 업데이트 후 다시 부팅**을 선택합니다. 시스템 업데이트 적용 프로세스를 완료하기 위해 다시 시작해야 하는 VM의 목록을 표시하는 **시스템 업데이트를 완료하기 위한 다시 시작이 보류 중입니다** 블레이드가 열립니다. ![다시 시작 보류 중][6]

프로세스를 완료하려면 Azure에서 VM을 다시 시작합니다.

## 참고 항목
보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) – Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) -- 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) -- Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]: ./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png

<!---HONumber=AcomDC_0803_2016-->