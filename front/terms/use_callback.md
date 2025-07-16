아래는 **React의 `useCallback` 훅**에 대한 설명과 예제, 주의사항 등을 포함한 **Markdown 문서**입니다. 바로 복사해서 사용하실 수 있도록 구성했습니다.

````markdown
# 🔁 React `useCallback` 활용 가이드

## ✅ 개념

`useCallback`은 **함수 자체를 메모이제이션**하여 **불필요한 함수 재생성을 방지**하는 훅입니다.

```tsx
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
````

* `a`, `b`가 변경되지 않는 한, 같은 함수 객체를 반환합니다.
* 컴포넌트가 다시 렌더링되더라도 **같은 함수 참조값**을 유지합니다.

---

## 🔧 주요 활용 용도

| 활용 목적                          | 설명                                                  |
|--------------------------------|-----------------------------------------------------|
| **불필요한 자식 컴포넌트 리렌더링 방지**       | props로 전![img.png](img.png)달`useMemo` 내 의존성으로 함수를 넣을 때 안전하게 메모이제이션 |
| **성능 최적화**                     | 렌더링 성능이 중요한 경우 함수 재생성 최소화                           |

---

## 📌 사용 예제

### 1. 자식 컴포넌트에 콜백 전달

```tsx
const Parent = () => {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("Clicked!");
  }, []); // 의존성 없음

  return <Child onClick={handleClick} />;
};

const Child = React.memo(({ onClick }) => {
  console.log("Child render");
  return <button onClick={onClick}>Click me</button>;
});
```

### 2. useEffect 내부에서 함수 의존성으로 사용

```tsx
const fetchData = useCallback(async () => {
  const result = await fetch('/api/data');
  const json = await result.json();
  setData(json);
}, [setData]);

useEffect(() => {
  fetchData();
}, [fetchData]);
```

---

## ⚠️ 주의사항

* **남용 금지**: 함수가 재사용되지 않거나 성능에 영향이 없다면 굳이 사용할 필요는 없습니다.
* **의존성 배열 누락 주의**: `useCallback` 내부에서 사용하는 모든 값은 의존성 배열에 포함해야 합니다.

---

## ✅ 사용 권장 조건

| 조건                  | `useCallback` 사용 여부 |
|---------------------|---------------------|
| props로 콜백을 자식에게 전달  | ✅ 권장                |
| 렌더링 최적화 필요          | ✅ 권장                |
| 콜백 내부에서 상태나 외부 값 참조 | ✅ 의존성 배열 신경 써야 함    |
| 단순한 함수, 한 번만 쓰는 함수  | ❌ 불필요               |

---

## 🔄 관련 React 기능

| 기능           | 설명                   |
|--------------|----------------------|
| `useMemo `   |  계산 결과를 메모이제이션할 때 사용 |
| `React.memo` | 컴포넌트를 메모이제이션할 때 사용   |
| `useRef`     | 불변 값을 참조하거나 저장할 때 사용 |

---

## 📎 참고 문서

* [React 공식 문서 - useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)
* [React 공식 문서 - React.memo](https://reactjs.org/docs/react-api.html#reactmemo)

```

필요하시면 `useMemo`와 `useCallback`의 차이점도 비교 표로 정리해 드릴 수 있습니다.
```
