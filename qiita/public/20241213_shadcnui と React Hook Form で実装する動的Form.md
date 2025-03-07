---
title: shadcn/ui と React Hook Form で実装する動的Form
tags:
  - React
  - react-hook-form
  - shadcn
private: false
updated_at: '2024-12-13T09:03:00+09:00'
id: 16cab00232c19c504073
organization_url_name: null
slide: false
ignorePublish: false
---

# 目次

- [目次](#目次)
- [1. はじめに](#1-はじめに)
- [2. 使用環境](#2-使用環境)
- [3. 動的 Form の実装例](#3-動的-form-の実装例)
- [4. 動的 Form の実装方法](#4-動的-form-の実装方法)
  - [① Form の定義](#-form-の定義)
  - [② 動的 Form の操作を定義](#-動的-form-の操作を定義)
  - [③ Form の実装](#-form-の実装)
  - [④ Form 項目 の追加、削除機能を実装](#-form-項目-の追加削除機能を実装)

# 1. はじめに

`shadcn/ui` と `React Hook Form` を用いた動的な値を持つ Form の実装方法についてまとめました。下記の図のような、追加したり、削除したりできる項目を `useForm` で管理する方法を説明します。

![usefield_form.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/35464845-f47b-f653-6546-eb486bdfbaa6.png)

# 2. 使用環境

Windows 11
Next.js : ^15.0.3
React : 18.2.0
React Hook Form : ^7.54.0
Tailwind CSS : ^3.4.1

# 3. 動的 Form の実装例

```page.tsx
'use client';
import { PlusCircle, Trash2 } from 'lucide-react';

import { useFieldArray, useForm, useWatch } from 'react-hook-form';
import { z } from 'zod';

import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from '@/components/ui/form';

export const itemSchema = z.object({
  id: z.number().int().positive(),
  value: z.string().nullable().optional(),
});

export const formSchema = z.object({
  items: z.array(itemSchema),
});

export type FormSchemaType = z.infer<typeof formSchema>;

const page = () => {
  const form = useForm<FormSchemaType>({
    defaultValues: {
      items: [{ id: 1, value: '' }],
    },
  });

  const { fields, append, remove } = useFieldArray({
    name: 'items',
    control: form.control,
  });

  const items = useWatch({
    name: 'items',
    control: form.control,
  });

  const addItem = () => {
    const newId = items.length > 0 ? Math.max(...items.map((item) => item.id)) + 1 : 1;
    append({ id: newId, value: '' });
  };

  const onSubmit = () => console.log('送信しました');

  return (
    <div className="App container my-8">
      <Form {...form}>
        <form className="space-y-4" onSubmit={form.handleSubmit(onSubmit)}>
          <div className="space-y-2">
            <FormLabel>選択肢：</FormLabel>
            {fields.map((item, index) => (
              <div key={item.id}>
                <FormField
                  name={`items.${index}.value`}
                  render={({ field }) => (
                    <FormItem>
                      <FormControl>
                        <div className="flex items-center space-x-2" key={item.id}>
                          <Input
                            aria-label={`選択肢 ${index + 1}`}
                            className="grow"
                            onChange={(e) => field.onChange(e.target.value)}
                            placeholder={`選択肢 ${index + 1}`}
                            type="text"
                            value={field.value ?? ''}
                          />
                          <Button
                            aria-label={`選択肢 ${index + 1} を削除`}
                            className="shrink-0"
                            onClick={() => remove(index)}
                            size="icon"
                            type="button"
                            variant="destructive"
                          >
                            <Trash2 className="size-4" />
                          </Button>
                        </div>
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
              </div>
            ))}
          </div>
          <div className="flex items-center justify-between pt-4">
            <Button className="mr-2 w-full" onClick={addItem} type="button" variant="outline">
              <PlusCircle className="mr-2 size-4" />
              選択肢を追加
            </Button>
            <Button
              className="ml-2 w-full"
              disabled={items.filter((item) => item.value?.trim() !== '').length < 2}
              type="submit"
            >
              '選択する'
            </Button>
          </div>
        </form>
      </Form>
    </div>
  );
};

export default page;

```

# 4. 動的 Form の実装方法

## ① Form の定義

今回は、id と value を持つ配列を値に持つ Form を例にして解説します。
最初に、formSchema を zod を使用して作成します。その後、`useForm` を使用して、`react-hook-form` を作成します。

```tsx
export const itemSchema = z.object({
  id: z.number().int().positive(),
  value: z.string().nullable().optional(),
});

export const formSchema = z.object({
  items: z.array(itemSchema),
});

const form = useForm<FormSchemaType>({
  defaultValues: {
    items: [{ id: 1, value: "" }],
  },
});
```

## ② 動的 Form の操作を定義

`useFieldArray`を使用して、動的 Form を操作できるようにします。① で定義した form の型とコンポーネントを登録する`control`を指定して作成します。
以下の値を返します。

- fields : items の値
- append : items に値を追加する function
- remove : items から値を削除する function

```tsx
const { fields, append, remove } = useFieldArray({
  name: "items",
  control: form.control,
});
```

function に関しては、他にもいろいろあるので、以下を参照してください。

https://react-hook-form.com/docs/usefieldarray

## ③ Form の実装

fields を map 関数を使って繰り返し、`shadcn/ui`の Form を作成します。
その際、`FormField`の render には、動的に値が変化する（ユーザの入力によって値が変わる）`items.${index}.value`を指定します。

```tsx
{
  fields.map((item, index) => (
    <div key={item.id}>
      <FormField
        name={`items.${index}.value`}
        render={({ field }) => (
          <FormItem>
            <FormControl>
              <div className="flex items-center space-x-2" key={item.id}>
                <Input
                  aria-label={`選択肢 ${index + 1}`}
                  className="grow"
                  onChange={(e) => field.onChange(e.target.value)}
                  placeholder={`選択肢 ${index + 1}`}
                  type="text"
                  value={field.value ?? ""}
                />
                <Button
                  aria-label={`選択肢 ${index + 1} を削除`}
                  className="shrink-0"
                  onClick={() => remove(index)}
                  size="icon"
                  type="button"
                  variant="destructive"
                >
                  <Trash2 className="size-4" />
                </Button>
              </div>
            </FormControl>
            <FormMessage />
          </FormItem>
        )}
      />
    </div>
  ));
}
```

## ④ Form 項目 の追加、削除機能を実装

項目の追加、削除機能は、② で定義した、append、remove を使用して実装します。
remove は index（削除したい値の箇所）、append は追加したい値を指定するだけで使用できるので簡単に扱えます。

```tsx
<Button
aria-label={`選択肢 ${index + 1} を削除`}
className="shrink-0"
onClick={() => remove(index)}
size="icon"
type="button"
variant="destructive"
>
```

```tsx
const addItem = () => {
  const newId =
    items.length > 0 ? Math.max(...items.map((item) => item.id)) + 1 : 1;
  append({ id: newId, value: "" });
};

<Button
  className="mr-2 w-full"
  onClick={addItem}
  type="button"
  variant="outline"
>
  <PlusCircle className="mr-2 size-4" />
  選択肢を追加
</Button>;
```
