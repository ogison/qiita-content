---
title: shadcn/ui Tableで上下にスクロールバーを実現する方法
tags:
  - table
  - React
  - shadcn
private: false
updated_at: '2025-01-18T11:46:58+09:00'
id: 83f6bd04c9c3c25000be
organization_url_name: null
slide: false
ignorePublish: false
---

# 1. はじめに

下記のような列が多い テーブル を扱う時に、`overflow` を用いて、左右に移動させるスクロールバーを表示させることがあると思います。
![scroll_1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/79edcd30-395c-da37-a118-eecd181320ef.png)

こちらは、`shadcn/ui` の `Table` を用いて テーブル を表示させており、default で`overflow-auto`がついているので、テーブル 下部にスクロールバーが表示されます。
![scroll_2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/a9627110-da9d-6ca5-0d3a-016304c2c448.png)

テーブル によっては、行が多すぎて、横にスクロールさせたいけど、テーブル の一番下まで行ってスクロールさせるのがめんどくさいというパターンが出てくると思います。
そこで、 `shadcn/ui` の `Table` を用いて、テーブル上下部に、左右にスクロールできるバーを表示させる方法をまとめました。

# 2. 前提

`shadcn/ui` の `Table` を Manual でインストールして使用します。
https://ui.shadcn.com/docs/components/table

# 3. テーブル上下のスクロールバー実装例

Manual で貼り付けたコードの `Table` を下記のように修正します。

```table.tsx
const Table = React.forwardRef<
  HTMLTableElement,
  React.HTMLAttributes<HTMLTableElement>
>(({ className, ...props }, ref) => {
  // テーブル下部のスクロールバー
  const scrollContainerRef = React.useRef<HTMLDivElement>(null);

  // テーブル上部のスクロールバーが表示されるかどうか
  const [isOverflowing, setIsOverflowing] = React.useState<boolean>(false);

  // テーブル上部のスクロールバー
  const topScrollRef = React.useRef<HTMLDivElement>(null);
  const [tableScrollWidth, setTableScrollWidth] = React.useState<number>(0);
  const [topScrollbarWidth, setTopScrollbarWidth] = React.useState<number>(0);

  // テーブル上部のスクロールバーを更新
  const updateOverflowStatus = React.useCallback(() => {
    const tableElement = (
      ref as React.MutableRefObject<HTMLTableElement | null>
    ).current;
    if (scrollContainerRef.current && tableElement) {
      const container = scrollContainerRef.current;
      const table = tableElement;

      const containerWidth = Math.ceil(container.clientWidth);
      const contentScrollWidth = table.scrollWidth;
      const contentWidth = Math.ceil(table.getBoundingClientRect().width);

      setTableScrollWidth(contentWidth);
      setTopScrollbarWidth(containerWidth);
      setIsOverflowing(contentScrollWidth > containerWidth);
    }
  }, [ref]);

  React.useEffect(() => {
    updateOverflowStatus();

    const resizeObserver = new ResizeObserver(updateOverflowStatus);
    const container = scrollContainerRef.current;
    const tableElement = (
      ref as React.MutableRefObject<HTMLTableElement | null>
    ).current;

    if (container) resizeObserver.observe(container);
    if (tableElement) resizeObserver.observe(tableElement);

    return () => resizeObserver.disconnect();
  }, [updateOverflowStatus]);

  // テーブル下部のスクロールバーとテーブル上部のスクロールバー
  const syncBottomScroll = React.useCallback(() => {
    if (scrollContainerRef.current && topScrollRef.current) {
      topScrollRef.current.scrollLeft = scrollContainerRef.current.scrollLeft;
    }
  }, []);
  const syncTopScroll = React.useCallback(() => {
    if (scrollContainerRef.current && topScrollRef.current) {
      scrollContainerRef.current.scrollLeft = topScrollRef.current.scrollLeft;
    }
  }, []);

  return (
    <div>
      {isOverflowing && (
        <div
          ref={topScrollRef}
          onScroll={syncTopScroll}
          className={`overflow-x-auto`}
          style={{ width: `${topScrollbarWidth}px` }}
        >
          <div
            className="h-4 bg-transparent"
            style={{ width: `${tableScrollWidth}px` }}
          />
        </div>
      )}
      <div
        className="relative w-full overflow-auto"
        onScroll={syncBottomScroll}
        ref={scrollContainerRef}
      >
        <table
          ref={ref}
          className={cn("w-full caption-bottom text-sm", className)}
          {...props}
        />
      </div>
    </div>
  );
});
```

呼び出し先

```tsx
export function TableDemo() {
  // refを使用して、Tableを呼び出す
  const tableRef = useRef<HTMLTableElement>(null);

  return (
    <div style={{ padding: "20px" }}>
      <Table className="w-4/5" ref={tableRef}>
        <TableHeader>
          <TableRow>
          .
          .

```

