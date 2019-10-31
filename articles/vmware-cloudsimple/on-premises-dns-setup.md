---
title: CloudSimple의 Azure VMware 솔루션-CloudSimple 사설 클라우드에 대 한 DNS 구성
description: 온-프레미스 워크스테이션에서 CloudSimple 사설 클라우드의 vCenter 서버에 액세스 하기 위해 DNS 이름 확인을 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 72bf461dc882b9a097e59e8ecf40b0f71b2cc1f2
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163147"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>온-프레미스 워크스테이션에서 사설 클라우드 vCenter 액세스의 이름 확인에 대 한 DNS 구성

온-프레미스 워크스테이션에서 CloudSimple 사설 클라우드의 vCenter 서버에 액세스 하려면 IP 주소 뿐만 아니라 호스트 이름을 사용 하 여 vCenter 서버 주소를 지정할 수 있도록 DNS 주소 확인을 구성 해야 합니다.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>사설 클라우드에 대 한 DNS 서버의 IP 주소를 가져옵니다.

1. [Cloudsimple 포털](access-cloudsimple-portal.md)에 로그인 합니다.

2. **리소스** > **사설** 클라우드로 이동 하 고 연결 하려는 사설 클라우드를 선택 합니다.

3. 사설 클라우드의 **요약** 페이지에 있는 **기본 정보**에서 사설 클라우드 DNS 서버 IP 주소를 복사 합니다.

    ![사설 클라우드 DNS 서버](media/private-cloud-dns-server.png)


DNS 구성에 대해 이러한 옵션 중 하나를 사용 합니다.

* [DNS 서버에서 *. cloudsimple.io에 대 한 영역을 만듭니다.](#create-a-zone-on-a-microsoft-windows-dns-server)
* [온-프레미스 DNS 서버에서 조건 전달자를 만들어 *. cloudsimple.io를 확인 합니다.](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>DNS 서버에서 *. cloudsimple.io에 대 한 영역을 만듭니다.

스텁 영역으로 영역을 설정 하 고 이름 확인을 위해 사설 클라우드의 DNS 서버를 가리킬 수 있습니다. 이 섹션에서는 BIND DNS 서버 또는 Microsoft Windows DNS 서버를 사용 하는 방법에 대 한 정보를 제공 합니다.

### <a name="create-a-zone-on-a-bind-dns-server"></a>BIND DNS 서버에 영역 만들기

구성할 특정 파일 및 매개 변수는 개별 DNS 설정에 따라 달라질 수 있습니다.

예를 들어 기본 바인드 서버 구성의 경우 DNS 서버에서/etc/named.conf 파일을 편집 하 고 다음 영역 정보를 추가 합니다.

```
zone "cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS 서버에서 영역 만들기

1. DNS 서버를 마우스 오른쪽 단추로 클릭 하 고 **새 영역**을 선택 합니다. 
  
    ![새 영역](media/DNS01.png)
2. **스텁 영역** 을 선택 하 고 **다음**을 클릭 합니다.

    ![새 영역](media/DNS02.png)
3. 사용자 환경에 따라 적절 한 옵션을 선택 하 고 **다음**을 클릭 합니다.

    ![새 영역](media/DNS03.png)
4. **전방 조회 영역** 을 선택 하 고 **다음**을 클릭 합니다.

    ![새 영역](media/DNS01.png)
5. 영역 이름을 입력 하 고 **다음**을 클릭 합니다.

    ![새 영역](media/DNS05.png)
6. CloudSimple 포털에서 가져온 사설 클라우드에 대 한 DNS 서버의 IP 주소를 입력 합니다.

    ![새 영역](media/DNS06.png)
7. 필요에 따라 **다음** 을 클릭 하 여 마법사 설치를 완료 합니다.

## <a name="create-a-conditional-forwarder"></a>조건부 전달자 만들기

조건부 전달자는 모든 DNS 이름 확인 요청을 지정 된 서버에 전달 합니다. 이 설정을 사용 하면 *. cloudsimple.io에 대 한 모든 요청이 사설 클라우드에 있는 DNS 서버로 전달 됩니다. 다음 예에서는 여러 유형의 DNS 서버에서 전달자를 설정 하는 방법을 보여 줍니다.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>바인드 DNS 서버에 조건부 전달자 만들기

구성할 특정 파일 및 매개 변수는 개별 DNS 설정에 따라 달라질 수 있습니다.

예를 들어 기본 바인드 서버 구성의 경우 DNS 서버에서/etc/named.conf 파일을 편집 하 고 다음과 같은 조건부 전달 정보를 추가 합니다.

```
zone "cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS 서버에서 조건부 전달자 만들기

1. DNS 서버에서 DNS 관리자를 엽니다.
2. **조건부 전달자** 를 마우스 오른쪽 단추로 클릭 하 고 옵션을 선택 하 여 새 조건부 전달자를 추가 합니다.

    ![조건부 전달자 1 Windows DNS](media/DNS08.png)
3. 사설 클라우드에 dns 도메인 및 DNS 서버의 IP 주소를 입력 하 고 **확인**을 클릭 합니다.
