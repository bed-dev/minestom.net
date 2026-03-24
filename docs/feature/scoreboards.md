# Scoreboards and Teams

Minestom provides a comprehensive API for handling Minecraft's scoreboard features, including the Sidebar, Tab List sorting/display, Below Name tags, and Teams.

## Sidebar

The Sidebar (or "Scoreboard") is the display on the right side of the screen. In Minestom, this is represented by the `Sidebar` class.

### Creating a Sidebar

To create a sidebar, instantiate the `Sidebar` class with a title.

```java
import net.kyori.adventure.text.Component;
import net.minestom.server.scoreboard.Sidebar;

Sidebar sidebar = new Sidebar(Component.text("My Server"));
```

### Adding Viewers

To show the sidebar to a player, add them as a viewer.

```java
sidebar.addViewer(player);
```

### Creating Lines

Lines in the sidebar are also `Sidebar.ScoreboardLine` objects. You create them using `sidebar.createLine`. Each line must have a unique identifier.

```java
import net.minestom.server.scoreboard.Sidebar;

// Create a line with unique ID "score" and value 10
Sidebar.ScoreboardLine line1 = new Sidebar.ScoreboardLine("score", Component.text("Score: 10"), 10);
sidebar.createLine(line1);

// Create another line with a lower score (will appear below)
sidebar.createLine(new Sidebar.ScoreboardLine("rank", Component.text("Rank: Admin"), 9));
```

### Updating Lines

You can update the content or score of a line dynamically using its unique ID.

```java
// Update the text component
sidebar.updateLineContent("score", Component.text("Score: 20"));

// Update the score (position 1-15)
sidebar.updateLineScore("score", 5);
```

### Removing Lines

To remove a line, reference its ID.

```java
sidebar.removeLine("score");
```

## Tab List

The Tab List (player list) is managed per-player, but Minestom allows controlling the objective display (like ping or health) via the `TabList` class which extends `Scoreboard`.

:::note
The header and footer of the tab list are handled directly on the `Player` or via Adventure's `Audience` API: `player.sendPlayerListHeaderAndFooter(...)`.
:::

### Creating a TabList Objective

To display scores in the tab list (often used for Ping or Health), use the `TabList` class.

```java
import net.minestom.server.scoreboard.TabList;
import net.minestom.server.network.packet.server.play.ScoreboardObjectivePacket;

// Create a TabList with a name and type (INTEGER or HEARTS)
TabList tabList = new TabList("my-tab-list", ScoreboardObjectivePacket.Type.INTEGER);

// Add viewers to sync this display
tabList.addViewer(player);
```

## Below Name Tag

This displays a tag below the player's nameplate, often used for health or custom stats.

```java
import net.minestom.server.scoreboard.BelowNameTag;

// Create the tag, giving it an ID and a default label
BelowNameTag belowName = new BelowNameTag("health", Component.text("10/20"));

// Add viewers who should see this tag under others' names
belowName.addViewer(player);
```

## Team Manager

Teams are used to group entities (mostly players) to share properties like name tag visibility, collision rules, prefixes, suffixes, and glowing colors.

Teams are managed by the `TeamManager`.

```java
import net.minestom.server.MinecraftServer;
import net.minestom.server.scoreboard.TeamManager;
import net.minestom.server.scoreboard.Team;

TeamManager teamManager = MinecraftServer.getTeamManager();
```

### Creating a Team

You can create a team with a simple registry name.

```java
Team team = teamManager.createTeam("red-team");
```

### Configuring Capabilities

Teams have several configurable properties:

- **Display Name**: The name shown in /team list (server-side only mostly).
- **Prefix/Suffix**: Shown before/after member names in chat and tab list.
- **Color**: Determines the color of the glowing effect and default name color.
- **Friendly Fire**: Whether players in the same team can hurt each other.
- **See Invisible**: Whether players can see invisible teammates.
- **Name Tag Visibility**: Who can see the name tags of team members.
- **Collision Rule**: How players collide with teammates.

```java
import net.kyori.adventure.text.format.NamedTextColor;
import net.minestom.server.network.packet.server.play.TeamsPacket;

// Set visual properties
team.setTeamDisplayName(Component.text("Red Team"));
team.setPrefix(Component.text("[Red] ", NamedTextColor.RED));
team.setSuffix(Component.text(" [Member]", NamedTextColor.GRAY));
team.setTeamColor(NamedTextColor.RED);

// Set behavioral properties
team.setAllowFriendlyFire(false);
team.setSeeInvisiblePlayers(true);
team.setNameTagVisibility(TeamsPacket.NameTagVisibility.ALWAYS);
team.setCollisionRule(TeamsPacket.CollisionRule.PUSH_OTHER_TEAMS);

// Apply changes (sends update packet to viewers)
team.sendUpdatePacket();
```

### Managing Members

Members are tracked by their username (for players) or UUID string (for entities).

```java
// Add a player to the team
team.addMember(player.getUsername());

// Add an entity (like a tamed wolf) to the team
team.addMember(entity.getUuid().toString());

// Remove a member
team.removeMember(player.getUsername());
```

### Deleting a Team

To remove a team globally:

```java
teamManager.deleteTeam(team);
```

