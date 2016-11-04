---
title: 펜 테스트 | Microsoft Docs
description: 이 문서는 침투 테스트 (pentest) 프로세스의 개요와 Azure 인프라에서 실행 중인 앱에 대해 pentest를 수행하는 방법을 제공합니다.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh

ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2016
ms.author: yurid

---
# 펜 테스트
응용 프로그램 테스트 및 배포를 위해 Microsoft Azure를 사용하는 장점은 응용 프로그램을 개발하고, 테스트하고, 배포하기 위해 온-프레미스 인프라를 준비할 필요가 없다는 것입니다. 모든 인프라는 Microsoft Azure Platform 서비스에서 처리합니다. 자신의 온-프레미스 하드웨어 요청, 구입, “래킹과 스태킹(racking and stacking)”에 대해 걱정할 필요가 없습니다.

이것은 좋은 일입니다 – 그렇지만 여전히 일반적인 보안 실사는 수행해야 합니다. 수행해야 할 일 중 하나는 Azure에서 배포하는 응용 프로그램에 대한 침투 테스트를 하는 것입니다.

Microsoft가 [Azure 환경에서의 침투 테스트](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 수행하는 것에 관해 어쩌면 이미 알고 계실 것입니다. 이는 보안 제어를 개선하고, 신규 보안 제어를 소개하고, 보안 프로세스를 개선하는 관점에서 우리 작업을 이끌고 플랫폼을 개선하는데 도움이 됩니다.

우리는 여러분을 위해 응용 프로그램을 펜 테스트하지는 않지만, 여러분이 자신의 응용 프로그램에 펜 테스트를 수행하기를 원하며 그럴 필요가 있다는 것을 이해합니다. 응용 프로그램의 보안을 강화할 때 Azure 전체 에코시스템을 보다 안전하게 보호하는데 도움이 되기 때문에 그렇게 하는 것이 좋습니다.

응용 프로그램을 펜 테스트할 때, 우리가 공격받고 있는 것처럼 보일 수 있습니다. 우리는 공격 패턴에 대해 [지속적인 모니터링](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx)을 하며 필요에 따라 인시던트 대응 프로세스를 시작합니다. 여러분 자신의 펜 테스트 실사 때문에 인시턴트 대응을 트리거한다면 여러분과 우리에게 도움이 되지 않습니다.

그렇다면 어떻게 해야 할까요?

Azure 호스티드 응용 프로그램을 펜 테스트할 준비가 되면, 우리에게 알려주셔야 합니다. 여러분이 특정 테스트를 수행하리라는 것을 우리가 알고 있으면, 테스트가 Azure 펜 테스트 조건을 준수하는 한 (여러분이 테스트를 진행하는 IP 주소를 차단하는 등과 같이) 의도치 않게 종료하지 않을 것입니다. 여러분이 수행할 수 있는 표준 테스트는 다음과 같습니다.

* [OWASP(Open Web Application Security Project) 취약점 상위 10개](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)을 찾아내기 위한 끝점 테스트
* 끝점 [퍼지 테스트](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* 끝점 [포트 검색](https://en.wikipedia.org/wiki/Port_scanner)

여러분이 수행할 수 없는 한가지 유형의 테스트는 모든 종류의 [서비스 거부 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 공격입니다. 이는 DoS 공격 자체를 시작하거나, 모든 종류의 DoS 공격을 결정, 시연 또는 시뮬레이션할 수도 있는 관련 테스트를 수행하는 것을 포함합니다.

Microsoft Azure에서 호스팅된 응용 프로그램의 펜 테스트를 시작할 준비가 되셨습니까? 그렇다면, [침투 테스트 개요](https://security-forms.azure.com/penetration-testing/terms) 페이지를 참조하고 (페이지 맨 아래에 있는 테스트 요청 만들기 버튼을 클릭합니다. 또한 펜 테스트 조건에 대한 더 많은 정보와 Azure 또는 다른 모든 Microsoft 서비스와 관련된 보안 결함을 보고할 수 방법에 대한 유용한 링크도 있습니다.

<!---HONumber=AcomDC_0810_2016-->