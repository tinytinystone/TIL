# stack, queue

## stack과 queue의 정의

스택은 데이터 삽입과 삭제가 한쪽 끝에서만 일어나는 자료구조입니다. 즉, 처음 들어간 것이 가장 마지막에 나오는 구조로 FILO(First In Last Out)라고 부릅니다. 큐는 한쪽 끝에서는 데이터 삽입이, 다른 쪽에서는 데이터 삭제만 일어나는 구조입니다. 처음 들어간 데이터가 가장 먼저 나오는 구조이며, FIFO(First In First Out)라고도 부릅니다.

## overflow, underflow가 발생하는 이유와 해결 방안

오버플로는 할당된 저장공간을 초과해서 더이상 데이터 삽입이 불가능한 경우 발생합니다. 스택 혹은 큐 공간이 가득 찬 상태에서 데이터를 추가하려고 시도하면 오버플로가 발생합니다. 언더플로는 데이터가 존재하지 않는데 삭제하려고 할 때 발생합니다. 스택 혹은 큐 저장공간에 데이터가 없는 상황에서 데이터를 꺼내려고 하면 언더플로가 발생합니다.

큐 자료구조에서 오버플로와 언더플로를 해결할 수 있는 방법에 대해 적어보겠습니다. 한정한 공간에서 양쪽 끝에서 데이터를 삽입, 삭제하다 보면 데이터를 넣을 수 있는 공간이 존재함에도 한쪽 끝에 데이터가 차 있거나, 제거할 데이터가 존재함에도 다른 끝에 데이터가 없는 경우가 생길 수 있습니다. 이를 해결하려면 포인터와 데이터를 모두 옮겨주는 방법이 있습니다. 하지만 이 경우에는 공간 낭비가 발생합니다.

공간 낭비도 해결하고 오버플로, 언더플로를 해결하는 방법은 환형 큐(circular queue)를 이용하는 방법이 있습니다. 환형 큐는 큐의 처음과 끝이 이어져서 고리 모양으로 생긴 데이터 구조입니다. 중간에 원소가 비어도 데이터 삽입이 가능합니다. 물론 환형 큐에서도 큐가 꽉 차 있거나 비어 있다면 문제가 될 수 있지만, 어쨌든 데이터 낭비를 막을 수 있고 포인터와 데이터를 확인할 필요가 없다는 장점이 있습니다.