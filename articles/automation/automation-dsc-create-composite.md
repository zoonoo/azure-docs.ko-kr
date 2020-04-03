---
title: 상태 구성을 위한 구성을 복합 리소스로 변환 - Azure 자동화
description: Azure Automation에서 상태 구성을 위해 구성을 복합 리소스로 변환하는 방법을 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585525"
---
# <a name="convert-configurations-to-composite-resources"></a>구성을 복합 리소스로 변환

> 적용: 윈도우 파워쉘 5.1

구성 작성을 시작하면 설정 그룹을 관리하는 "시나리오"를 빠르게 만들 수 있습니다.
예제는 다음과 같습니다.

- 웹 서버 만들기
- DNS 서버 만들기
- 공유점 서버 만들기
- SQL 클러스터 구성
- 방화벽 설정 관리
- 암호 설정 관리

이 작업을 다른 사람과 공유하려는 경우 구성을 [복합 리소스로](/powershell/scripting/dsc/resources/authoringresourcecomposite)패키징하는 것이 가장 좋습니다.
처음으로 복합 리소스를 만드는 것은 어려울 수 있습니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 참조합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-compositeresource"></a>커뮤니티 프로젝트: 컴포지트 리소스

이 문제를 해결하기 위해 [CompositeResource라는](https://github.com/microsoft/compositeresource) 커뮤니티 유지 관리 솔루션이 만들어졌습니다.

CompositeResource는 구성에서 새 모듈을 만드는 프로세스를 자동화합니다.
먼저 워크스테이션(또는 빌드 서버)에서 구성 스크립트를 [소싱하여](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) 메모리에 로드합니다.
그런 다음 구성을 실행하여 MOF 파일을 생성하는 대신 CompositeResource 모듈에서 제공하는 함수를 사용하여 변환을 자동화합니다.
cmdlet은 구성 내용을 로드하고 매개 변수 목록을 가져오고 필요한 모든 것을 갖춘 새 모듈을 생성합니다.

모듈을 생성한 후에는 변경을 수행할 때마다 버전을 증분하고 릴리스 메모를 추가하고 [PowerShellGet 리포지토리에](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)게시할 수 있습니다.

구성(또는 여러 구성)을 포함하는 복합 리소스 모듈을 만든 후에는 Azure의 [컴포저블 작성 환경에서](/azure/automation/compose-configurationwithcompositeresources) 이를 사용하거나 [DSC 구성 스크립트에](/powershell/scripting/dsc/configurations/configurations) 추가하여 MOF 파일을 생성하고 [MOF 파일을 Azure 자동화에 업로드할](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure에서](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) 서버를 등록하여 구성을 가져옵니다.
프로젝트에 대한 최신 업데이트는 PowerShell 갤러리에서 구성가져오기 프로세스를 자동화하기 위해 Azure Automation용 [Runbook을](https://www.powershellgallery.com/packages?q=DscGallerySamples) 게시했습니다.

DSC에 대한 복합 리소스 생성자동화를 시도하려면 [PowerShell 갤러리를](https://www.powershellgallery.com/packages/compositeresource/) 방문하여 솔루션을 다운로드하거나 "프로젝트 사이트"를 클릭하여 [설명서를](https://github.com/microsoft/compositeresource)봅니다.

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell 필요한 상태 구성 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
