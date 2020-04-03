---
title: 대규모 구성 데이터 - Azure 자동화
description: Azure 자동화에서 상태 구성에 대해 대규모로 데이터를 구성하는 방법에 대해 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585567"
---
# <a name="configuration-data-at-scale"></a>규모에 맞는 구성 데이터

> 적용: 윈도우 파워쉘 5.1

수백 또는 수천 개의 서버를 관리하는 것은 어려울 수 있습니다.
고객은 가장 어려운 측면이 실제로 [구성 데이터를](/powershell/scripting/dsc/configurations/configdata)관리하는 것이라는 피드백을 제공했습니다.
위치, 유형 및 환경과 같은 논리 구문 에서 정보를 구성합니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 참조합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-datum"></a>커뮤니티 프로젝트: 데이텀

이 문제를 해결하기 위해 [Datum이라는](https://github.com/gaelcolas/Datum) 커뮤니티 유지 관리 솔루션이 만들어졌습니다.
Datum은 다른 구성 관리 플랫폼의 훌륭한 아이디어를 기반으로 PowerShell DSC에 대해 동일한 유형의 솔루션을 구현합니다.
정보는 논리적 아이디어를 기반으로 [텍스트 파일에 구성됩니다.](https://github.com/gaelcolas/Datum#3-intended-usage)
예제는 다음과 같습니다.

- 전역적으로 적용해야 하는 설정
- 위치의 모든 서버에 적용해야 하는 설정
- 모든 데이터베이스 서버에 적용해야 하는 설정
- 개별 서버 설정

이 정보는 원하는 파일 형식(JSON, Yaml 또는 PSD1)으로 구성됩니다.
그런 다음 cmdlet이 제공되어 각 파일의 정보를 서버 또는 서버 역할의 단일 보기로 [통합하여](https://github.com/gaelcolas/Datum#datum-tree) 구성 데이터 파일을 생성합니다.

데이터 파일이 생성되면 [DSC 구성 스크립트와](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) 함께 MOF 파일을 생성하고 [MOF 파일을 Azure 자동화](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)에 업로드할 수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure에서](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) 서버를 등록하여 구성을 가져옵니다.

Datum을 사용해 보십시오. [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) [documentation](https://github.com/gaelcolas/Datum#2-getting-started--concepts)

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell 필요한 상태 구성 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