# 4. テーブル上下のスクロールバー実装方法

## ① テーブル上下部のスクロールバー用の状態を定義

テーブル上下部のスクロールバーを制御するために、`ref` と状態変数を用意します。

```tsx
// テーブル下部のスクロールバー
const scrollContainerRef = React.useRef<HTMLDivElement>(null);

// テーブル上部のスクロールバーが表示されるかどうか
const [isOverflowing, setIsOverflowing] = React.useState<boolean>(false);

// テーブル上部のスクロールバー
const topScrollRef = React.useRef<HTMLDivElement>(null);
const [tableScrollWidth, setTableScrollWidth] = React.useState<number>(0);
const [topScrollbarWidth, setTopScrollbarWidth] = React.useState<number>(0);
```

## ② テーブル上部のスクロールバーを更新

テーブルのサイズが変更されたときに、スクロールバーの表示状態を更新します。テーブル下部のスクロールバーが表示されるときのみ、テーブル上部のスクロールバーを表示させるようにして、テーブル下部の長さと同じ長さのスクロールバーを上部に表示させます。

```tsx
// テーブル上部のスクロールバーを更新
const updateOverflowStatus = React.useCallback(() => {
  const tableElement = (ref as React.MutableRefObject<HTMLTableElement | null>)
    .current;
  if (scrollContainerRef.current && tableElement) {
    const container = scrollContainerRef.current;
    const table = tableElement;

    const containerWidth = Math.ceil(container.clientWidth);
    const contentScrollWidth = table.scrollWidth;
    const contentWidth = Math.ceil(table.getBoundingClientRect().width);

    setTableScrollWidth(contentWidth);
    setTopScrollbarWidth(containerWidth);
    setIsOverflowing(contentScrollWidth > containerWidth);
  }
}, [ref]);

React.useEffect(() => {
  updateOverflowStatus();

  const resizeObserver = new ResizeObserver(updateOverflowStatus);
  const container = scrollContainerRef.current;
  const tableElement = (ref as React.MutableRefObject<HTMLTableElement | null>)
    .current;

  if (container) resizeObserver.observe(container);
  if (tableElement) resizeObserver.observe(tableElement);

  return () => resizeObserver.disconnect();
}, [updateOverflowStatus]);
```

## ③ テーブル上下部のスクロールバーを同期

テーブル上下部のスクロールバーの長さを同期させる関数を用意します。

```tsx
// テーブル下部のスクロールバーとテーブル上部のスクロールバー
const syncBottomScroll = React.useCallback(() => {
  if (scrollContainerRef.current && topScrollRef.current) {
    topScrollRef.current.scrollLeft = scrollContainerRef.current.scrollLeft;
  }
}, []);
const syncTopScroll = React.useCallback(() => {
  if (scrollContainerRef.current && topScrollRef.current) {
    scrollContainerRef.current.scrollLeft = topScrollRef.current.scrollLeft;
  }
}, []);
```

## ④ テーブル表示

定義した `ref` と状態を使用してテーブルを表示させます。

```tsx
return (
  <div>
    {isOverflowing && (
      <div
        ref={topScrollRef}
        onScroll={syncTopScroll}
        className={`overflow-x-auto`}
        style={{ width: `${topScrollbarWidth}px` }}
      >
        <div
          className="h-4 bg-transparent"
          style={{ width: `${tableScrollWidth}px` }}
        />
      </div>
    )}
    <div
      className="relative w-full overflow-auto"
      onScroll={syncBottomScroll}
      ref={scrollContainerRef}
    >
      <table
        ref={ref}
        className={cn("w-full caption-bottom text-sm", className)}
        {...props}
      />
    </div>
  </div>
);
```

## ⑤ ref を用いて、Table を呼び出す

`ref` を使用して、`Table` を呼び出します。

```tsx
export function TableDemo() {
  // refを使用して、Tableを呼び出す
  const tableRef = useRef<HTMLTableElement>(null);

  return (
    <div style={{ padding: "20px" }}>
      <Table className="w-4/5" ref={tableRef}>
        <TableHeader>
          <TableRow>
          .
          .

```

下記のようにテーブル上下部にスクロールバーが表示されます。
![scroll_3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/bf458596-0a38-7916-55b1-0b172f15c192.png)

# 5.　さいごに

`shadcn/ui` を使用したテーブル上下部にスクロールバーを表示させる方法をまとめました。もしかしたら、`shadcn/ui` の`Scroll-area`を用いた方法がコンパクトにまとめれたのかもしれません。一旦は、`shadcn/ui` の`Table`のみを用いた方法でまとめました。もっとコンパクトにまとめれる方法がありましたらご指摘していただけると幸いです。
