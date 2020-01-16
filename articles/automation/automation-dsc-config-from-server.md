---
title: 기존 서버에서 구성 만들기-Azure Automation
description: Azure Automation 위해 기존 서버에서 구성을 만드는 방법에 대해 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030132"
---
# <a name="create-configurations-from-existing-servers"></a>기존 서버에서 구성 만들기

> 적용 대상: Windows PowerShell 5.1

기존 서버에서 구성을 만드는 작업은 어려울 수 있습니다.
*모든* 설정이 필요 하지 않을 수도 있습니다.
그러면 구성이 성공적으로 적용 되려면 설정을 적용 해야 하는 순서를 알아야 합니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리 하는 솔루션을 의미 합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-reversedsc"></a>커뮤니티 프로젝트: ReverseDSC

[ReverseDSC](https://github.com/microsoft/reversedsc) 라는 커뮤니티에서 유지 관리 하는 솔루션은이 영역에서 SharePoint를 시작 하기 위해 만들어졌습니다.

솔루션은 [SharePointDSC 리소스](https://github.com/powershell/sharepointdsc) 를 빌드하고 확장 하 여 기존 SharePoint 서버에서 [정보 수집](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) 을 오케스트레이션 합니다.
최신 버전에는 포함할 정보의 수준을 결정 하는 여러 [추출 모드가](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) 있습니다.

솔루션을 사용한 결과 SharePointDSC 구성 스크립트와 함께 사용할 [구성 데이터](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) 를 생성 합니다.

데이터 파일을 생성 한 후에는 [DSC 구성 스크립트](/powershell/scripting/dsc/overview/overview) 와 함께 사용 하 여 mof 파일을 생성 하 고 [Azure Automation에 mof 파일을 업로드할](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) 에서 서버를 등록 하 여 구성을 가져옵니다.

ReverseDSC을 체험 하려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/ReverseDSC/) 방문 하 여 솔루션을 다운로드 하거나 "프로젝트 사이트"를 클릭 하 여 [설명서](https://github.com/Microsoft/sharepointDSC.reverse)를 확인 하세요.

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
