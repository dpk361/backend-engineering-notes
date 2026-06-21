---
title: Team & Player Objects
parent: Stream API
nav_order: 1
---



```java

package common;

import java.util.Arrays;
import java.util.List;

public class Team {
    private int teamId;
    private String name;
    private String country;
    private int ranking;
    private List<Player> players;

    public Team(int teamId, String name, String country, int ranking, List<Player> players) {
        this.teamId = teamId;
        this.name = name;
        this.country = country;
        this.ranking = ranking;
        this.players = players;
    }

    @Override
    public String toString() {
        return "Team{" +
                "teamId=" + teamId +
                ", name='" + name + '\'' +
                ", country='" + country + '\'' +
                ", ranking=" + ranking +
                ", players=" + players +
                '}'+'\n';
    }

    public int getTeamId() {
        return teamId;
    }

    public String getName() {
        return name;
    }

    public String getCountry() {
        return country;
    }

    public int getRanking() {
        return ranking;
    }

    public List<Player> getPlayers() {
        return players;
    }


    public static List<Team> getTeams() {

            List<Player> indiaPlayers = Arrays.asList(
                    new Player(1, "Kohli", 35, "Batsman", 100, 5000, 10, 92.5),
                    new Player(2, "Rohit", 36, "Batsman", 90, 4500, 5, 88.0),
                    new Player(3, "Jadeja", 34, "AllRounder", 80, 3000, 150, 85.0)
            );

            List<Player> ausPlayers = Arrays.asList(
                    new Player(4, "Smith", 34, "Batsman", 85, 4800, 8, 87.5),
                    new Player(5, "Warner", 37, "Batsman", 95, 4700, 3, 90.0),
                    new Player(6, "Starc", 33, "Bowler", 70, 1200, 200, 75.0)
            );

            return Arrays.asList(
                    new Team(1, "India", "India", 1, indiaPlayers),
                    new Team(2, "Australia", "Australia", 2, ausPlayers)
            );
        }

}
```



```java
package common;

public class Player {
    private int playerId;
    private String name;
    private int age;
    private String role; // Batsman, Bowler, AllRounder
    private int totalInnings;
    private int totalScore;
    private int wickets;
    private double strikeRate;

    public Player(int playerId, String name, int age, String role,
                  int totalInnings, int totalScore, int wickets, double strikeRate) {
        this.playerId = playerId;
        this.name = name;
        this.age = age;
        this.role = role;
        this.totalInnings = totalInnings;
        this.totalScore = totalScore;
        this.wickets = wickets;
        this.strikeRate = strikeRate;
    }

    public double getAverageScore() {
        return totalInnings == 0 ? 0 : (double) totalScore / totalInnings;
    }

    @Override
    public String toString() {
        return "Player{" +
                "playerId=" + playerId +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", role='" + role + '\'' +
                ", totalInnings=" + totalInnings +
                ", totalScore=" + totalScore +
                ", wickets=" + wickets +
                ", strikeRate=" + strikeRate +
                '}'+'\n';
    }

    public int getPlayerId() {
        return playerId;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String getRole() {
        return role;
    }

    public int getTotalInnings() {
        return totalInnings;
    }

    public int getTotalScore() {
        return totalScore;
    }

    public int getWickets() {
        return wickets;
    }

    public double getStrikeRate() {
        return strikeRate;
    }
    
}

```