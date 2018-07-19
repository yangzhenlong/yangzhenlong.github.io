## java8中Optional常用处理

```java
  @Data
  @AllArgsConstructor
  public static class User {
    Integer id;
    String name;
  }

  public static void main(String[] args) {
    User userNull = null;
    User userNotNull = new User(1, "abc");

    // ofNullable 基本操作
    User user1 = Optional.ofNullable(userNull).orElse(new User(0, "方式1：这是为null后新创建的user"));
    User user2 = Optional.ofNullable(userNull).orElseGet(() -> new User(0, "方式2：这是为null后新创建的user"));
    User user3 =
        Optional.ofNullable(userNull).orElseThrow(() -> new RuntimeException("为null则抛出异常"));

    // map 转换为其他类型
    Optional<User> optional = Optional.of(userNotNull);
    String findUserResult =
        optional
            .map(
                user -> {
                  if (user.getId() == 1) {
                    return "这是我需要找的人";
                  }
                  return "不是我要找的人";
                })
            .orElse("用户为空了");
  }
```