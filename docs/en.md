# Recruiting — defeated enemies who join the party

Recruiting lets an enemy beaten in battle be captured and later fight on the player's side. After a victory each defeated enemy has a chance to be captured; captured characters are listed in the **Recruits** panel, and any of them can be added to the party or removed again. While captured, a recruit lives by rules of its own — in Cthangover it gets hungry.

The mod does not depend on any battle system: it listens for the platform's "characters defeated" event, so it works with FF Battle, Card Battle or anything else that reports defeated characters. Which characters can be captured, and under which conditions, is decided by the game's data and by other mods.

## The Recruits panel

Open it with its toolbar button. At the start of Cthangover nobody has been captured yet:

![The Recruits panel: the title Recruits, the line "Captured enemies · any of them can join the party", "No captured recruits yet" and at the bottom "Captured: 0 · in party: 0".](assets/recruits-panel.jpg)

Each captured recruit gets a row with its portrait, name and health, and a button: **Add to party** or **Remove from party**. The footer counts the recruits and how many of them are in the party.

## How a capture happens

After a victory, for every defeated enemy:

1. The enemy's character file must have a `RecruitmentChance` above 0.
2. Only one recruit of each enemy type: if this type is already captured, it is skipped.
3. Every registered **condition** must allow it. This mod brings one: the player must have the skill `slave_master` (in Cthangover Marao has it from the start).
4. A roll from 1 to 100 must not exceed the chance.

Then the enemy joins the roster with 10 health. In Cthangover the only character that can be captured is met later in the story, with a chance of 50%:

```json
{ "Id": "werewolf_girl_1", "RecruitmentChance": 50 }
```

## In the party

**Add to party** makes the recruit an ordinary party member: it appears on the character panel, fights in battles with its own actions, can be the target of items and is saved with the party. Health lost in battle stays with the recruit. **Remove from party** keeps its actions and experience for the next time it joins.

## Recruits' needs

Every recruit runs the registered **behaviours** on each tick of the game clock. The mod brings one, nutrition: a recruit that has not eaten for 24 game hours loses 1 health per tick. In the current game nothing feeds a recruit yet, so a captured recruit starts to starve a day after the capture.

The roster, health and behaviour state are saved with the game.

## For mod authors

- A **condition** is a class implementing `IRecruitCondition`; the recruiting mod finds it in any loaded mod. Return false to forbid a capture.
- A **behaviour** implements `IRecruitBehaviour`: it is configured when a recruit is captured, called on every tick, and removed with the recruit. Its state lives in the recruit's properties.
- Code can react to `RecruitJoinedEvent` and `RecruitLeftEvent`.

## If something goes wrong

1. **Nobody is ever captured.** The enemy's `RecruitmentChance` is 0, a condition forbids it (the player lacks the `slave_master` skill), or the roll failed.
2. **The same enemy is not captured twice.** One recruit per enemy type, by design.
3. **A recruit's health keeps falling.** Nutrition: it has not eaten for a day, and there is no way to feed it yet.
