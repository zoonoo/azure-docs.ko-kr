---
title: Azure Sentinel에 VMware ESXi 데이터 연결| Microsoft Docs
description: VMware ESXi 데이터 커넥터를 사용하여 Azure Sentinel에 ESXi 로그를 가져오는 방법을 알아봅니다. 통합 문서에서 ESXi 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700866"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Azure Sentinel에 VMware ESXi 연결

> [!IMPORTANT]
> VMware ESXi 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 VMware ESXi 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. VMware ESXi 데이터 커넥터를 사용하면 VMware ESXi 로그를 Azure Sentinel에 쉽게 수집할 수 있으므로 조직의 ESXi 작업에 자세한 정보를 제공하고 보안 작업 기능을 향상시킬 수 있습니다. VMware ESXi와 Azure Sentinel 간의 통합에는 Log Analytics 에이전트가 설치된 Syslog 서버를 사용합니다. 또한 Kusto 함수를 기반으로 하는 사용자 지정 파서를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- VMware ESXi 솔루션은 Syslog를 통해 로그를 내보내도록 구성해야 합니다.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Syslog 에이전트로 VMware ESXi 로그 보내기  

Syslog 에이전트를 통해 Azure Sentinel 작업 영역에 Syslog 메시지를 전달하도록 VMware ESXi를 구성합니다.
3. **VMware ESXi** 페이지의 지침을 따릅니다.


1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **VMware ESXi(미리 보기)** 커넥터를 선택한 다음, **커넥터 페이지를 엽니다**.

1. **VMware ESXi** 커넥터 페이지의 지시를 따릅니다.

    1. Linux용 에이전트 설치 및 온보딩

        - Azure Linux VM 또는 비 Azure Linux 컴퓨터(실제 또는 가상)를 선택합니다.

    1. 수집할 로그 구성

        - **작업 영역 에이전트 구성** 에서 시설 및 심각도를 선택합니다.

    1. VMware ESXi 구성 및 연결

        - 다음 지침에 따라 Syslog를 전달하도록 VMware ESXi를 구성합니다.
            - [VMware ESXi 3.5 및 4.x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 이상](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            원격 서버의 경우 Linux 에이전트를 설치한 Linux 컴퓨터의 IP 주소를 사용합니다.

## <a name="validate-connectivity-and-find-your-data"></a>연결 유효성 검사 및 데이터 찾기

로그가 Azure Sentinel에 표시되는 데는 최대 20분이 걸릴 수 있습니다. 

연결이 설정되면 데이터가 *Syslog* 테이블의 *로그 관리* 섹션에 있는 **로그** 에 표시됩니다.

이 데이터 커넥터는 정상적으로 작동하기 위해 Kusto 함수를 기반으로 하는 파서를 사용합니다. **VMwareESXi** [kusto 함수 별칭을 만들려면](https://aka.ms/sentinel-vmwareesxi-parser) 다음 지침을 따릅니다. 그러면 Azure Sentinel의 쿼리 창에 `VMwareESXi`를 입력하여 데이터를 쿼리할 수 있습니다.

몇 가지 유용한 쿼리 샘플은 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 VMware ESXi를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 잠재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
