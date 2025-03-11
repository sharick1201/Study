3.5(수)

우리가 사용하는 서버, 데스크탑, 모바일, 임베디드 시스템... 의 구조(계층)는 어떻게 되어있는가? 크게 보면 아래 그림과 같다.
![[스크린샷 2025-03-10 오후 1.23.31.png]]

우리가 짠 코드가 도당최 어떤 식으로 돌아가는 것인가? 라는 물음에 대한 답으로, 위 그림에 있는 계층 중 운영체제(Operating System, OS)를 공부함으로써 한 번 알아보자.

그런데... 운영체제란 무엇인가?

#### 운영체제란?
##### 정의
* Program the acts as an intermediary between a user of computer and the computer hardware (A.Silberschatz(공룡책 저자))
* Programs the make the hardware usable(Dietel)
	* <font color="#a5a5a5">즉, 하드웨어 자원을 효율적으로 관리하고, 사용자에게 필요한 기능을 제공한다.</font>

<font color="#a5a5a5">⇒ 컴퓨터 사용자와 컴퓨터 하드웨어 간의 중재자 역할이다. 사용자가 컴퓨터에 직접 접근하지 않고도 컴퓨터를 사용할 수 있도록 만드는 것이 운영체제이다. </font>

<font color="#a5a5a5">즉, 운영체제는 <u>하드웨어를 관리하고 제어하는 소프트웨어</u>이다.</font>

##### 왜 운영체제를 배우는가?(목적)
1. 새로운 하드웨어를 만들고 작동시키기 위해
	* <font color="#a5a5a5">운영체제를 통해서 하드웨어와 소프트웨어가 상호작용 하므로, 원하는 소프트웨어에 최적화된 하드웨어를 만들고 작동시킬 때, 운영체제에 대한 지식이 도움이 된다.</font>
2. 기능을 추가, 수정, 향상시키기 위해
	* <font color="#a5a5a5">ex. 시스템 호출을 통해 사용자 정의 프로그램을 작성하거나, 운영체제의 기능을 확장하는 방법을 배울 수 있다.</font>
3. 성능을 fine-tuning하기 위해
	* <font color="#a5a5a5">CPU, 메모리, 입출력 장치 등의 자원을 효과적으로 관리함으로써 시스템의 응답 속도와 처리 능력을 향상시킬 수 있다.</font>
		* <font color="#a5a5a5">ex.프로세스 우선순위를 조정하거나 메모리 관리를 최적화하는 방법을 배울 수 있다.</font>
	* <font color="#9bbb59">fine-tuning 한다는게 무슨 말인가?</font>
		* <font color="#a5a5a5">시스템의 성능을 극대화하기 위해 세밀히 조정하고 최적화한다는 의미이다.</font>
		* <font color="#a5a5a5">시스템 구성 요소(CPU, 메모리 등)를 분석하고 최적의 성능을 위하여 자원을 효율적으로 활용하도록 조작하는 행위.</font>
		* <font color="#9bbb59">여기서 성능은 무엇을 의미하는가?</font>
			* <font color="#a5a5a5">기본적으로 작업 처리 속도, 응답 시간, 자원 활용도, 동시 처리 능력, 신뢰성 및 안정성 등이 해당된다.</font>
			* <font color="#a5a5a5">성능에 해당하는 항목들은 요구사항에 따라 우선순위나 고려사항이 달라지겠다.</font>
		* <font color="#9bbb59">성능을 올리면 뭐가 좋은가?</font>
			*  <font color="#a5a5a5">사용자 경험 & 시스템 효율성이 향상한다. </font>
		* <font color="#9bbb59">작업 처리 속도랑 응답 시간은 다른 개념인가?</font>
			* <font color="#a5a5a5">다른 개념이다.</font>
			* <font color="#a5a5a5"><u>처리 속도(Throughput)</u></font>
				* <font color="#a5a5a5">단위 시간 당 시스템이 처리할 수 있는 작업의 양</font>
				* <font color="#a5a5a5">보통 초당 처리할 수 있는 작업 수(작업 단위의 예: 트랜잭션, 요청 등)로 측정한다.</font>
				* <font color="#a5a5a5">많은 작업이 동시에 처리할 수 있는 능력을 나타낸다. 대규모 작업에서 중요하게 여겨진다.</font>
			* <font color="#a5a5a5"><u>응답 시간(Response Time</u>)</font>
				* <font color="#a5a5a5">사용자가 요청을 보낸 시점부터, 시스템이 그 요청에 대한 결과를 반환할 때까지 걸리는 시간; 사용자의 관점에서 느끼는 지연 시간</font>
				* <font color="#a5a5a5">시스템의 반응 속도를 나타낸다. 사용자 경험에서 중요하게 여겨진다.</font>

