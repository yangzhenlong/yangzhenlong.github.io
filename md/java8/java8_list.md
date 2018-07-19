# list处理

## java8中list常用处理
```java
  @Data
  @AllArgsConstructor
  public static class User {
    Integer id;
    String name;
  }

  public static void main(String[] args) {

    List<User> list = Arrays.asList(new User(1, "a"), new User(1, "b"));
    // 遍历
    list.forEach(user -> System.out.println(user.getName()));
    // 根据id排序
    list.sort((u1, u2) -> u1.getId().compareTo(u2.getId()));
    // 从list中获取id的集合
    List<Integer> idList = list.stream().map(User::getId).collect(Collectors.toList());
    // list转map，以id为key
    Map<Integer, User> userMap = list.stream().collect(Collectors.toMap(User::getId, user -> user));
  }
```