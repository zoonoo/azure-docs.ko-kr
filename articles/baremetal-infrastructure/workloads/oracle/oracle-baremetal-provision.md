---
title: BareMetal for Oracle 프로비전
description: BareMetal Infrastructure for Oracle 프로비저닝에 대해 알아봅니다.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 59618d43d720fe4964d1e2971865f83572e681d9
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578554"
---
# <a name="provision-baremetal-for-oracle"></a>BareMetal for Oracle 프로비전

이 문서에서는 Oracle 워크로드에 대한 BareMetal Infrastructure 인스턴스를 프로비전하는 방법을 살펴보겠습니다. 

BareMetal 인스턴스를 프로비전하는 첫 번째 단계는 Microsoft CSA와 함께 작업하는 것입니다. Microsoft CSA는 단일 인스턴스, One Node RAC 또는 RAC 여부에 관계없이 배포 중인 아키텍처와 특정 워크로드 요구 사항에 따라 도움을 제공합니다. 이러한 토폴로지에 대한 자세한 내용은 [BareMetal Infrastructure For Oracle의 아키텍처](oracle-baremetal-architecture.md)를 참조하세요.

## <a name="prerequisites"></a>필수 요건

> [!div class="checklist"]
> * 활성 Azure 구독
> * Microsoft 프리미어 지원 계약
> * Red Hat Enterprise Linux 7.6에 대한 라이선스
> * Oracle 지원 계약 
> * Oracle에 대한 라이선스 및 소프트웨어 설치 구성 요소
> * **온-프레미스에서 Azure** 에 연결된 ExpressRoute(**선택 사항**), 온-프레미스에서 Oracle Database로 직접 연결을 위해 ExpressRoute Global Reach 구성   
> * 가상 네트워크
> * 게이트웨이 생성
> * 가상 네트워크(점프 상자)의 VM(가상 머신)

## <a name="information-to-provide-microsoft-operations"></a>Microsoft Operations 제공을 위한 정보

CSA에 다음 정보를 제공해야 합니다.

1. 가상 네트워크 주소 공간. 이 범위는 /24 서브넷(예: 10.11.0.0/24)이어야 합니다.
2. P2P 범위. 이 범위는 /29 서브넷(예: 10.12.0.0/29)이어야 합니다.
3. 서버 IP 주소 풀. 권장 범위는 /24(예: 10.13.0.0/24)입니다.
4. 서버 IP 주소. 서버 IP 주소 풀에서 IP 주소를 선택합니다.

    > [!Note] 
    > 처음 30개의 IP 주소는 Microsoft infrastructure 구성용으로 예약되어 있습니다. 따라서 이 예에서 블레이드에 사용 가능한 첫 번째 IP 주소는 10.13.0.30입니다.

5. Azure 지역 필요(예: 미국 서부2).
6. BareMetal Infrastructure SKU 필요(예: S192(컴퓨터 2대)).

## <a name="storage-requirements"></a>저장소 요구 사항

향후 성장에 따른 예상 스토리지 요구 사항 등, 프로비전 요청 중 스토리지 요구 사항에 대해 CSA 담당자와 협력하세요. 추가된 스토리지는 1TB 단위로 증가합니다.

볼륨의 경우 기본 계층 및 엔터프라이즈 구성으로 Oracle의 [OFA(Optimal Flexible Architecture) 표준](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)을 따릅니다. 표준 "티셔츠 크기 조정" 이외의 사용자 지정 스토리지 요구 사항이 있는 경우 CSA를 통해 사용자 지정 요청을 만듭니다.

| 기본 구성(POC/테스트) | Description | 작음 | 중간 | 대형 |
| --- | --- | --- | --- | --- |
| /u01 | Oracle 이진 파일 | 500GB | 500GB | 500GB |
| /u02 | 읽기 집약적/관리자 | 500GB | 1TB | 5TB |
| /u03 | 쓰기 집약적/로그 | 500GB | 1TB | 5TB |
| /u09 | Backup | 5TB | 10TB | 15TB |

| 엔터프라이즈 구성 | Description | 작음 | 중간 | 대형 | 초대형 |
| --- | --- | --- | --- | --- | --- |
| /u01 | Oracle 이진 파일 | 500GB | 500GB | 500GB | 500GB |
| /u02 | Admin | 100GB | 100GB | 100GB | 100GB |
| /u10 ~ /u59 | 읽기 집약적 | 500GB | 5TB | 10TB | 20TB |
| /u60 ~ /u89 | 쓰기 집약적 | 500GB | 5TB | 10TB | 20TB |
| /u90 ~ /u91 | 다시 실행 로그 | 500GB | 500GB | 1TB | 1TB |
| /u95 | 보관 | 10TB | 10TB | 20TB | 20TB |
| /u98 | Backup | 25TB | 25TB | 50TB | 50TB |

## <a name="next-step"></a>다음 단계

BareMetal Infrastructure for Oracle에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure의 BareMetal Infrastructure란?](../../concepts-baremetal-infrastructure-overview.md)
