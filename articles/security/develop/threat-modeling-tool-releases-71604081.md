---
title: Microsoft Threat Modeling Tool 릴리스 4/9/2019
titleSuffix: Azure
description: Threat Modeling Tool 릴리스 7.1.60408.1의 릴리스 정보를 문서화합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 69dd2c6cdba41779849b4eb6b889cde9b1d6e5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913571"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool 업데이트 릴리스 7.1.60408.1 - 4/9/2019

Microsoft TMT(Threat Modeling Tool) 버전 7.1.60408.1은 2019년 4월 9일 릴리스되었으며 이 버전에 포함된 변경 내용은 다음과 같습니다.

- Azure Key Vault 및 Azure Traffic Manager의 새로운 스텐실
- 이제 TMT 버전 번호가 홈 화면에 표시됨
- 지원 링크가 업데이트됨
- 버그 수정

## <a name="feature-changes"></a>기능 변경

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure Key Vault 및 Azure Traffic Manager의 새로운 스텐실

![Azure Key Vault 및 Azure Traffic Manager 아이콘을 보여 주는 스크린샷](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Azure Key Vault 및 Azure Traffic Manager의 새로운 스텐실과 위협이 Azure 스텐실 세트에 추가되었습니다. Azure 스텐실 세트에 따라 모델을 여는 경우 모델과 연결된 템플릿을 업데이트하라는 메시지가 사용자에게 표시됩니다. Azure 스텐실 세트에 따른 모델 업데이트는 “파일” 메뉴의 “템플릿 적용” 명령을 사용하고 최신 Azure Cloud Services.tb7 파일을 다시 적용하여 수동으로 시작할 수도 있습니다.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>이제 TMT 버전 번호가 홈 화면에 표시됨

이제 Threat Modeling Tool의 클라이언트 버전이 쉽게 액세스할 수 있도록 애플리케이션의 홈 화면에 표시됩니다.

![클라이언트 버전 번호와 함께 Microsoft Threat Modeling Tool을 보여 주는 스크린샷](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>지원 링크가 업데이트됨

사용자를 MSDN 포럼이 아닌 [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com)으로 안내하도록 도구 내 모든 지원 링크가 업데이트되었습니다.

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [Microsoft Windows 10 1주년 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](threat-modeling-tool.md)에 있으며 [도구 사용](threat-modeling-tool-getting-started.md)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
