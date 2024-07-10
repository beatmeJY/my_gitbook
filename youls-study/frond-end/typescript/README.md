# TypeScript

## Type 사용법

```tsx
// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}

export type Address {
    city: string;
    detail: string;
    zipcode: number;
}

export type Menu {
    name: string;
    price: number;
    category: string;
}
```



## Interface 사용법

```tsx
// Store.tsx
import { Address, Restaurant } from './model/restaurant'

interface Ownprops {
    info:Restaurant,
    changeAddress(address:Address):void
}

// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}

export type Address {
    city: string;
    detail: string;
    zipcode: number;
}
```



## extends 사용법

### Interface에서 사용법

<pre class="language-tsx"><code class="lang-tsx"><strong>// BestMenu.tsx
</strong>import { Menu } from './model/restaurant'

interface Ownprops extends Menu {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Menu {
    name: string;
    price: number;
    category: string;
}
</code></pre>

### Type에서 사용법

```tsx
// BestMenu.tsx
import { Menu } from './model/restaurant'

type OwnProps = Menu & {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Menu {
    name: string;
    price: number;
    category: string;
}
```

### Omit 사용법 (특정 항목만 제거하고 싶을 때)

#### type

```tsx
// restaurant.ts
export type Address {
    city: string;
    detail: string;
    zipcode: number;
}

export type AddressWithoutZip = Omit<Address, 'zipCode'>
```

#### interface

```tsx
// BestMenu.tsx
import { Menu } from './model/restaurant'

interface OwnProps extends Omit<Menu, 'price'> {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Menu {
    name: string;
    price: number;
    category: string;
}
```

* 두 예제 모두 Omit으로 Menu에서 price를 제외한다. (예제에선 name, catefory만 사용 됨)

### Pick 사용법 (특정 항목만 사용하고 싶을 때)

#### type

```tsx
// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}

export type AddressWithoutZip = Pick<Restaurant, 'category'>
```

#### interface

```tsx
// BestMenu.tsx
import { Restaurant } from './model/restaurant'

interface OwnProps extends Pick<Restaurant, 'category'> {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}
```

* 두 예제 모두 Pick으로 Restaurant에서 category만 사용한다.
