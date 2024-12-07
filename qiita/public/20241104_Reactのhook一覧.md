---
title: Reactのhook一覧
tags:
  - JavaScript
  - Hook
  - React
private: false
updated_at: '2024-11-04T21:51:27+09:00'
id: aa9a707a3c365e6f77fd
organization_url_name: null
slide: false
ignorePublish: false
---

# React の hook 一覧

- useState
- useReducer
- useContext
- useRef
- useImperativeHandle
- useEffect
- useLayoutEffec
- useInsertionEffectReact
- useMemo
- useCallback
- useTransition
- useDeferredValue
- useDebugValue
- useId
- useSyncExternalStore
- useActionState

# state に関する hook

state：component 内で管理する動的に変化するデータ。

### ■ useState

単純な状態管理のための hook。

```js
const [count, setCount] = useState(0);
```

### ■ useReducer

複雑な状態管理のための hook。

```js
function countReducer(state, action) {
  switch (action.type) {
    case "increment":
      return state + 1;
    default:
      return state;
  }
}

const [count, dispatch] = useReducer(countReducer, 0);
```

# context に関する hook

context：component ツリー全体でデータを共有するための仕組み。

### ■ useContext

component 間でデータを共有するための hook で、props を使わずに状態や値を親子間で受け渡すことが可能です。

```js
const UserContext = createContext();
const user = useContext(UserContext);
```

# ref に関する hook

ref：DOM 要素やクラスコンポーネントに直接アクセスするための手段。
(フォーカスやテキストの選択、アニメーションなど)

### ■ useRef

DOM 要素や任意の値の参照を保持し、再レンダリングされても値を保持し続ける hook。

```js
const inputRef = useRef(null);

return (
  <div>
    <input ref={inputRef} type="text" placeholder="Click the button to focus" />
    <button onClick={handleFocus}>Focus Input</button>
  </div>
);
```

### ■ useImperativeHandle

親コンポーネントが特定のメソッドやプロパティにアクセスできるように、カスタムの ref を定義する hook。

```js
// 親コンポーネント
function ParentComponent() {
  const customInputRef = useRef();

  const handleFocus = () => {
    customInputRef.current.focus();
  };

  return (
    <div>
      <CustomInput ref={customInputRef} />
      <button onClick={handleFocus}>Focus Custom Input</button>
    </div>
  );
}

// 子コンポーネント
const CustomInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  // 親コンポーネントからアクセス
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    },
  }));

  return (
    <input ref={inputRef} type="text" placeholder="Focus me from parent" />
  );
});
```

# effect に関する hook

effect: component の描画に影響しない外部処理や状態変更。

### ■ useEffect

レンダリング後に実行される副作用処理を定義する hook。

```js
// countが変わるたびに実行され、ドキュメントタイトルを更新
useEffect(() => {
  document.title = `Count: ${count}`;
  console.log(`ドキュメントタイトルを「Count: ${count}」に更新しました`);
}, [count]);
```

### ■ useLayoutEffect

DOM が変更される前に実行される処理を定義する hook。

```js
// textが変わるたびに実行され、textの内容に基づいて入力の幅を自動調整
useLayoutEffect(() => {
  if (inputRef.current) {
    inputRef.current.style.width = `${inputRef.current.scrollWidth}px`;
  }
}, [text]);
```

### ■ useInsertionEffect

CSS-in-JS のライブラリなどがスタイルを挿入するタイミングを制御するための hook。

```js
// componentマウント時、<style>タグを作成し、スタイルを挿入
useInsertionEffect(() => {
  const style = document.createElement("style");
  style.innerHTML = `
      .dynamic-style {
        color: purple;
        font-size: 24px;
        font-weight: bold;
      }
    `;
  document.head.appendChild(style);

  // クリーンアップで<style>タグを削除
  return () => {
    document.head.removeChild(style);
  };
}, []);
```

# performance に関する hook

キャッシュされた計算を再利用して、不要な作業をなくして performance を向上させる。

### ■ useMemo

計算コストの高い関数の結果をメモ化し、依存関係が変わらない限り再計算を避ける hook。

```js
const expensiveCalculation = useMemo(() => {
  return number * 1000;
}, [number]);
```

### ■ useMemo

関数をメモ化し、依存関係が変わらない限り新しい関数が生成されるのを防ぐ hook。
関数が子 component に渡される時などに役立ちます。

```js
const increment = useCallback(() => {
  setCount((prevCount) => prevCount + 1);
}, []);
```

### ■ useTransition

状態更新を低優先度に設定し、UI の応答性を保ちながら状態を遅延して更新するための hook。

```js
// isPending : 状態が低優先度で更新中であるかどうかを示すフラグ
// startTransition : この関数で囲んだ状態更新が低優先度として実行される
const [isPending, startTransition] = useTransition();

const handleChange = (e) => {
  setInput(e.target.value);

  // リストの更新を低優先度に設定
  startTransition(() => {
    const newList = Array.from(
      { length: 20000 },
      (_, index) => `${e.target.value} - Item ${index + 1}`
    );
    setList(newList);
  });
};
```

### ■ useDeferredValue

状態の値を遅延させ、UI の応答性を保ちながら状態更新のタイミングを調整する hook。

```js
function List({ input }) {
  // value : 遅延させたい元の値
  // deferredValue : 元の値を遅延させたもの
  const deferredValue = useDeferredValue(value);
  const items = Array.from({ length: 20000 }, (_, index) => (
    <p key={index}>
      {deferredInput} - Item {index + 1}
    </p>
  ));

  return <div>{items}</div>;
}
```

# その他の hook

こちらの hook は一般的には使用されない hook で特定のユースケースで使用されます。

### ■ useDebugValue

デバッグ用に使用する hook で、React DevTools で表示されるラベルを設定する。

```js
const [isOnline, setIsOnline] = useState(null);
// デバッグ時、isOnlineの状態が一目で分かるようにする
useDebugValue(isOnline ? "オンライン" : "オフライン");
```

### ■ useId

各コンポーネントに一意の ID を生成し、アクセシビリティ属性やフォーム要素のラベル付けなどに使用する。

```js
const id = useId();

return (
  <div>
    <label htmlFor={id}>Name:</label>
    <input id={id} type="text" />
  </div>
);
```

### ■ useSyncExternalStore

React コンポーネントが外部の状態管理ストア（Redux など）と同期できるようにするための hook。

```js
const store = {
  state: 0,
  listeners: new Set(),

  subscribe(listener) {
    this.listeners.add(listener);
    return () => this.listeners.delete(listener);
  },

  setState(newState) {
    this.state = newState;
    this.listeners.forEach((listener) => listener());
  },

  getSnapshot() {
    return this.state;
  },
};

const count = useSyncExternalStore(
  store.subscribe.bind(store), // storeの更新を監視
  store.getSnapshot.bind(store) // 現在のstoreの状態を取得
);
```

### ■ useActionState

form の action の状態を管理する hook。

```js
async function increment(previousState, formData) {
  return previousState + 1;
}

// state : 現在の値
// formAction : formのactionとして渡す関数(incrementを実行)
// 0が初期値
const [state, formAction] = useActionState(increment, 0);

return (
  <form>
    {state}
    <button formAction={formAction}>Increment</button>
  </form>
);
```