##### 운영체제의 목표

1. 자원의 효율적인 사용(성능)
	* 성능에 영향을 미치는 병목 현상을 피한다.
		* <font color="#9bbb59">운영체제에서는 언제 병목 현상이 일어나는가?</font>
			* <font color="#a5a5a5"><u>CPU 병목 현상</u></font>
				* <font color="#a5a5a5">특정 프로세스가 CPU 리소스를 독점</font>
				* <font color="#a5a5a5">CPU 사용률 100%일 때</font>
			* <font color="#a5a5a5"><u>메모리 병목 현상</u></font>
				* <font color="#a5a5a5">프로세스가 필요한 메모리를 확보 못함</font>
				* <font color="#a5a5a5">여러 응용 프로그램이 동시에 실행됨 → 메모리 사용량 증가 → 전체 시스템 메모리 부족 → 스와핑 현상 발생할 때</font>
					* <font color="#9bbb59">스와핑 현상?</font>
						* <font color="#a5a5a5">메모리 부족 상황에서, 프로세스를 RAM에서 보조 저장 장치(디스크)로 이동시키는 과정</font>
						* <font color="#a5a5a5">빈번한 스와핑은 성능 저하를 유발한다.</font>
			* <font color="#a5a5a5"><u>디스크 I/O 병목 현상</u></font>
				* <font color="#a5a5a5">디스크 읽기/쓰기 속도 저하 또는 디스크 과부하</font>
				* <font color="#a5a5a5">대량 파일 복사 또는 대규모 데이터베이스 쿼리 실행할 때</font>
			* <font color="#a5a5a5"><u>네트워크 병목 현상</u></font>
				* <font color="#a5a5a5">로컬 네트워크에서 데이터 전송 속도 느리거나 대역폭이 부족</font>
				* <font color="#a5a5a5">여러 장치가 동일한 네트워크 사용할 때</font>
			* <font color="#a5a5a5"><u>프로세스 간 경쟁</u></font>
				* <font color="#a5a5a5">여러 프로세스가 동일한 자원에 접근</font>
				* <font color="#a5a5a5">복수의 프로세스가 동일한 자원에 접근하려 할 때, Lock으로 인해 대기</font>
					* <font color="#9bbb59">Lock?</font>
						* <font color="#a5a5a5">락을 가진 프로세스만 특정 자원에 접근할 수 있다. 이때 다른 프로세스는 대기한다.</font>
						* <font color="#a5a5a5">데이터 무결성 유지, 경쟁 조건(race condition, 동시 접근 시 접근 순서에 따라 결과가 달라지는 현상) 방지</font>
	* 웬만하면, 모든 구성 요소들이 바쁘게 일하고 있도록 만드는 게 좋다.
		* <font color="#a5a5a5">쉬고있는 구성요소가 별로 없도록 할 것.</font>
		
2. 사용자 편의성과 생산성
	* 기능 못지않게 중요하다.
	* 기계보다 사용자가 더 비싸다.
		* <font color="#a5a5a5">사용자가 기계를 쉽고 빠르게 사용할 수 있어야 한다.</font>
	* 기능을 가능한 한, 최대한 효율적으로 제공한다.
		
3. 가용성과 신뢰성
	* 컴퓨터 시스템은 critical하다.
		* <font color="#a5a5a5">비즈니스 운영의 핵심이고, 서비스 제공의 필수 요소이다. 시스템의 가용성과 신뢰성은 비즈니스/서비스 운영의 성패에 중요한 역할을 한다.</font>
	* 서버는 죽으면 안 된다. 실패한 시스템은 회사가 실패했음을 의미한다.

#### 운영체제 개요
##### 추상적 개요

| Applications                                            |
| ------------------------------------------------------- |
| <font color="#c0504d">OS</font> → 하드웨어와 어플리케이션이 접해있는 곳! |
| Hardware<br>(CPU/Memory/Storage/Network ...)            |

##### 구체적 개요