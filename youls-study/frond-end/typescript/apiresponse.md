---
description: api 호출 시 응답도 미리 정의하여 받을 수 있다.
---

# ApiResponse 만들기



<pre class="language-tsx"><code class="lang-tsx"><strong>// ApiResponse.ts
</strong><strong>export type ApiResponse&#x3C;T> = {
</strong><strong>    data:T[],
</strong><strong>    totalPage:number,
</strong><strong>    page:number
</strong><strong>}
</strong><strong>
</strong><strong>
</strong><strong>// restaurant.ts
</strong>export type Restaurant = {
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
</code></pre>
