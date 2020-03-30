---
title: 마이크로소프트 위협 모델링 도구 릴리스 4/9/2019
titleSuffix: Azure
description: Threat Modeling Tool 릴리스 정보 문서화
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269722"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>위협 모델링 도구 업데이트 릴리스 7.1.60408.1 - 2019년 4월 9일

Microsoft 위협 모델링 도구(TMT)의 버전 7.1.60408.1은 2019년 4월 9일에 릴리스되었으며 다음과 같은 변경 사항이 포함되어 있습니다.

- Azure 키 볼트 및 Azure 트래픽 관리자를 위한 새 스텐실
- TMT 버전 번호가 이제 홈 화면에 표시됩니다.
- 지원 링크가 업데이트되었습니다.
- 버그 수정

## <a name="feature-changes"></a>기능 변경

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure 키 볼트 및 Azure 트래픽 관리자를 위한 새 스텐실

![Azure 키 볼트 스텐실](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Azure 키 자격 증명 모음 및 Azure 트래픽 관리자에 대한 새 스텐실 및 위협이 Azure 스텐실 집합에 추가되었습니다. Azure 스텐실 집합을 기반으로 모델을 열 때 모델과 연결된 템플릿을 업데이트하라는 메시지가 표시됩니다. Azure 스텐실 집합을 기반으로 하는 모델 업데이트는 "파일" 메뉴에서 "템플릿 적용" 명령을 사용하고 최신 Azure Cloud Services.tb7 파일을 다시 적용하여 수동으로 시작할 수도 있습니다.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT 버전 번호가 이제 홈 화면에 표시됩니다.

위협 모델링 도구의 클라이언트 버전은 이제 액세스의 용이성을 위해 응용 프로그램의 홈 화면에 표시됩니다.

![Azure 키 볼트 스텐실](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>지원 링크가 업데이트되었습니다.

도구 내의 모든 지원 링크가 MSDN 포럼이 아닌 사용자를 직접 연결하도록 [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) 업데이트되었습니다.

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [마이크로소프트 윈도 10 주년 기념 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](threat-modeling-tool.md)에 있으며 [도구 사용](threat-modeling-tool-getting-started.md)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
