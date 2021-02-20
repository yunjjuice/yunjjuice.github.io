---
title: "[JAVA] Comparable 및 Comparator를 사용하여 정렬하기"
date: 2021-02-21 02:10:37
categories : [JAVA]
tags: [JAVA]
excerpt: "Comparable과 Comparator 사용해보기"
toc: true
toc_sticky: true
---

Arrays나 Collections 기본적으로 정의된 정렬은 오름차순/사전순이다. <br>
```java
int[] arr = {5, 10, 1, -3, 6};
Arrays.sort(arr);
System.out.println(Arrays.toString(arr)); // [-3, 1, 5, 6, 10]

List<Integer> list = new ArrayList<>();
list.add(5);
list.add(10);
list.add(1);
list.add(-3);
list.add(6);
Collections.sort(list);
System.out.println(list.toString()); // [-3, 1, 5, 6, 10]
```

하지만 기존에 정의된 오름차순/사전순이 아닌 특정한 기준이 필요할 때가 있다. <br>
이 때 Comparable, Comparator 인터페이스를 사용해 기준을 바꿀 수 있다. <br>

# Comparable
`java.lang.Comparable`에 정의되어 있으며 `compareTo`메서드를 가진 인터페이스이다. <br>
java에서 제공되는 정렬이 가능한 클래스들은 모두 Comparable을 구현하고 있으며, 정렬 시에 이에 맞게 정렬된다. <br>
정렬이 필요한 클래스에 implements해준다. <br>
나의 경우엔 객체를 우선순위큐나 arraylist에 저장할 때 주로 사용했다. <br>
## compareTo
`int compareTo(T o)`
리턴값에 따라 결과가 바뀐다. <br>
- 음수 : 현재 객체 < 파라미터로 넘어온 객체
- 0 : 현재 객체 == 파라미터로 넘어온 객체
- 양수 : 현재 객체 > 파라미터로 넘어온 객체 <br>

음수 또는 0이면 객체의 자리가 그대로 유지되며, 양수인 경우에는 두 객체의 자리가 바뀐다.

```java
public static void main(String[] args) {
  List<Pos> list = new ArrayList<>();
  list.add(new Pos(1, 3));
  list.add(new Pos(5, 2));
  list.add(new Pos(1, 5));
  list.add(new Pos(3, 3));
  list.add(new Pos(4, 1));
  Collections.sort(list);
  System.out.println(list.toString());
  // [Pos [x=1, y=5], Pos [x=1, y=3], Pos [x=3, y=3], Pos [x=4, y=1], Pos [x=5, y=2]]
}

static class Pos implements Comparable<Pos> {
  int x, y;

  public Pos(int x, int y) {
    super();
    this.x = x;
    this.y = y;
  }

  @Override
  public String toString() {
    return "Pos [x=" + x + ", y=" + y + "]";
  }

  @Override
  public int compareTo(Pos o) {
    // x는 오름차순, x가 같다면 y는 내림차순으로 정렬
    if (this.x != o.x) return this.x - o.x;
    else return o.y - this.y;
  }
}
```

<br>

# Comparator
`java.util.Comparator`에 정의되었으며 compare 메서드를 가진 인터페이스이다.<br>
`Arrays.sort`나 `Collections.sort`에서 Comparator를 인자로 받는다. <br> 
Comparator를 상속받은 클래스를 만들어 사용할 수도 있지만, 나는 주로 익명클래스로 구현했다. <br>
## compare
`int compare(T o1, T o2)`
리턴값에 따라 결과가 바뀐다. <br>
- 음수 : 첫 번째 파라미터로 넘어온 객체 < 두 번째 파라미터로 넘어온 객체
- 0 : 첫 번째 파라미터로 넘어온 객체 == 두 번째 파라미터로 넘어온 객체
- 양수 : 첫 번째 파라미터로 넘어온 객체 > 두 번째 파라미터로 넘어온 객체 <br>

음수 또는 0이면 객체의 자리가 그대로 유지되며, 양수인 경우에는 두 객체의 자리가 바뀐다.

```java
int[][] arr = {%raw%}{{1, 3}, {5, 2}, {2, 5}, {3, 3}, {4, 1}}{%endraw%};

Arrays.sort(arr, new Comparator<int[]>() {
  @Override
  public int compare(int[] o1, int[] o2) {
    // 0번째 원소를 기준으로 내림차순 정렬
    return o2[0] - o1[0];
    }
});
		
for (int i = 0; i < arr.length; i++) {
  System.out.println(Arrays.toString(arr[i]));			
}
/*
[5, 2]
[4, 1]
[3, 3]
[2, 5]
[1, 3]
*/
```

<br>

# compareTo와 compare의 리턴값에 대하여
구현하는 방법은 다양하다. <br>
1. 위에 작성한 것처럼 계산값을 리턴하는 방법 <br> `return this.x - o.x`
2. if문을 사용하여 명확하게 표시하는 방법 <br> `if (this.x > o.x) return 1`
3. 래퍼클래스에 정의된 비교문을 사용하여 리턴하는 방법 <br> `return Integer.compare(o1[x], o2[x])`
<br> 등이 있다. <br>
<br>

리턴값이 따른 결과가 내림차순인지 오름차순인지 자주 헷갈린다. <br>
오름차순으로 하고 싶으면 `앞 - 뒤` <br>
내림차순으로 하고 싶으면 `뒤 - 앞` <br>
으로 기억하기로 하자.. <br>
`compareTo`는 인자가 한 개니까 `앞 == 현재 객체`로 생각하고 <br>
`compare`는 인자가 두개니까 `앞 == 첫번째 인자`로 생각하는 것이다. <br>
<br>

2번째 방법을 사용할 때도 마찬가지로 <br>
오름차순 : `if (this.x > o.x) return 1` <br>
내림차순 : `if (this.x <= o.x) return 1` <br>
이 될 것이다. <br>
양수가 리턴될 때 값이 바뀐다는 걸 기억하면 된다. <br>
<br>
