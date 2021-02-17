# TradingBot_scribble
낙서장_중요내용 안적게 주의
<hr>
* 거래량 변화추이가 불분명함. 체크를 위해서 시간대별 거래량 상위종목(1~15위) 추출프로그램 필요.
(9:00~9:30까지 1분단위. 7일간 테스트). 코스피 코스닥 각각. 
CpSysDib.CpSvr7049 이용. 
objAmount로 객체생성.
<hr>
거래량 상위종목에 대한 분할매수 방법을 생각해보자
<hr>
수정전략. 
윌리암스로 탐색후 엔빵매수, 리스트화
익일 CheckModel 가동.
<hr>
예상 장점. 전략a사용시 갭하락에 의한 관성으로 손실극대화가능성 존재. CheckModel이 이를 방지. 또한 전략a가 가져올 갭상승+관성으로인한 상승분 checkModel로 얻는것이 가능
<hr>
추가로직. 갭상승후 추가상승분없이 바로 하락할경우 매도<br>
CheckModel case3 die가 3:20 일어나니까 매도후 전략a탐색을 3:22에 해서 매수까지를 완료하면될듯. 이러면 매매자동화문제도 해결됨. 자동로그인만해결하면됨
<br>
상한가종목 필터링, 거래정지 필터링
<hr>
시가 Dscbo1.StockCur<br>
전일 <br>
3:15 --> 윌리암스 작동 <br>
프로그램종료<br>
당일 9시 CheckModel 작동<br>
케이스1: 3시 14분전 매도--> while(0) 중 3:15 --> 윌리암스 작동<br>
케이스2: 3시 14 매도 --> 윌리암스 작동
<hr>
윌리암스 모듈화 위해선 계좌내 종목 코드, 수량 가져오는 메서드만들어 CheckModel내 윌리암스 중복인자를 대체해야함
<hr>
변수 줄이려 시장가매입했으나 손실이 생김. 시장가매수 특성상 호가보다 높게 가격을 책정하게됨. 무시할수 있는 정도의 손실인지 고민필요
<br>
단일가종목 필터링 필요. 알고리즘에서 벗어남
<hr>
탐색시간이 너무 오래걸린다. 최적화필요. for문이 저차원이긴하지만 내부에 함수가 너무 많은 영향인것 같음. 미리 변수를 받아놓을 수 있는 방법을 생각하고<br>
그렇게 하였을때 시간이 얼마나 단축되는지 체크해보자
<hr>
RangeWilliam() 실행시간 854.648s 걸림. 이걸 매번 for돌릴때마다 실행중. RangeWilliam()을 미리 돌려 리스트에 저장하고 그걸 호출하자<br>
그런다해도 854초는 너무 길다. 
<hr>
multiprocessing으로 속도를 줄일수 있다. 체크해보자.<br>
pool = Pool(processes='활용 가능한 CPU 코어수')<br>
result = pool.map(정의된 함수, 병렬처리할 input 값)<br>
pool.close()<br>
pool.join()<br>
<hr>
멀티프로세스 구현할때 if __name__=='__main__': 조건문이 꼭 필요하다. 그렇지 않으면 오류 발생함
<hr>
멀티프로세싱한 데이터를 리스트에 쌓으려면 manager 객체를 이용하면 된다.<br>
https://m.blog.naver.com/stop2y/221730273927
<hr>
근본적으로 자를수 있는건 kosdaq_list임. numpy.array_split()으로 n등분해서 프로세스 8개에 분할
<hr>
pool이용대신 process이용.<br>
속도가 빨라지니 시세데이터 조회제한걸림. time.sleep()으로 적정선 찾기. 경고창뜨면 프로세스 하나가 중단되고 해당 경고창의 확인을 누르면<br>
프로세스 재실행된다. 알아서 확인을 누를방법을 생각<br>
윈도우 경고창을 제어할수 --> win32.com
에러창의 process ID를 확인하자. 작업관리자로 PID확인 가능. win32 API로 PID 제어 가능할것같음
<hr>
윌리엄 개선 필요. 코어를 거래량으로 변경후 테스트
<hr>
cmd 에서 taskkill /im 프로세스명 으로 종료신호보낼 수 있음. 파이썬에서 cmd명령을 실행할 수 있는가<br>
import os해서 os.system()으로 전달하고 이걸 멀티프로세스를 만들어 동시 실행시켜주면 될거같음
<hr>
subprocess로 대체. 콘솔 안띄우기 위해 프로세스 API를 보고 CREATE_NO_WINDOW=0x08000000 이용.<br>
subprocess.call('taskkill /im KakaoTalk.exe',creationflags=CREATE_NO_WINDOW) 이런식으로 사용하면 해결됨
<hr>
개별작동은 확인했는데 프로세스를 따로 만들어작동시키면 계속 중간에 pwintypes.com_error가 발생함

