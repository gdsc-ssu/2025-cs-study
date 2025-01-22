## 메모리 주소
- 논리적 주소(logical address) 
	- process가 memory에 적재되기 위한 독자적인 주소 공간
	- 각 process마다 독립적으로 할당되며, 0번지부터 시작
- 물리적 주소(physical address)
	- process가 실제로 메모리에 적재되는 위치
- 주소 바인딩(address binding)
	- CPU가 기계어 명령을 수행하기 위해 process의 논리적 주소가 실제 물리적 메모리의 어느 위치에 매핑되는지 확인하는 과정

---

## 가상 메모리(Virtual Memory)
- process 전체가 메모리에 올라오지 않더라도 실행이 가능하도록 하는 기법
- 프로그램을 여러 개의 작은 블록 단위로 나누어서 Virtual Memory(가상기억장치)에 보관해놓고, 프로그램 실행 시 요구되는 블록만 주기억장치에 불연속적으로 할당하여 처리
- 사용자 프로그램이 물리적 메모리보다 커져도 실행이 가능하다

- 운영체제는 프로그램의 논리적 주소 영역에서 필요한 부분만 물리적 메모리에 적재하고, 직접적으로 필요하지 않은 메모리 공간은 디스크(Swap 영역)에 저장한다

![image](https://github.com/user-attachments/assets/576e9153-99dd-4c04-a233-5384e74cdf3e)

### 요구 페이징(Demand Paging)
- 당장 사용될 주소 공간을 page 단위로 메모리에 적재하는 방법
- 당장 실행에 필요한 page만을 메모리에 적재하기 때문에 메모리 사용량이 감소하고, 프로세스 전체를 메모리에 적재하는 입출력 오버헤드도 감소하는 장점이 있다

### Page fault
- 유효/무효 비트(valid/invalid bit)를 두어 각 page가 메모리에 존재하는지 표시한다
- CPU가 무효 page에 접근하면 주소 변환을 담당하는 하드웨어인 MMU가 page fault trap을 발생시킨다
- page fault가 발생하면, 요청된 page를 디스크에서 메모리로 가져온다

![image](https://github.com/user-attachments/assets/f081ee80-d323-437a-9eb6-d4ed13ba6e6d)

#### page 교체 알고리즘(replacement algorithm)
- 페이지 교체(page replacement): page fault가 발생하면 요청된 page를 디스크 -> 메모리로 가져온다. 이때 메모리에 올라와 있는 page를 디스크로 옮겨서 메모리 공간을 확보한다
- page 교체 알고리즘은 최대한 page fault가 적게 일어나도록 하기 위해 참조될 가능성이 적은 page를 선택한다

| 알고리즘                       | 설명                                                 |
| -------------------------- | -------------------------------------------------- |
| FIFO(First In First Out)   | 메모리에 올라온지 가장 오래된 page를 교체한다                        |
| 최적 페이지 교체                  | 앞으로 가장 오랫동안 사용되지 않을 page를 찾아 교체한다. 실제구현은 어렵다       |
| LRU(Least Recently Used)   | 가장 오랫동안 사용되지 않은 page를 교체한다                         |
| LFU(Least Frequently Used) | 참조 횟수가 가장 적은 page를 교체한다. 비용 대비 성능이 좋지 않아 잘 쓰이진 않는다 |

---

## Paging
- 가상기억장치에 보관되어 있는 프로그램과 주기억장치의 영역을 동일한 크기로 나눈 후 나눠진 프로그램을 동일하게 나눠진 주기억장치의 영역에 적재시켜 실행하는 기법
- page: 프로그램을 일정한 크기로 나눈 단위
- page frame: 페이지 크기로 일정하게 나누어진 주기억장치의 단위
- page map table: 가상기억장치(논리적 가상 주소)에서 주기억장치(물리적 실 기억 주소)로의 주소 변환을 위한 페이지의 위치 정보를 갖고 있는 테이블

![image](https://github.com/user-attachments/assets/14def9a2-5208-40e1-b8f1-b3739bc263de)

![image](https://github.com/user-attachments/assets/d9b538a4-c5a6-4000-92ac-738493db68a6)

- 외부 단편화 발생 X, 내부 단편화 발생 O
	- 페이지의 크기가 4KB이고 사용할 프로그램이 17KB라면 프로그램은 페이지 단위로 4KB씩 나누어지게 되므로, 마지막 페이지의 실제 용량은 1KB(17KB - 4 * 4KB)가 되고, 이것이 주기억장치에 적재되면 3KB의 내부 단편화가 발생하게 된다
- 페이지 맵 테이블의 사용으로 비용이 증가하고 처리 속도가 감소된다

### 페이지의 크기
##### 페이지의 크기가 작을 경우
- 페이지 단편화가 감소, 한 개의 페이지를 주기억장치로 이동시키는 시간 감소
- 페이지 맵 테이블의 크기가 커지고 매핑 속도가 늦어짐
- 디스크 접근 횟수 증가로 입출력 시간 증가
##### 페이지의 크기가 클 경우
- 페이지 맵 테이블의 크기가 작아지고 매핑 속도 증가
- 디스크 접근 횟수 감소로 입출력 효율성 증가
- 페이지 단편화 증가, 한 개의 페이지를 주기억장치로 이동시키는 시간 증가

---

## Segmentation
- 가상기억장치에 보관되어 있는 프로그램을 code, data, heap, stack등의 기능(의미)단위로 나눈 후 주기억장치에 적재시켜 실행시키는 기법
- 목적: 기억공간의 절약을 위해 사용
- segment: 프로그램의 배열이나 함수 등과 같은 논리적인 크기로 나눈 단위, 각 세그먼트는 고유한 이름과 크기를 가진다
- segment map table : 주소 변환을 위해 세그먼트가 존재하는 위치 정보를 갖고 있는 테이블
- segment는 그 크기가 균일하지 않기 때문에 논리적 주소가 <segment 번호, offset>으로 표현된다
- offset 값이 table의 limit 값보다 크면, 해당 segment를 넘어가므로 segmentation fault 오류가 발생한다

![image](https://github.com/user-attachments/assets/b89361f8-e285-4db9-84cd-07be5ebddafd)

- 내부 단편화 발생 X, 외부 단편화 발생 O
	- 100MB 크기의 메모리에 프로세스 A(20MB), B(30MB), C(40MB)가 순서대로 할당되었다. 프로세스 B가 종료되어 메모리를 반환했을 때 메모리 상태는 A(20MB), 빈 공간(30MB), C(40MB), 빈 공간(10MB)이 된다. 이때 새로운 프로세스 D가 35MB의 메모리를 요구한다. 총 사용 가능한 메모리는 40MB(30MB + 10MB)로 충분하지만, 연속된 35MB 공간이 없어 D를 할당할 수 없다

---

## Paged Segmentation
- segmentation을 기본으로 하되 이를 다시 동일 크기의 page로 나누어 물리 메모리에 할당하는 메모리 관리 기법
- 프로그램을 의미 단위의 segment로 나누고 개별 segment의 크기를 page의 배수가 되도록 하는 방법
- segmentation 기법에서 발생하는 외부 단편화 문제를 해결하고, 동시에 segment 단위로 process 간의 공유나 process 내의 접근 권한 보호가 이루어지도록 해서 paging 기법의 단점을 해결