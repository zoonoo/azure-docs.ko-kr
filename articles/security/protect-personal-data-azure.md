---
title: "Microsoft Azure에서 개인 데이터 보호 | Microsoft Docs"
description: "Azure를 사용하여 개인 데이터를 보호하는 데 도움이 되는 문서 시리즈의 첫 번째 문서입니다."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4dbdb2dc11bdc515fb3856dd45203868122c7726
ms.contentlocale: ko-kr
ms.lasthandoff: 08/30/2017

---
# <a name="protect-personal-data-in-microsoft-azure"></a>Microsoft Azure에서 개인 데이터 보호

이 문서에서는 Azure 보안 기술 및 서비스를 사용하여 개인 데이터를 보호하는 데 도움이 되는 일련의 문서를 소개합니다. 이는 많은 기업 및 업계의 규정 준수 및 거버넌스 이니셔티브의 핵심 요구 사항입니다. 여기에는 시나리오, 문제 설명 및 회사 목표가 포함됩니다.

## <a name="scenario-and-problem-statement"></a>시나리오 및 문제 설명

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해, 아드리아해 및 발트해 연안의 여정도 제공하도록 사업을 확장하고 있습니다. 이러한 노력을 지원하기 위해 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 몇 개의 소형 크루즈 라인을 인수했습니다.

회사에서 Microsoft Azure를 사용하여 클라우드에 회사 데이터를 저장합니다. 여기에는 다음과 같은 고객 및/또는 직원 정보가 포함될 수 있습니다.

- 주소
- 전화 번호
- 납세자 번호
- 신용 카드 정보

회사에서 고객 및 직원 데이터의 개인 정보를 보호해야 하지만, 필요로 하는 부서(예: 급여 및 예약 부서)에서 해당 데이터에 액세스할 수 있어야 합니다.

## <a name="company-goals"></a>회사 목표 

- 개인 데이터가 포함된 데이터 원본은 클라우드 저장소에 있을 때 암호화됩니다.

- 한 위치에서 다른 위치로 전송되는 개인 데이터는 전송 중에 암호화됩니다. 데이터가 가상 네트워크에서 또는 기업 데이터 센터와 Azure 클라우드 사이의 인터넷에서 이동하는 경우에도 마찬가지입니다.

- 강력한 ID 관리 및 액세스 제어 기술을 통해 무단 액세스로부터 개인 데이터의 기밀성과 무결성을 보호합니다.

- 개인 데이터는 취약성 및 위협에 대해 모니터링하여 데이터 위반을 통한 노출로부터 보호됩니다.

- 개인 데이터를 저장하거나 전송하는 Azure 서비스의 보안 상태는 개인 데이터를 더 효율적으로 보호할 수 있는 기회를 식별하기 위해 평가됩니다.

## <a name="data-protection-guidance"></a>데이터 보호 지침

다음 문서에는 위에서 나열된 개인 데이터 보호 목표를 달성하는 데 도움이 되는 기술적 사용 지침이 포함되어 있습니다.

- [Azure 암호화 기술](protect-personal-data-at-rest.md)

- [Azure 암호화 기술](protect-personal-data-in-transit-encryption.md)

- [Azure ID 및 액세스 기술](protect-personal-data-identity-access-controls.md)

- [Azure 네트워크 보안 기술](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>다음 단계

- [Azure 보안 정보 사이트](https://aka.ms/AzureSecInfo)

- [Microsoft 보안 센터](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Azure 보안 팀 블로그](https://www.azuresecurityorg)

- [Azure.com 블로그 - 보안](https://azure.microsoft.com/blog/topics/security/)

