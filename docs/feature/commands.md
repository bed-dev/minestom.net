# Commands

Commands are the main communication channel between the server and players (or console). Minestom provides a powerful, Brigadier-compatible command system with full support for auto-completion, suggestions, and complex parsing.

## Basic Structure

All custom commands extend the `Command` class.

```java
import net.minestom.server.command.builder.Command;
import net.minestom.server.command.builder.arguments.ArgumentType;

public class MyCommand extends Command {

    public MyCommand() {
        super("mycommand", "alias1", "alias2");

        // default execution (no arguments)
        setDefaultExecutor((sender, context) -> {
            sender.sendMessage("You executed the base command!");
        });
    }
}
```

To register the command:

```java
MinecraftServer.getCommandManager().register(new MyCommand());
```

## Syntaxes

A "syntax" defines a specific combination of arguments that triggers an executor. You can have multiple syntaxes for a single command.

```java
var numberArg = ArgumentType.Integer("number");
var stringArg = ArgumentType.String("text");

// Syntax: /mycommand <number>
addSyntax((sender, context) -> {
    int num = context.get(numberArg);
    sender.sendMessage("Number: " + num);
}, numberArg);

// Syntax: /mycommand <number> <text>
addSyntax((sender, context) -> {
    int num = context.get(numberArg);
    String text = context.get(stringArg);
    sender.sendMessage("Number: " + num + ", Text: " + text);
}, numberArg, stringArg);
```

## Argument Types

Minestom includes all standard Brigadier arguments and Minecraft-specific ones via the `ArgumentType` factory.

### Basic Types
- `ArgumentType.Boolean("id")`
- `ArgumentType.Integer("id")`
- `ArgumentType.Float("id")`
- `ArgumentType.String("id")` (Quoted string)
- `ArgumentType.Word("id")` (Single word)
- `ArgumentType.StringArray("id")` (Greedy string, takes rest of input)
- `ArgumentType.Enum("id", MyEnum.class)`

### Minecraft Types
- `ArgumentType.Entity("id")` (Selectors like @a, @p)
- `ArgumentType.ItemStack("id")`
- `ArgumentType.BlockState("id")`
- `ArgumentType.Component("id")` (JSON text)
- `ArgumentType.Color("id")`
- `ArgumentType.Time("id")`
- `ArgumentType.Particle("id")`
- `ArgumentType.ResourceLocation("id")`
- `ArgumentType.IntRange("id")` (e.g., 1..10)
- `ArgumentType.FloatRange("id")`
- `ArgumentType.NbtCompound("id")`

## Optional Arguments

Arguments can be marked as optional with a default value.

```java
var optionalArg = ArgumentType.Integer("number").setDefaultValue(5);

addSyntax((sender, context) -> {
    // Will return 5 if not provided
    int num = context.get(optionalArg);
}, optionalArg);
```

**Note:** Optional arguments must be placed at the end of the argument list.

## Validation & Callbacks

You can validate arguments before the executor runs using callbacks. This is useful for range checks or custom validation.

```java
var ageArg = ArgumentType.Integer("age");

ageArg.setCallback((sender, exception) -> {
    sender.sendMessage("Invalid age inputted: " + exception.getInput());
});
```

## Suggestions (Tab Completion)

You can provide custom tab-completions for arguments.

```java
import net.minestom.server.command.builder.suggestion.SuggestionEntry;

var typeArg = ArgumentType.Word("type");

typeArg.setSuggestionCallback((sender, context, suggestion) -> {
    suggestion.addEntry(new SuggestionEntry("fast"));
    suggestion.addEntry(new SuggestionEntry("slow"));
    suggestion.addEntry(new SuggestionEntry("medium"));
});
```

## Command Conditions

Conditions allow you to restrict who can execute a command (e.g., permissions or sender type).

```java
setCondition((sender, commandString) -> {
    if (!sender.hasPermission("admin.command")) {
        sender.sendMessage("You do not have permission!");
        return false;
    }
    return true;
});
```

## Subcommands

You can nest commands to create structures like `/plot claim`, `/plot auto`.

```java
public class PlotCommand extends Command {
    public PlotCommand() {
        super("plot");
        
        addSubcommand(new PlotClaimCommand());
        addSubcommand(new PlotAutoCommand());
    }
}
```

## Command Data

Syntaxes can return data, which is useful for cross-command communication or scripting.

```java
import net.minestom.server.command.builder.CommandData;

addSyntax((sender, context) -> {
    context.setReturnData(new CommandData().set("result", 42));
}, argument);
```

Executing explicitly to get data:

```java
import net.minestom.server.command.builder.CommandResult;

CommandResult result = MinecraftServer.getCommandManager().executeServerCommand("mycommand 10");
if (result.getType() == CommandResult.Type.SUCCESS) {
    int value = result.getCommandData().get("result");
}
```

