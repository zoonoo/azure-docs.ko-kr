---
title: Azure Stack에서 공용 IP 주소 추가 | Microsoft Docs
description: Azure Stack에 더 많은 공용 IP 주소를 추가 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 09805719262f0a1d30f3b38af4b5209667d25e5a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195373"
---
# <a name="add-public-ip-addresses"></a>공용 IP 주소를 추가 합니다.
*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*  

Azure Stack에 더 많은 공용 IP 주소를 추가 하는 방법에 알아봅니다.  이 문서에서는 공용 IP 주소와 외부 주소를 지칭 아니지만 Azure Stack에서이 점이 외부 네트워크에 IP 주소 블록 추가를 참조 하세요.  여부는 외부 네트워크 라우팅할 수 있는 공용 인터넷 또는 인트라넷에서 이며 개인 주소 공간을 사용 하 여이 문서의 목적에 대 한 중요 하지 않습니다.  단계가 동일 합니다. 

## <a name="add-a-public-ip-address-pool"></a>공용 IP 주소 풀을 추가 합니다.
Azure Stack 시스템의 초기 배포 후 언제 든 지 Azure Stack 시스템에 공용 IP 주소를 추가할 수 있습니다. 체크 아웃 하는 방법 [뷰 공용 IP 주소 사용](azure-stack-viewing-public-ip-address-consumption.md) 가용성이 어떤 현재 사용량 및 공용 IP 주소를 보려면 Azure 스택에 합니다.

높은 수준에서 Azure Stack에 새 공용 IP 주소 블록을 추가 하는 과정은 다음과 같습니다.

 ![IP 흐름 추가](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>공급자의 주소 블록 가져오기
가장 먼저 수행 해야 Azure Stack에 추가 하려는 주소 블록을 가져오려고 됩니다.  주소 블록을 가져온 위치에 따라 리드 타임은 및 Azure Stack에서 공용 IP 주소를 사용 하는 속도 대 한 관리 고려를 해야 합니다.  

> [!IMPORTANT]
> Azure Stack으로 유효한 주소 블록을 이며 Azure Stack에서 기존 주소 범위와 겹치지 않는 제공 하는 주소 블록을 수락 합니다.  유효한 주소 블록을 라우팅 가능 하 고 Azure Stack 연결 되는 외부 네트워크와 겹치는 아닌 가져와야 있는지 확인 하십시오.  Azure Stack에 범위를 추가한 후에 제거할 수 없습니다.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Azure Stack에 IP 주소 범위 추가

1. 인터넷 브라우저에서 admin portal 대시보드로 이동 합니다.  사용 예를 들어 https://adminportal.local.azurestack.external합니다.  
2.  클라우드 운영자로 서 Azure Stack 관리 포털에 로그인 합니다.
3.  기본 대시보드에서 – 지역 관리 목록의 찾을 하 고 관리 하려면, 예를 들어 로컬 지역을 선택 합니다.
4.  리소스 공급자 타일 및 네트워크 리소스 공급자를 찾습니다.
5.  사용량 타일을 풀 하는 공용 IP를 클릭 합니다.
6.  추가 IP 풀 단추를 클릭 합니다.
7.  IP 풀에 대 한 이름을 제공 합니다.  호출할 수 있도록이 원하는 IP 풀을 쉽게 식별할 수 있도록 하는 방금 선택한 이름입니다.  주소 범위와 동일한 이름을 확인 하는 것이 좋습니다 있지만 필요 하지 않습니다.
8.   CIDR 표기법에서 추가 하려는 주소 블록을 입력 합니다.  예를 들면 다음과 같습니다. 192.168.203.0/24
9.  시작 ip 주소 범위 (CIDR 블록) 필드에 유효한 CIDR 범위를 제공 하는 경우 끝 IP 주소 및 사용 가능한 IP 주소 필드는 자동으로 채워집니다.  읽기 전용 이며 이러한 주소 범위의 필드에 값을 수정 하지 않고 변경할 수 없습니다 있도록 자동으로 생성 합니다.
10. 항목이 블레이드 및 확인 정보를 검토 한 후 해결, 변경 내용 커밋 및 Azure Stack에 주소 범위를 추가 하려면 확인을 클릭 합니다.


## <a name="next-steps"></a>다음 단계 
[검토 확장 단위 노드 작업](azure-stack-node-actions.md) 
