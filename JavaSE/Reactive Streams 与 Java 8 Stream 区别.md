1.Project Reactor:

    Flux.fromIterable(Character.captainAmerica3())
            .filter(Character::isPlay)
            .map(Character::getName)
            .subscribe(System.out::println);

2.Java 8 Stream:

    Character.captainAmerica3().stream()
            .filter(Character::isPlay)
            .map(Character::getName)
            .forEach(System.out::println);

https://www.jianshu.com/p/a6a8de73f4bf
https://blog.csdn.net/mmlz00/article/details/86249161


parallel()