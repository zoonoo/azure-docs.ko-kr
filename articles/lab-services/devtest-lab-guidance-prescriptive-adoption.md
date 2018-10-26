---
title: 기업용 Azure DevTest Labs 도입
description: 이 문서에서는 기업에서 Azure DevTest Labs를 도입하기 위한 규범 지침을 제공합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 5b4a52327227ecd650ee38d60f6bdfcc6bf20056
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250858"
---
# <a name="set-up-azure-devtest-labs-infrastructure-in-your-enterprise"></a>기업에서 Azure DevTest Labs 인프라 설정
빠른 속도, 뛰어난 유연성 및 비용 측면의 이점으로 인해 클라우드를 도입하는 기업이 급격히 증가하고 있습니다. 일반적으로 클라우드를 도입할 때는 먼저 개발 및 테스트 워크로드를 마이그레이션합니다. 이러한 워크로드를 마이그레이션할 때는 일반적으로 다음과 같은 문제를 해결해야 합니다. 

- 개발/테스트 리소스 보호
- 개발/테스트 환경과 프로덕션 환경의 명확한 분리
- IT, 응용 프로그램 및 프로젝트 팀 간의 리소스 구분
- 비용 관리
- 보안을 유지하면서 셀프 서비스 방식 개발/테스트 인프라 설정 허용

## <a name="intended-audience"></a>대상 그룹
이 문서는 전반적인 배포 과정을 설정/검토하고 운영 방식을 감독하는 IT 계획 담당자, 설계자 및 관리자를 대상으로 제공됩니다. 따라서 이 문서에서는 안전하고 안정적인 개발 환경을 제공하기 위한 전반적인 프로세스 및 권장 디자인 방식을 중점적으로 설명합니다. 이러한 환경이 구축되면 조직 내에서 Azure DevTest Labs를 원활하게 도입할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure DevTest Labs 사용 시작](devtest-lab-guidance-get-started.md)