## Divide And Conquer

입력을 더이상 나눌 수 없을 때까지 작은 문제로 순환적으로 분할하고, 분할된 문제를 각각 해결한 후, 그 해를 결합해 원래 문제의 해를 구하는 하향식 접근 방법

- 분할: 주어진 문제의 입력을 여러 개의 작은 문제로 분할
- 정복: 작은 문제를 순환적으로 분할하고, 충분히 작아졌다면 순환호출 없이 작은 문제의 해를 구함
- 결합: 해를 결합해 원래 문제의 해를 구함 (결합 단계가 없는 경우도 있음)

## 퀵 정렬

- 특정 원소(피벗)를 기준으로 두 부분 배열로 분할하여 부분배열을 정렬 (부분배열의 크기는 일정하지 않음)
- 퀵 정렬의 수행 시간은 분할함수 수행 시간 + 왼쪽 부분배열과 오른쪽 부분배열 각각에 대한 순환 호출
  - 최악의 경우
    - 피벗이 항상 최솟값(혹은 최대값)이 되었을 경우 (O(n^2))
    - 한번의 분할함수에 피벗 한 원소만 정렬되므로 n번의 분할함수 호출이 필요함
  - 최선의 경우
    - 피벗이 항상 중앙값이 되는 경우 (O(nlogn))
    - 피벗 선정의 임의성이 보장된다면 평균적인 성능을 보일 가능성이 높음
- 분할정복 3단계
  - 분할: 피벗을 기준으로 주어진 배열을 두 부분배열로 분할
  - 정복: 각 부분배열에 대해 퀵 정렬을 순환적으로 적용
  - 결합: 결합과정은 필요 없음

```js
/**
 * 
 * @param {arr} arr 정렬하고 싶은 배열
 * @param {int} left 정렬하려는 배열의 가장 왼쪽 index
 * @param {int} right 정렬하려는 배열의 가장 오른쪽 index
 */
const quickSort = (arr, left = 0, right = arr.length - 1) => {
  // 배열 길이가 1 이하인 경우 정렬할 필요 없음
  if (right <= left) return

  // 두 부분배열로 분할하기 위해 기준이 되는 피벗 값을 구한다
  const pivot = partition(arr, left, right);

  // 피벗 왼쪽 부분배열에 대해 순환호출
  quickSort(arr, left, pivot - 1);
  // 피벗 오른쪽 부분배열에 대해 순환호출
  quickSort(arr, pivot + 1, right);
};
const partition = (arr, left, right) => {
  // 가장 왼쪽 값을 피벗으로 선택
  const pivotIndex = left
  const pivotValue = arr[pivotIndex];
  // 피벗 바로 옆의 값으로 left를 이동
  left += 1
  // left와 right를 이동시키면서 pivotValue보다 작은 값과 큰 값을 구분
  while (left < right) {
    while (arr[left] < pivotValue) { left++; }
    while (arr[right] >= pivotValue) { right--; }
    if (left < right) {
      [arr[left], arr[right]] = [arr[right], arr[left]]
    } else {
      [arr[pivotIndex], arr[right]] = [arr[right], arr[pivotIndex]]
    }
  }
  // pivotValue의 최종 위치를 반환
  return right;
};
```

## binary search

- 분할: 가운데 원소를 기준으로 왼쪽 부분배열과 오른쪽 부분배열로 분할. 탐색키 x와 가운데 원소가 같으면 해당 배열의 인덱스를 반환하고 종료
- 정복: x가 가운데 원소보다 작으면 왼쪽 부분배열을, 크다면 오른쪽 부분배열을 대상으로 이진탐색을 순환 호출. 탐색 수행 시 탐색 범위가 절반으로 줄어듦.
- 결합: 이진탐색 결과가 직접 호출되므로 결합할 필요 없음.
- 성능: O(logn)
  - 입력이 정렬된 배열에 한해서 적용 가능
  - 데이터 삽입/삭제 연산 수행 시 데이터 이동이 발생
    - 삽입/삭제가 빈번하다면 적합하지 않음

```js
/**
 * 
 * @param {arr} arr 값을 찾고 싶은 이미 정렬된 배열
 * @param {int} left 값을 찾으려는 범위의 가장 왼쪽 index
 * @param {int} right 값을 찾으려는 범위의 가장 오른쪽 index
 * @param {int} x 찾고싶은 값
 * returns x의 위치 혹은 위치 없음을 알려주는 값(-1)
 */

// 재귀 호출
const binarySearchRecursive = (arr, left, right, x) => {
  // 범위 내에 찾으려는 값이 없음
  if (left > right) return -1;
  const mid = Math.floor(left + right / 2);
  if (arr[mid] === x) return mid;
  if (x < arr[mid]) {
    binarySearch(arr, left, mid - 1, x);
  } else {
    binarySearch(arr, mid + 1, right, x);
  }
};
// 반복문
const binarySearch = (arr, x) => {
  const length = arr.length;
  let left = 0;
  let right = length - 1;
  while (left <= right) {
    const mid = Math.floor(left + right / 2);
    if (x === arr[mid]) return mid;
    if (x < arr[mid]) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }
  return -1;
};
```