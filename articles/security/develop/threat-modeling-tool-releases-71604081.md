---
title: Threat Modeling Tool 릴리스 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool 릴리스 정보 문서화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6992ac58ebf8976714e5a478dd7b886efab2a4b7
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934797"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool 업데이트 릴리스 7.1.60408.1-4/9/2019

TMT (Microsoft Threat Modeling Tool 버전 7.1.60408.1)는 4 월 9 2019에 출시 되었으며 다음 변경 내용이 포함 되어 있습니다.

- Azure Key Vault 및 Azure Traffic Manager에 대 한 새 스텐실
- 이제 TMT 버전 번호가 홈 화면에 표시 됩니다.
- 지원 링크가 업데이트 되었습니다.
- 버그 수정

## <a name="feature-changes"></a>기능 변경

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure Key Vault 및 Azure Traffic Manager에 대 한 새 스텐실

![Azure Key Vault 스텐실](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Azure Key Vault 및 Azure Traffic Manager에 대 한 새로운 스텐실 및 위협이 Azure 스텐실 집합에 추가 되었습니다. Azure 스텐실 집합을 기반으로 모델을 열 때 사용자에 게 모델과 연결 된 템플릿을 업데이트 하 라는 메시지가 표시 됩니다. "파일" 메뉴에서 "템플릿 적용" 명령을 사용 하 여 Azure 스텐실 집합을 기반으로 모델을 업데이트 하 고 최신 Azure Cloud Services tb7 파일을 다시 적용 하 여 수동으로 시작할 수도 있습니다.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>이제 TMT 버전 번호가 홈 화면에 표시 됩니다.

이제 클라이언트 버전의 Threat Modeling Tool은 쉽게 액세스할 수 있도록 응용 프로그램의 홈 화면에 표시 됩니다.

![Azure Key Vault 스텐실](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>지원 링크가 업데이트 되었습니다.

도구 내의 모든 지원 링크가 MSDN 포럼 대신 사용자에 [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) 게 직접 업데이트 되었습니다.

## <a name="system-requirements"></a>시스템 요구 사항

- Supported Operating Systems
  - [Microsoft Windows 10 1주년 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](threat-modeling-tool.md)에 있으며 [도구 사용](threat-modeling-tool-getting-started.md)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
