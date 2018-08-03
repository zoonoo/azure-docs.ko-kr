---
title: Azure Stack 데이터 센터 통합-보안
description: 데이터 센터 보안을 사용 하 여 Azure Stack 보안을 통합 하는 방법에 알아봅니다.
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442478"
---
# <a name="azure-stack-datacenter-integration---security"></a>Azure Stack 데이터 센터 통합-보안
Azure Stack 설계 및 보안을 염두를 사용 하 여 작성 되었습니다. Azure Stack 잠긴 시스템 이므로 소프트웨어 보안 에이전트 설치가 지원 되지 않습니다.

이 문서에서는 데이터 센터에 이미 배포 된 보안 솔루션을 사용 하 여 Azure Stack의 보안 기능을 통합 합니다.

## <a name="security-logs"></a>보안 로그

Azure Stack 2 분 마다 운영 체제 및 인프라 역할과 단위 노드 크기 조정에 대 한 보안 이벤트 수집합니다. 로그는 저장소 계정 blob 컨테이너에 저장 됩니다.

인프라 역할당 하나의 저장소 계정 및 모든 일반적인 운영 체제 이벤트에 대 한 일반 저장소 계정이 있습니다.

Blob 컨테이너 URL을 검색할 REST 프로토콜을 통해 상태 리소스 공급자를 호출할 수 있습니다. 타사 보안 솔루션 API 및 저장소 계정을 사용 하 여 처리에 대 한 이벤트를 검색할 수 있습니다.

### <a name="use-azure-storage-explorer-to-view-events"></a>Azure Storage 탐색기를 사용 하 여 이벤트를 보려면

Azure Storage 탐색기 라는 도구를 사용 하 여 Azure Stack에 의해 수집 된 이벤트를 검색할 수 있습니다. Azure Storage 탐색기에서 다운로드할 수 있습니다 [ http://storageexplorer.com ](http://storageexplorer.com)합니다.

다음 절차는 Azure Stack에 대 한 Azure Storage 탐색기를 구성 하 여 예제:

1. 운영자로 서 Azure Stack 관리자 포털에 로그인 합니다.
1. 찾아보기 **Storage 계정** 를 찾아서 **frphealthaccount**합니다. 합니다 **frphealthaccount** 계정은 모든 운영 체제 이벤트를 저장 하는 데 일반 저장소 계정입니다.

   ![Storage 계정](media/azure-stack-integrate-security/storage-accounts.png)

1. 선택 **frphealthaccount**, 클릭 **선택키가**합니다.

   ![액세스 키](media/azure-stack-integrate-security/access-keys.png)

1. 액세스 키를 클립보드에 복사 합니다.
1. Azure Storage 탐색기를 엽니다.
1. 에 **편집할** 메뉴에서 **대상 Azure Stack**합니다.
1. 선택 **계정 추가**를 선택한 후 **저장소 계정 이름과 키를 사용 하 여**입니다.

   ![저장소 연결](media/azure-stack-integrate-security/connect-storage.png)

1. **다음**을 클릭합니다.
1. 에 **외부 저장소 연결** 페이지:

   a. 계정 이름을 입력 **frphealthaccount**합니다.

   나. 저장소 계정 액세스 키를 붙여 넣습니다.

   다. 아래 **저장소 끝점 도메인**를 선택 **기타**, 저장소 끝점을 지정 하 고 **[Region]. [ DomainName]** 합니다.

   d. 선택 된 **사용 하 여 HTTP** 확인란 합니다.

   ![외부 저장소 연결](media/azure-stack-integrate-security/attach-storage.png)

1. 클릭 **다음**, 요약 정보를 검토 하 고 **마침** 마법사.
1. 이제 개별 blob 컨테이너를 이동 하 고 이벤트를 다운로드할 수 있습니다.

   ![Blob 찾아보기](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>이벤트에 액세스 하려면 프로그래밍 언어를 사용 합니다.

저장소 계정에 액세스 하도록 다양 한 프로그래밍 언어를 사용할 수 있습니다. 다음 설명서를 사용 하 여 언어와 일치 하는 예를 선택 합니다.

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>장치 액세스 감사

Azure Stack에서 모든 물리적 장치 TACACS 또는 RADIUS 사용을 지원 합니다. 베이스 보드 관리 컨트롤러 (BMC) 및 네트워크 스위치에 대 한 액세스를 포함 합니다.

Azure Stack 솔루션 RADIUS 또는 기본 제공 TACACS 제공 되지 않습니다. 그러나 솔루션 유효성이 확인 된 시장에서 기존 RADIUS 또는 TACACS 솔루션의 사용 가능한 사용을 지원 하도록 합니다.

만 RADIUS를 위한 MSCHAPv2 유효성을 검사 했습니다. 이 RADIUS를 사용 하 여 가장 안전한 구현을 나타냅니다.
Azure Stack 솔루션에 포함 된 장치에서 TACAS 또는 RADIUS를 사용 하도록 설정 하려면 OEM 하드웨어 공급 업체에 문의 하세요.

## <a name="syslog"></a>syslog

Azure Stack에서 모든 물리적 장치는 Syslog 메시지를 보낼 수 있습니다. Syslog 서버를 사용 하 여 azure Stack 솔루션을 제공 하지 마십시오. 그러나 솔루션 유효성이 확인 된 시장에서 사용할 수 있는 기존 Syslog 솔루션에 보내는 메시지를 지원 하도록 합니다.

Syslog 대상 주소는 배포의 경우 수집 되는 선택적 매개 변수 이지만 추가할 수 있습니다 배포 후.

## <a name="next-steps"></a>다음 단계

[서비스 정책](azure-stack-servicing-policy.md)
