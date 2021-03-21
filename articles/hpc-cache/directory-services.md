---
title: Azure HPC 캐시에서 확장 그룹 사용
description: Azure HPC 캐시의 저장소 대상에 대 한 클라이언트 액세스를 위한 디렉터리 서비스를 구성 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563379"
---
# <a name="configure-directory-services"></a>디렉터리 서비스 구성

**디렉터리 서비스** 설정을 사용 하면 Azure HPC 캐시가 외부 소스를 사용 하 여 백 엔드 저장소에 액세스 하는 사용자를 인증할 수 있습니다.

워크플로에 NFS 개 이상의 그룹 구성원 인 클라이언트가 NFS 저장소 대상 및 클라이언트를 포함 하는 경우 **확장 그룹** 을 사용 하도록 설정 해야 할 수 있습니다.

확장 된 그룹을 사용 하도록 설정 하는 단추를 클릭 한 후에는 Azure HPC 캐시에서 사용자 및 그룹 자격 증명을 가져오는 데 사용할 원본을 선택 해야 합니다.

* [Active Directory](#configure-active-directory) -외부 Active Directory 서버에서 자격 증명을 가져옵니다. 이 작업에 Azure Active Directory를 사용할 수 없습니다.
* [플랫 파일](#configure-file-download) - `/etc/group` `/etc/passwd` 네트워크 위치에서 및 파일을 다운로드 합니다.
* [Ldap](#configure-ldap) -Ldap (Lightweight Directory Access Protocol) 호환 원본에서 자격 증명을 가져옵니다.

> [!NOTE]
> 캐시는 해당 보안 서브 네트워크 내에서 그룹 정보 원본에 액세스할 수 있는지 확인 합니다.<!-- + details/examples -->

**다운로드 한 사용자 이름** 필드에는 가장 최근의 그룹 정보 다운로드 상태가 표시 됩니다.

![확장 된 그룹에 대해 예 옵션을 선택 하 고 다운로드 소스 열기로 레이블이 지정 된 드롭다운 메뉴를 사용 하 여 포털의 디렉터리 서비스 페이지 설정 페이지 스크린샷](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Active Directory 구성

이 섹션에서는 외부 Active Directory (AD) 서버에서 사용자 및 그룹 자격 증명을 가져오기 위해 캐시를 설정 하는 방법을 설명 합니다.

**Active directory 세부 정보** 에서 다음 값을 제공 합니다.

* **기본 DNS** -캐시에서 AD 도메인 이름을 확인 하는 데 사용할 수 있는 도메인 이름 서버의 IP 주소를 지정 합니다.

* **보조 DNS** (선택 사항)-주 서버를 사용할 수 없는 경우 사용할 이름 서버의 주소를 입력 합니다.

* **AD DNS 도메인 이름** -자격 증명을 가져오기 위해 캐시가 가입할 ad 서버의 정규화 된 도메인 이름을 제공 합니다.

* **캐시 서버 이름 (컴퓨터 계정)** -AD 도메인에 가입할 때이 HPC 캐시에 할당 되는 이름을 설정 합니다. 이 캐시로 인식 하기 쉬운 이름을 지정 합니다. 이름은 최대 15 자까지 지정할 수 있으며 대문자 또는 소문자, 숫자 및 하이픈 (-)을 포함할 수 있습니다.

**자격 증명** 섹션에서 Azure HPC 캐시가 ad 서버에 액세스 하는 데 사용할 수 있는 ad 관리자 사용자 이름과 암호를 제공 합니다. 이 정보는 저장 될 때 암호화 되며 쿼리할 수 없습니다.

페이지 맨 위에 있는 단추를 클릭 하 여 설정을 저장 합니다.

![Active Directory 값이 채워진 다운로드 세부 정보 섹션의 스크린샷](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>파일 다운로드 구성

이러한 값은 사용자 및 그룹 정보를 사용 하 여 파일을 다운로드 하려는 경우에 필요 합니다. 파일은 표준 Linux/UNIX 및 형식 이어야 합니다 `/etc/group` `/etc/passwrd` .

* **사용자 파일 uri** -파일의 전체 uri를 입력 `/etc/passwrd` 합니다.
* **그룹 파일 uri** -파일의 전체 uri를 입력 `/etc/group` 합니다.

![플랫 파일 다운로드에 대 한 다운로드 세부 정보 섹션의 스크린샷](media/group-download-details-file.png)

## <a name="configure-ldap"></a>LDAP 구성

비 AD LDAP 원본을 사용 하 여 사용자 및 그룹 자격 증명을 가져오려면 이러한 값을 입력 합니다. 이러한 값에 대 한 도움이 필요한 경우 LDAP 관리자에 게 문의 하세요.

* **Ldap 서버** -사용할 ldap 서버의 정규화 된 도메인 이름 또는 IP 주소를 입력 합니다. <!-- only one, not up to 3 -->

* **Ldap 기본 dn** -ldap 도메인의 기본 고유 이름을 DN 형식으로 지정 합니다. 기본 DN을 모르는 경우 LDAP 관리자에 게 문의 하세요.

서버 및 기본 DN은 LDAP 작업을 수행 하는 데 필요한 유일한 설정 이지만 추가 옵션을 사용 하면 더 안전 하 게 연결할 수 있습니다.

![디렉터리 서비스 페이지 설정 페이지의 LDAP 구성 영역에 대 한 스크린샷](media/group-download-details-ldap.png)

**보안 액세스** 섹션에서 LDAP 연결에 대 한 암호화 및 인증서 유효성 검사를 사용 하도록 설정할 수 있습니다. **예** 를 클릭 하 여 암호화를 사용 하도록 설정 하면 다음과 같은 옵션을 사용할 수 있습니다.

* **인증서 유효성 검사** -이 설정을 설정 하면 아래 URI 필드의 인증 기관에 대해 LDAP 서버의 인증서를 확인 합니다.

* **CA 인증서 URI** -신뢰할 수 있는 인증서의 경로를 지정 합니다. 이는 CA 유효성 검사 인증서 또는 자체 서명 된 인증서에 대 한 링크 일 수 있습니다. 이 필드는 외부에서 유효성을 검사 한 인증서 설정을 사용 하는 데 필요 합니다.

* **인증서 자동 다운로드** -이러한 설정을 전송 하는 즉시 인증서를 다운로드 하려는 경우 **예** 를 선택 합니다.

LDAP 보안을 위해 정적 자격 증명을 사용 하려는 경우 **자격 증명** 섹션에 입력 합니다. 이 정보는 저장 될 때 암호화 되며 쿼리할 수 없습니다.

* **바인드 DN** -LDAP 서버에 인증 하는 데 사용할 바인딩 고유 이름을 입력 합니다. (DN 형식을 사용 합니다.)
* **바인딩 암호** -바인드 DN에 대 한 암호를 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [AZURE HPC 캐시 탑재](hpc-cache-mount.md) 의 클라이언트 액세스에 대해 자세히 알아보기
* 자격 증명이 제대로 다운로드 되지 않으면 관리자에 게 자격 증명의 원본을 문의 하십시오. 필요한 경우 [지원 티켓](hpc-cache-support-ticket.md) 을 엽니다.
