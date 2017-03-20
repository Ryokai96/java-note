# Java 算法笔记
## 递归算法
- 阶乘递归算法
```java
public class TestRecursion {
    //计算阶乘
    public static long factorail(int n) {
        if(n == 1) {
            return 1;
        } else {
            return n * factorail(n-1);
        }
    }

    public static void main(String[] args) {
        System.out.println(factorail(10));
    }
}
```