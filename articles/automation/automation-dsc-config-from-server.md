---
title: 기존 서버에서 구성 만들기 - Azure 자동화
description: Azure 자동화를 위한 기존 서버에서 구성을 만드는 방법을 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dff9b8f52207a38cf7eaddefa178aff262ddc546
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585559"
---
# <a name="create-configurations-from-existing-servers"></a>기존 서버에서 구성 만들기

> 적용: 윈도우 파워쉘 5.1

기존 서버에서 구성을 만드는 것은 어려운 작업일 수 있습니다.
*모든* 설정을 원하지 않을 수도 있습니다.
그럼에도 불구하고 구성이 성공적으로 적용되려면 설정을 적용해야하는 순서를 알아야합니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 참조합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-reversedsc"></a>커뮤니티 프로젝트: 리버스DSC

SharePoint를 시작하는 이 영역에서 작동하도록 [ReverseDSC라는](https://github.com/microsoft/reversedsc) 커뮤니티 유지 관리 솔루션이 만들어졌습니다.

이 솔루션은 [SharePointDSC 리소스를](https://github.com/powershell/sharepointdsc) 기반으로 하며 기존 SharePoint 서버에서 [정보를 수집하도록](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) 오케스트레이션하도록 확장합니다.
최신 버전에는 포함할 정보 수준을 결정하는 여러 [추출 모드가](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) 있습니다.

솔루션을 사용한 결과는 SharePointDSC 구성 스크립트와 함께 사용할 [구성 데이터를](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) 생성합니다.

데이터 파일이 생성되면 [DSC 구성 스크립트와](/powershell/scripting/dsc/overview/overview) 함께 MOF 파일을 생성하고 [MOF 파일을 Azure 자동화](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)에 업로드할 수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure에서](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) 서버를 등록하여 구성을 가져옵니다.

ReverseDSC를 사용해 보십시오. [PowerShell Gallery](https://www.powershellgallery.com/packages/ReverseDSC/) [documentation](https://github.com/Microsoft/sharepointDSC.reverse)

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell 필요한 상태 구성 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
