写在前面

该文档主要记录java体系知识，从基本语法到高级特性，都在这里记录，形成一个后续可查阅到知识体系。

---

关键字

**return**

return 关键字用于方法中时，其作用是 **结束本方法的执行**。当方法有多个分支时，任何一个分支的return 都可以结束该方法执行。

>  当有方法嵌套时，也只能影响本方法，不能影响调用者。

代码验证：

```
private static void testtest(int a){
        if (a  > 0){
            System.out.println("a 大于 0");
            return;

        }else {
            System.out.println("a 小于 0");
        }
        returnM();
        System.out.println("returnM 方法之后打印");
}
private static void returnM(){
        System.out.println("M 方法之中");
        return;
    }
    
分别执行 
Test2.testtest(1);
Test2.testtest(-1);

打印：
a 大于 0
——————————————————————————————————————
a 小于 0
M 方法之中
returnM 方法之后打印
```

