---
title: Azure Security Center FAQ-가상 컴퓨터에 대 한 질문
description: 위협을 예방, 감지 및 대응 하는 데 도움이 되는 Azure Security Center의 가상 컴퓨터에 대 한 질문과 대답
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599367"
---
# <a name="faq---questions-about-virtual-machines"></a>FAQ-가상 컴퓨터에 대 한 질문


## <a name="what-types-of-virtual-machines-are-supported"></a>어떤 유형의 가상 머신이 지원되나요?

[클래식 및 Resource Manager 배포 모델](../azure-classic-rm.md)을 모두 사용하여 작성된 VM(가상 머신)에 대해 모니터링 및 권장 사항이 제공됩니다.

지원되는 플랫폼 목록은 [Azure Security Center에서 지원되는 플랫폼](security-center-os-coverage.md)을 참조하세요.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure Security Center가 내 Azure VM에서 실행 중인 맬웨어 방지 솔루션을 인식하지 못하는 이유는 무엇인가요?

Azure Security Center는 Azure 확장을 통해 설치된 맬웨어 방지 프로그램을 확인할 수 있습니다. 예를 들어 보안 센터는 사용자가 제공한 이미지에 미리 설치되어 있거나, 구성 관리 시스템 등의 고유한 프로세스를 사용하여 가상 머신에 설치한 맬웨어 방지 프로그램을 검색할 수 없습니다.


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>VM에 대해 "검사 데이터 누락" 메시지가 표시되는 이유는 무엇인가요?

VM에 대한 검색 데이터가 없는 경우 이 메시지가 표시됩니다. Azure Security Center에서 데이터 수집을 사용하도록 설정한 후 검사 데이터가 입력될 때까지는 다소 시간이 걸릴 수 있습니다(1시간 이내). 검색 데이터를 처음 입력한 후에는 검색 데이터가 전혀 없거나 최근 검색 데이터가 없기 때문에 이 메시지가 나타날 수 있습니다. VM이 중지된 상태이면 검사를 수행해도 데이터가 입력되지 않습니다. Windows 에이전트의 보존 정책에 따라 최근에(기본값은 30일) 검색 데이터가 입력되지 않은 경우에도 이 메시지가 나타날 수 있습니다.


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Security Center는 운영 체제 취약점, 시스템 업데이트 및 Endpoint Protection 문제를 얼마나 자주 검사합니까?

다음은 취약점, 업데이트 및 문제에 대 한 Security Center 검색에 대 한 대기 시간입니다.

- 운영 체제 보안 구성 - 데이터가 48시간 이내 업데이트됩니다.
- 시스템 업데이트 – 데이터가 24시간 이내 업데이트됩니다.
- Endpoint Protection 문제 – 데이터가 8시간 이내 업데이트됩니다.

Security Center는 일반적으로 한 시간마다 새 데이터를 검사하며 그에 따라 권장 사항을 새로 고칩니다. 

> [!NOTE]
> Security Center는 Microsoft Monitoring Agent를 사용 하 여 데이터를 수집 하 고 저장 합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요.


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>"VM 에이전트가 누락됨" 메시지가 표시되는 이유는 무엇인가요?

데이터 수집을 사용하도록 설정하려면 VM에 VM 에이전트를 설치해야 합니다. Azure Marketplace에서 배포된 VM에 VM 에이전트가 기본적으로 설치됩니다. 다른 VM에 VM 에이전트를 설치하는 방법에 대한 자세한 내용은 블로그 게시물 [VM 에이전트 및 확장](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)을 참조하세요.