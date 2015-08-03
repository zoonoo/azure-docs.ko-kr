<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
</tr>
<tr>
   <td valign="middle"><p>데이터베이스 크기</p></td>
   <td valign="middle"><p>성능 수준에 따라 달라집니다<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>로그인</p></td>
   <td valign="middle"><p>성능 수준에 따라 달라집니다<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>메모리 사용량</p></td>
   <td valign="middle"><p>20초 이상에 16MB 메모리 부여</p></td>
</tr>
<tr>
   <td valign="middle"><p>세션</p></td>
   <td valign="middle"><p>성능 수준에 따라 달라집니다<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Tempdb 크기</p></td>
   <td valign="middle"><p>5GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>트랜잭션 기간</p></td>
   <td valign="middle"><p>24시간<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>트랜잭션 당 잠금</p></td>
   <td valign="middle"><p>1백만</p></td>
</tr>
<tr>
   <td valign="middle"><p>트랜잭션 당 크기</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>트랜잭션 당 사용되는 총 로그 공간의 백분율</p></td>
   <td valign="middle"><p>20%</p></td>
</tr>
<tr>
   <td valign="middle"><p>최대 동시 요청(작업자 스레드)</p></td>
   <td valign="middle"><p>성능 수준에 따라 달라집니다<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>SQL 데이터베이스에 기본, 표준 및 프리미엄과 같은 성능 계층이 있습니다. 또한 표준 및 프리미엄은 성능 수준으로 구분됩니다. 계층 및 서비스 수준 당 자세한 제한은 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](https://msdn.microsoft.com/library/azure/dn741336.aspx)을 참조하십시오.

<sup>2</sup>트랜잭션이 기본 시스템 작업에서 필요한 리소스를 잠근 경우 최대 시간은 20초입니다.

<!---HONumber=July15_HO4-->