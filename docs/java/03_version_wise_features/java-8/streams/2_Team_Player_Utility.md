---
title: Team Utility
parent: Stream API
nav_order: 2
---

```java
List<Team> teams = Team.getTeams();

List<Player> players = teams.stream()
        .flatMap(team -> team.getPlayers().stream())
        .toList();
```

## 1. Get all unique roles

```java
List<String> distinctRoles = players.stream()
        .map(Player::getRole)
        .distinct()
        .toList();

// Alternative (better for uniqueness)
Set<String> roles = players.stream()
        .map(Player::getRole)
        .collect(Collectors.toSet());
```

## 2. Top 2 players by strike rate

```java
List<Player> top2Players = players.stream()
        .sorted(Comparator.comparingDouble(Player::getStrikeRate).reversed())
        .limit(2)
        .toList();
```

## 3. Partition players age > 35

```java
Map<Boolean, List<Player>> partition = players.stream()
        .collect(Collectors.partitioningBy(p -> p.getAge() > 35));
```

## 4. Total score of all players

```java
int totalScore = players.stream()
        .mapToInt(Player::getTotalScore)
        .sum();
```

## 5. Join all player names

```java
String names = players.stream()
        .map(Player::getName)
        .collect(Collectors.joining(", "));
```


## 6. Average score per role

```java
Map<String, Double> avgScoreByRole = players.stream()
        .collect(Collectors.groupingBy(
                Player::getRole,
                Collectors.averagingDouble(Player::getTotalScore)
        ));
```

## 7. Highest strike rate player per role

```java
Map<String, Optional<Player>> highestStrikeByRole = players.stream()
        .collect(Collectors.groupingBy(
                Player::getRole,
                Collectors.maxBy(Comparator.comparingDouble(Player::getStrikeRate))
        ));
```

## 8. Team with highest total score

```java
Optional<Map.Entry<String, Integer>> topTeam = teams.stream()
        .collect(Collectors.toMap(
                Team::getName,
                t -> t.getPlayers().stream()
                        .mapToInt(Player::getTotalScore)
                        .sum()
        ))
        .entrySet()
        .stream()
        .max(Map.Entry.comparingByValue());
```

```java
Optional<Team> topTeam = teams.stream()
        .max(Comparator.comparingInt(
                t -> t.getPlayers().stream()
                        .mapToInt(Player::getTotalScore)
                        .sum()
        ));
```

## 9. Sort players by team ranking, then strike rate

```java
List<Player> sortedPlayers = teams.stream()
        .flatMap(team -> team.getPlayers().stream()
                .map(player -> Map.entry(team.getRanking(), player)))
        .sorted(Comparator
                .comparingInt((Map.Entry<Integer, Player> e) -> e.getKey())
                .thenComparing(e -> e.getValue().getStrikeRate(), Comparator.reverseOrder()))
        .map(Map.Entry::getValue)
        .toList();
```

```java
List<Player> sortedPlayers = teams.stream().flatMap(xt->xt.getPlayers().stream().map(player-> List.of(player,xt.getRanking())))

                .sorted((x,y)->{
                    double pr = ((Player)y.get(0)).getStrikeRate() - ((Player)x.get(0)).getStrikeRate();
                    int tr = (int)x.get(1) - (int)y.get(1);
                    if(tr!=0) return tr;
                    else return (int) pr;
                }).map(x->(Player)x.get(0)).toList();

```


## 10. Top scorer per team AND role

```java
Map<String, Map<String, Player>> result = teams.stream()
        .collect(Collectors.toMap(
                Team::getName,
                team -> team.getPlayers().stream()
                        .collect(Collectors.groupingBy(
                                Player::getRole,
                                Collectors.collectingAndThen(
                                        Collectors.maxBy(Comparator.comparingInt(Player::getTotalScore)),
                                        Optional::get
                                )
                        ))
        ));
```


## 11. For each team → total score + average strike rate

```java

record SomeCustomObject(double strikeRate, int totalScore) {}

Map<String, SomeCustomObject> teamStats = teams.stream()
        .collect(Collectors.toMap(
                Team::getName,
                team -> {
                    int totalScore = team.getPlayers().stream()
                            .mapToInt(Player::getTotalScore)
                            .sum();

                    double avgStrikeRate = team.getPlayers().stream()
                            .mapToDouble(Player::getStrikeRate)
                            .average()
                            .orElse(0.0);

                    return new SomeCustomObject(avgStrikeRate, totalScore);
                }
        ));
```

