---
title: 클라우드간이별 Azure VMware 솔루션 - 클라우드간이플 프라이빗 클라우드를 위한 DNS 구성
description: 온-프레미스 워크스테이션에서 CloudSimple 프라이빗 클라우드에서 vCenter 서버에 액세스하기 위해 DNS 이름 확인을 설정하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246111"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>온-프레미스 워크스테이션에서 프라이빗 클라우드 vCenter 액세스에 대한 이름 확인을 위해 DNS 구성

온-프레미스 워크스테이션에서 CloudSimple 프라이빗 클라우드의 vCenter 서버에 액세스하려면 vCenter 서버가 호스트 이름과 IP 주소로 주소를 지정할 수 있도록 DNS 주소 확인을 구성해야 합니다.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>프라이빗 클라우드용 DNS 서버의 IP 주소 획득

1. [CloudSimple 포털에 로그인합니다.](access-cloudsimple-portal.md)

2. **리소스** > **프라이빗 클라우드로** 이동하여 연결할 프라이빗 클라우드를 선택합니다.

3. **기본 정보**아래의 사설 클라우드 요약 **페이지에서** 프라이빗 클라우드 DNS 서버 IP 주소를 복사합니다.

    ![프라이빗 클라우드 DNS 서버](media/private-cloud-dns-server.png)


DNS 구성에 이러한 옵션 중 하나를 사용합니다.

* [*.cloudsimple.io 대한 DNS 서버의 영역 만들기](#create-a-zone-on-a-microsoft-windows-dns-server)
* [*.cloudsimple.io 해결하기 위해 온-프레미스 DNS 서버에 조건부 전달자를 만듭니다.](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>*.cloudsimple.io 대한 DNS 서버의 영역 만들기

영역을 스텁 영역으로 설정하고 이름 확인을 위해 프라이빗 클라우드의 DNS 서버를 가리킬 수 있습니다. 이 섹션에서는 BIND DNS 서버 또는 Microsoft Windows DNS 서버 사용에 대한 정보를 제공합니다.

### <a name="create-a-zone-on-a-bind-dns-server"></a>BIND DNS 서버에서 영역 만들기

구성할 특정 파일 및 매개 변수는 개별 DNS 설정에 따라 달라질 수 있습니다.

예를 들어 기본 BIND 서버 구성의 경우 DNS 서버에서 /etc/named.conf 파일을 편집하고 다음 영역 정보를 추가합니다.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>마이크로소프트 윈도 DNS 서버에 영역 만들기

1. DNS 서버를 마우스 오른쪽 단추로 클릭하고 **새 영역을**선택합니다. 
  
    ![새 영역](media/DNS01.png)
2. **스텁 영역을** 선택하고 **다음**을 클릭합니다.

    ![새 영역](media/DNS02.png)
3. 환경에 따라 적절한 옵션을 선택하고 다음 을 **클릭합니다.**

    ![새 영역](media/DNS03.png)
4. **[보기] 영역을** 선택하고 **다음**을 클릭합니다.

    ![새 영역](media/DNS01.png)
5. 영역 이름을 입력하고 **다음을 클릭합니다.**

    ![새 영역](media/DNS05.png)
6. CloudSimple 포털에서 얻은 프라이빗 클라우드용 DNS 서버의 IP 주소를 입력합니다.

    ![새 영역](media/DNS06.png)
7. 필요에 따라 **다음을** 클릭하여 마법사 설정을 완료합니다.

## <a name="create-a-conditional-forwarder"></a>조건부 전달자 만들기

조건부 전달자는 모든 DNS 이름 확인 요청을 지정된 서버로 전달합니다. 이 설정을 사용하면 *.cloudsimple.io 대한 모든 요청이 프라이빗 클라우드에 있는 DNS 서버로 전달됩니다. 다음 예제는 다양한 유형의 DNS 서버에 전달자를 설정하는 방법을 보여 주며,

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>BIND DNS 서버에서 조건부 전달자 만들기

구성할 특정 파일 및 매개 변수는 개별 DNS 설정에 따라 달라질 수 있습니다.

예를 들어 기본 BIND 서버 구성의 경우 DNS 서버에서 /etc/named.conf 파일을 편집하고 다음 조건부 전달 정보를 추가합니다.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>마이크로소프트 윈도 DNS 서버에서 조건부 전달자 만들기

1. DNS 서버에서 DNS 관리자를 엽니다.
2. **조건부 전달기를** 마우스 오른쪽 단추로 클릭하고 새 조건부 전달기를 추가하는 옵션을 선택합니다.

    ![조건부 포워더 1 윈도우 DNS](media/DNS08.png)
3. 프라이빗 클라우드에서 DNS 서버의 DNS 도메인과 IP 주소를 입력하고 **확인을**클릭합니다.
