## Map

### map 遍历有序吗

HashMap不是有序的，map 遍历和其存入的顺序不是对应的，一个存入的，并不是开始输出的；LinkedMap 是有序的；

```
Map <String,String> map = new HashMap<String,String>();
        map.put("熊大", "棕色");
        map.put("熊二", "黄色");
        map.put("熊三", "黑色");
        map.put("熊4", "4色");
        map.put("熊5", "5色");

        //方法一
        for(Map.Entry<String, String> entry : map.entrySet()){
            String mapKey = entry.getKey();
            String mapValue = entry.getValue();
            System.out.println(mapKey+":"+mapValue);
        }
        输出：
熊三:黑色
熊大:棕色
熊5:5色
熊4:4色
熊二:黄色
```

看出HashMap 不是有序的；

## ArrayList

### 扩容原理

arrayList 扩容原理，在add 方法 ensureCapacityInternal(size + 1) ; 把这个方法参数叫为最小数组长度。这个size 是之前的数组长度，这个方法先判断数组是不是空数组，若是第一次加入元素，会初始化为默认长度10；否则就取值size + 1；进入到ensureExplicitCapacity 方法，用这个最小长度和之前的数组初始化长度相比较，若大于则进行扩容，扩容方法为grow （minIncapacity）方法就用之前的长度进行位运算，右移一位，相当于1.5倍的方式；最后用native方法Array。copyOf 进行数组复制。同样的，如果是进行添加一个子容器，道理也是一样的。



总结：判断是不是空数组，是的话就初始化为10的长度，否则就拿这个数组长度和之前的数组比较，大于就扩容，方法是grow方法，然后进行1.5的容量，在调用native方法System.arrayCopy 复制数组。