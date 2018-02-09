


**최근 문서 업데이트**: 1월 22일 오후 3시 PST

투기적 실행 사이드 채널 공격으로 알려진 최근 공개된 [새로운 종류의 CPU 취약성](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)으로 인해 고객은 명확하게 이해하기 위해 질문하게 되었습니다.  

Azure를 실행하고 고객 워크로드를 서로 격리하는 인프라가 보호됩니다.  즉, Azure에서 실행하는 다른 고객은 이러한 취약성을 사용하여 응용 프로그램을 공격할 수 없습니다.

> [!NOTE] 
> 2018년 1월 3일에 발표된 Azure 완화는 Intel의 최신 [업데이트된 지침](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/)에 영향을 받지 않습니다. 이러한 새 정보의 결과로 고객 VM에 대한 추가 유지 관리 작업이 없습니다.
>
> 하드웨어 공급 업체로부터 마이크로코드 업데이트를 수신하는 대로 이러한 모범 사례를 계속해서 업데이트할 예정입니다. 업데이트된 지침을 다시 확인하세요.
>

## <a name="keeping-your-operating-systems-up-to-date"></a>운영 체제를 최신 상태로 유지

OS 업데이트가 Azure에서 실행되는 기타 고객으로부터 Azure에서 실행되는 응용 프로그램을 격리하지 않아도 되지만 항상 OS 버전을 최신 상태로 유지하는 것이 좋습니다. 

다음과 같은 제품에서 운영 체제를 업데이트하기 위해 권장된 조치는 다음과 같습니다. 

<table>
<tr>
<th>제품</th> <th>권장 작업 </th>
</tr>
<tr>
<td>Azure Cloud Services </td>  <td>자동 업데이트를 사용하거나 최신 게스트 OS를 실행합니다.</td>
</tr>
<tr>
<td>Azure Linux Virtual Machines</td> <td>사용 가능한 경우 운영 체제 공급자로부터 업데이트를 설치합니다. </td>
</tr>
<tr>
<td>Azure Windows Virtual Machines </td> <td>OS 업데이트를 설치하기 전에 지원되는 바이러스 백신 응용 프로그램을 실행하고 있는지 확인합니다. 호환성 정보는 바이러스 백신 소프트웨어 공급 업체에 문의하세요.<p> [1월 보안 롤업](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)을 설치합니다. </p></td>
</tr>
<tr>
<td>기타 Azure PaaS 서비스</td> <td>이러한 서비스를 사용하는 고객에게 필요한 작업은 없습니다. Azure에서는 자동으로 OS 버전을 최신 상태로 유지합니다. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>신뢰할 수 없는 코드를 실행하는 경우 추가 지침 

신뢰할 수 없는 코드를 실행하지 않으면 추가 고객 작업이 필요하지 않습니다. 신뢰할 수 없는 코드를 허용하는 경우(예: 고객 중 한 명이 응용 프로그램 내의 클라우드에서 실행할 이진 또는 코드 조각을 업로드하도록 허용) 다음 단계를 추가로 수행해야 합니다.  


### <a name="windows"></a>Windows 
또한 Windows를 사용하고 신뢰할 수 없는 코드를 호스트하는 경우 추론 실행 사이드 채널 취약성에 대해 추가적인 보호를 제공하는 KVA(커널 가상 주소) 섀도잉이라는 Windows 기능을 사용해야 합니다. 이 기능은 기본적으로 해제되어 있으며 사용하는 경우 성능에 영향을 줄 수 있습니다. 서버에서 보호를 사용하도록 설정하는 방법은 [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 지침에 따릅니다. Azure Cloud Services를 실행하는 경우 WA-GUEST-OS-5.15_201801-01 또는 WA-GUEST-OS-4.50_201801-01(1월 10일부터 사용 가능)을 실행하고 있는지 확인하고 시작 작업을 통해 레지스트리 키를 사용하도록 설정합니다.


### <a name="linux"></a>Linux
Linux를 사용하고 신뢰할 수 없는 코드를 호스팅하는 경우 KPTI(커널 페이지 테이블 격리)를 구현하는 보다 최신 버전으로 Linux를 업데이트해야 합니다. 여기서는 사용자 공간에 속한 페이지 테이블과 커널에서 사용되는 페이지 테이블을 구분합니다. 이러한 완화에는 Linux OS 업데이트가 필요하고 사용 가능한 경우 배포 공급자로부터 가져올 수 있습니다. OS 공급자는 기본적으로 보호를 활성화 또는 비활성화했는지를 알 수 있습니다.



## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [CPU 취약성으로부터 Azure 고객 보호](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)를 참조하세요.
