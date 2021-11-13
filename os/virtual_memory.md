# 가상메모리
- 메인 메모리의 크기가 한정되어 있기 때문에 물리적인 메모리 크기보다 큰 프로세스를 실행시킬 수 없다.
- 가상메모리란 메인 메모리보다 크기가 큰 프로세스를 실행시키기 위해 나온 방법이다.
- `프로세스 전체가 메모리 내에 올라오지 않더라도 실행이 가능하도록 하는 기법을 통틀어 가상모메리 라고 한다.`
- `필요한 부분만 메모리에 올림으로써 메모리에 올라가는 프로세스의 크기를 줄이는 요구페이징 기법을 사용한다.`

### 가상메모리가 하는 일
- 가상 메모리는 실제 물리 메모리 개념과 사용자의 논리 메모리 개념을 분리 한 것이다.
- 따라서 작은 메모리 공간을 가지고도 얼마든지 큰 가상주소 공간을 프로그래머에게 제공할 수 있다.

### 요구페이징
- 프로그램 실행 시작 시 `프로그램 전체를 디스크에서 물리 메모리에 적재하는 대신, 초기에 필요한 것들만 적재하는 전략`을 요구페이징이라 하며, 가상 메모리 시스템에서 많이 사용된다.
  - 가상메모리는 대개 페이지로 관리된다.
  - 요구페이징을 사용하는 가상메모리에서는 실행과정에서 필요해질 때 페이지들이 적재된다. 따라서 한번도 접근되지 않은 페이지는 물리 메모리에 적재되지 않는다.

## 페이지 교체 알고리즘

### 페이제 교체
- 프로세스의 동작에 필요한 페이지를 요청하는 과정에서 page fault(페이지 부재)가 발생하게 되면, 원하는 페이지를 보조저장장치에서 가져온다.
- 하지만 물리 메모리가 모두 사용중인 상황이라면 페이지 교체가 이뤄져야 한다.

### 페이지 교체 흐름
- 디스크에서 필요한 페이지의 위치를 찾는다.
- 빈 페이지 프레임을 찾는다.
  - 페이지 교체 알고리즘을 통해 희생될 페이지를 고른다.
  - 희생될 페이지를 디스크에 기록하고, 관련 페이지 테이블을 수정한다.
- 새롭게 비워진 페이지 테이블 내 프레임에 새 페이지를 읽어오고, 프레임 테이블을 수정한다.
- 사용자 프로세스 재시작

![image](https://user-images.githubusercontent.com/18106839/141488458-457becbc-702e-4c0a-a029-3a637a520ec4.png)

- 다 -> 가 -> 마 ->  라 -> 나

### 페이지 교체 알고리즘

#### FIFO

- 물리메모리에 들어온 페이지 순서대로 페이지 교체 시점에 먼저 나가게 된다.
- 장점
  - 이해하기 쉽고, 프로그래밍도 쉽다.
- 단점
  - 오래된 페이지가 불필요한 정보를 포함한다고 보장할 수 없다.
  - 처음부터 호라발하게 사용되는 페이지를 교체해서 부재율을 높일 수 있다.

#### Optimal Page Replacement - 최적알고리즘
- 앞으로 가장 오랫동안 사용되지 않을 페이지를 찾아 교체하는 것
- 장점
  - 가장 낮은 페이지 부재율을 보장한다.
- 단점
  - 구현이 어렵다. 모든 프로세스의 메모리 참조 계획을 미리 파악할 수 없기 때문이다.

#### LRU
- 최적 알고리즘의 근사 알고리즘. 가장 오랫동안 사용되지 않은 페이지를 선택하여 교체한다.

#### LFU
- 참조 횟수가 가장 적은 페이지를 교체한다. 활발하게 사용되는 페이지는 참조 횟수가 많아질 거라는 가정에서 만들어 졌다.
- 특징
  - 어떤 프로세스가 특정 페이지를 집중적으로 사용하다가 다른 기능을 사용하게 되면 더 이상 사용하지 않아도 계속 메모리에 머물게 되어, 초기 가정에 어긋날 수 있다.

#### MFU
- 참조 횟수가 가장 적은 페이지가 최근에 메모리에 올라왔꼬, 앞으로 계속 사용될 것이라는 가정에 기반한다.
    