---
title: 상태 구성에서 사용할 STIG기반 구성 - Azure 자동화
description: Azure 자동화의 상태 구성에 대한 STIG를 기반으로 하는 구성에 대해 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028283"
---
# <a name="configuration-based-on-stig"></a>STIG 기반 구성

> 적용: 윈도우 파워쉘 5.1

처음으로 구성 콘텐츠를 만드는 것은 어려울 수 있습니다.
대부분의 경우 목표는 업계 권장 사항에 부합하는 "기준"에 따라 서버 구성을 자동화하는 것입니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 참조합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-powerstig"></a>커뮤니티 프로젝트: 파워스티그

[PowerSTIG라는](https://github.com/microsoft/powerstig) 커뮤니티 프로젝트는 STIG (보안 기술 구현 가이드)에 대해 제공 된 [공개 정보를](https://public.cyber.mil/stigs/) 기반으로 DSC 콘텐츠를 생성하여이 문제를 해결하는 것을 목표로합니다.

기준선을 다루는 것은 소리보다 더 복잡합니다.
많은 조직에서 규칙에 [대한 예외를 문서화하고](https://github.com/microsoft/powerstig#powerstigdata) 해당 데이터를 대규모로 관리해야 합니다.
PowerSTIG는 하나의 대용량 파일에서 전체 설정 범위를 해결하는 대신 구성의 각 영역을 처리하는 [복합 리소스를](https://github.com/microsoft/powerstig#powerstigdsc) 제공하여 문제를 해결합니다.

구성이 생성되면 [DSC 구성 스크립트를](/powershell/scripting/dsc/configurations/configurations) 사용하여 MOF 파일을 생성하고 [MOF 파일을 Azure 자동화](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)에 업로드할 수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure에서](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) 서버를 등록하여 구성을 가져옵니다.

PowerSTIG를 사용해 보십시오. [PowerShell Gallery](https://www.powershellgallery.com) [documentation](https://github.com/microsoft/powerstig)

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell 필요한 상태 구성 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
