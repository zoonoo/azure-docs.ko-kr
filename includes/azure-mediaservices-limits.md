<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p>단일 구독의 Azure 미디어 서비스(AMS) 계정</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS 계정당 자산</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1,000,000</p></td>
</tr>
<tr>
   <td valign="middle"><p>작업당 연결된 태스크</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>태스크당 자산</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>작업당 자산</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS 계정당 작업 </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50,000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>한번에 자산과 연결된 고유 로케이터</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS 계정당 라이브 채널 </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>채널당 중지 상태인 프로그램 </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>채널당 실행 상태인 프로그램 </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>AMS 계정당 실행 상태인 스트리밍 끝점</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>스트리밍 끝점당 스트리밍 단위 </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS 계정당 인코딩 단위 </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> 이 할당량에 대 한 제한을 지원 티켓을 열어 업데이트를 요청할 수 있습니다. 제한을 증가시키려면 AMS 계정을 추가로 만들지 말고 지원 티켓을 제출 하십시오.

<sup>2</sup> 이 번호에 대기 중, 완료, 활성 및 취소 된 작업도 포함 됩니다. 삭제된 작업은 포함 되지 않습니다. **IJob.Delete** 또는 **DELETE** HTTP 요청을 사용하여 이전 작업을 삭제합니다.

<sup>3</sup> 작업 엔터티 목록을 요청할 때 요청당 최대 1,000개가 반환됩니다. 제출된 모든 작업을 추적하는 경우 [OData 시스템 쿼리 옵션](http://msdn.microsoft.com/library/gg309461.aspx)에서 설명한 대로 위쪽/건너뛰기를 사용할 수 있습니다.

<sup>4</sup> 로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 서로 다른 액세스 권한을 부여하려면 DRM(Digital Rights Management)솔루션을 사용 합니다.

<!---HONumber=July15_HO4-->