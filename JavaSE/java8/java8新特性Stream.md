### java8新特性Stream

```java
package com.example.stream;

import lombok.Data;
import lombok.NoArgsConstructor;
import org.junit.jupiter.api.Test;

import java.util.*;
import java.util.stream.Collectors;

/**

* java8 新特性操作Stream处理
  *

* @author gulin

* @date 2021/10/21 14:20
  */
  public class ApiStreamTest {

  @Data
  @NoArgsConstructor
  class Student {
  private int id;
  private String name;
  private int age;
  private int grade;
  private String major;
  private String school;

       public Student(int id, String name, int age, int grade, String major, String school) {
           this.id = id;
           this.name = name;
           this.age = age;
           this.grade = grade;
           this.major = major;
           this.school = school;
       }

  }

  /**

    * 初始化
      */
      List<Student> students = new ArrayList<Student>() {
      {
      add(new Student(20160001, "孔明", 20, 1, "土木工程", "武汉大学"));
      //add(new Student(20160001, "孔明", 20, 1, "土木工程", "武汉大学"));
      add(new Student(20160002, "伯约", 21, 2, "信息安全", "武汉大学"));
      add(new Student(20160003, "玄德", 22, 3, "经济管理", "武汉大学"));
      add(new Student(20160004, "云长", 21, 2, "信息安全", "武汉大学"));
      add(new Student(20161001, "翼德", 21, 2, "机械与自动化", "华中科技大学"));
      add(new Student(20161002, "元直", 23, 4, "土木工程", "华中科技大学"));
      add(new Student(20161003, "奉孝", 23, 4, "计算机科学", "华中科技大学"));
      add(new Student(20162001, "仲谋", 22, 3, "土木工程", "浙江大学"));
      add(new Student(20162002, "鲁肃", 23, 4, "计算机科学", "浙江大学"));
      add(new Student(20163001, "丁奉", 24, 5, "土木工程", "南京大学"));
      }
      };

  @Test
  public void filter() {
  //过滤
  List<Student> collect = students.stream().filter(student -> "武汉大学".equals(student.getSchool()))
  .collect(Collectors.toList());
  outPrintln(Collections.singletonList(collect));
  }

  @Test
  public void distinct() {
  //过滤 + 去重
  List<Student> collect = students.stream().filter(student -> "武汉大学".equals(student.getSchool()))
  .distinct()
  .collect(Collectors.toList());
  outPrintln(Collections.singletonList(collect));
  }

  @Test
  public void limit() {
  //分页+排序
  List<Student> collect = students.stream().filter(student -> "土木工程".equals(student.getMajor()))
  .sorted((s1, s2) -> s1.getAge() - s2.getAge())
  .limit(2)
  .collect(Collectors.toList());
  outPrintln(Collections.singletonList(collect));
  }

  @Test
  public void skip() {
  //从第几个开始查找
  List<Student> collect = students.stream().filter(student -> "土木工程".equals(student.getMajor()))
  .skip(2)
  .collect(Collectors.toList());
  outPrintln(Collections.singletonList(collect));
  }

  @Test
  public void map() {
  //获取指定信息
  List<String> collect = students.stream().filter(student -> "计算机科学".equals(student.getMajor())).map(Student::getName).collect(Collectors.toList());
  outPrintln(Collections.singletonList(collect));
  //求和
  int sum = students.stream().filter(student -> "计算机科学".equals(student.getMajor()))
  .mapToInt(Student::getAge).sum();
  System.out.println(sum);
  }

  @Test
  public void flatMap() {
  // flatMap是将一个流中的每个值都转成一个个流，然后再将这些流扁平化成为一个流 。
  String[] strs = {"java8", "is", "easy", "to", "use"};
  List<String[]> distinct = Arrays.stream(strs)
  .map(str -> str.split(""))  // 映射成为Stream<String[]>
  .distinct()
  .collect(Collectors.toList());

       //解析流的流 数据信息
       distinct.forEach(s -> {
           for (String s1 : s) {
               System.out.print(s1);
           }
           System.out.println();
       });
      
       List<String> distinctStrs = Arrays.stream(strs)
               .map(str -> str.split(""))  // 映射成为Stream<String[]>
               .flatMap(Arrays::stream)  // 扁平化为Stream<String>
               .distinct()
               .collect(Collectors.toList());
       outPrintln(Collections.singletonList(distinctStrs));

  }

  @Test
  public void allMatch() {
  //检查是否都满足
  boolean flag = students.stream().allMatch(student -> student.getAge() >= 21);
  outField(flag);
  }

  @Test
  public void anyMatch() {
  //检测是否有一个 或者 多个满足
  boolean flag = students.stream().anyMatch(student -> "武汉大学".equals(student.getSchool()));
  outField(flag);
  }

  @Test
  public void noneMathch() {
  //用户检测不存在的信息  如果不存在返回true 存在返回 false
  boolean flag = students.stream().noneMatch(student -> "计算机科学".equals(student.getMajor()));
  outField(flag);
  }

  @Test
  public void findFirst() {
  //获取满足条件的第一个
  Optional<Student> first = students.stream().filter(student -> "土木工程".equals(student.getMajor())).findFirst();
  outPrintln(Collections.singletonList(first));
  }

  @Test
  public void findAny() {
  //返回任意一个满足条件的信息
  Optional<Student> any = students.stream().filter(student -> "土木工程".equals(student.getMajor())).findAny();
  outPrintln(Collections.singletonList(any));
  }

  @Test
  public void reduce() {
  //使用 reduce 归约 汇总
  int totalAge1 = students.stream().filter(student -> "计算机科学".equals(student.getMajor()))
  .mapToInt(Student::getAge).sum();
  outField("totalAge1==>" + totalAge1);

       // 归约操作int totalAge2 = students.stream()
       int totalAge2 = students.stream().filter(student -> "计算机科学".equals(student.getMajor()))
               .map(Student::getAge)
               .reduce(0, (a, b) -> a + b);
       outField("totalAge2==>" + totalAge2);
      
       // 进一步简化int totalAge3 = students.stream()
       int totalAge3 = students.stream().filter(student -> "计算机科学".equals(student.getMajor()))
               .map(Student::getAge)
               .reduce(0, Integer::sum);
       outField("totalAge3==>" + totalAge3);
      
       // 采用无初始值的重载版本，需要注意返回OptionalOptional<Integer> totalAge = students.stream()
       Optional<Integer> totalAge4 = students.stream().filter(student -> "计算机科学".equals(student.getMajor()))
               .map(Student::getAge)
               .reduce(Integer::sum);  // 去掉初始值
       outPrintln(Collections.singletonList(totalAge4));
      
       // 进一步简化long count = students.stream().count(); 求学生总人数  counting
       long count = students.stream().collect(Collectors.counting());
       outField(count);
      
       //求年龄最大值和最小值 maxBy
       Optional<Student> collect = students.stream().collect(Collectors.maxBy((s1, s2) -> s1.getAge() - s2.getAge()));
       outPrintln(Collections.singletonList(collect));
      
       //求年龄总和 summingInt
       Integer collect1 = students.stream().collect(Collectors.summingInt(Student::getAge));
       outField(collect1);
      
       //求年龄平均值  averagingInt
       Double collect2 = students.stream().collect(Collectors.averagingInt(Student::getAge));
       outField(collect2);
      
       //一次性得到元素个数、总和、均值、最大值、最小值 summarizingInt
       IntSummaryStatistics collect3 = students.stream().collect(Collectors.summarizingInt(Student::getAge));
       outPrintln(Collections.singletonList(collect3));
      
       //字符串拼接 joining
       String collect4 = students.stream().map(Student::getName).collect(Collectors.joining(","));
       outField(collect4);

  }

  @Test
  public void group() {

       //按学校对学生进行分组 groupingBy
       Map<String, List<Student>> collect = students.stream().collect(Collectors.groupingBy(Student::getSchool));
       outPrintln(Collections.singletonList(collect));
      
       // 多级分组
       Map<String, Map<String, List<Student>>> collect1 = students.stream().collect(
               Collectors.groupingBy(Student::getSchool,  // 一级分组，按学校
                       Collectors.groupingBy(Student::getMajor)));  // 二级分组，按专业
       outPrintln(Collections.singletonList(collect1));
      
       //获取分组 - 总数信息
       Map<String, Long> collect2 = students.stream().collect(Collectors.groupingBy(Student::getSchool, Collectors.counting()));
       outPrintln(Collections.singletonList(collect2));

  }

  @Test
  public void partition() {
  //方法实现分区，该方法接收一个谓词，例如我们希望将学生分为武大学生和非武大学生， partitioningBy 返回结果 key 为 true 和 false
  Map<Boolean, List<Student>> collect = students.stream().collect(Collectors.partitioningBy(student -> "武汉大学".equals(student.getSchool())));
  outPrintln(Collections.singletonList(collect));
  }


    private void outPrintln(List<Object> collect) {
        System.out.println("处理获取个数:" + collect.size());
        collect.forEach(System.out::println);
    }
    
    private void outField(Object flag) {
        System.out.println(flag);
    }

}
```

[<center>返回</center>](../javase_menu.md)