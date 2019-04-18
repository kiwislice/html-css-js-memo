---
title: 'Uinon-Find Algorithm'
date: 2019-04-18
weight: 1
tags: ["algorithm","union-find"]
---





### 用途

判斷2個目標是否在同一組、判斷2個目標是否相連



### 實現

- 用1維陣列表示N個目標

- `array[n]`表示第n個目標的父節點

- 每個群組可視為一棵樹，任意2點有相同根節點表示同組

- `array[i]==i`表示到達根節點

- 將2點設為同組只需

  ```java
  public void unite(int p, int q) {
      int i = root(p);
      int j = root(q);
      id[i] = j;
  }
  ```

  

### 瓶頸

- 樹太深時尋找根節點費時



### 優化

- 另外開個陣列紀錄群組大小，合併時將小樹放到大樹下，減少深度
- 在訪問根的過程中，將所有節點的父節點都順手改為父父節點，減少深度



### 參考資料

- <https://www.cs.princeton.edu/~rs/AlgsDS07/01UnionFind.pdf>

















