< [Documentation](../../Readme.md) / [Modding](../Readme.md) / [Map Object Format](../Map_Object_Format.md) / Rewardable

## Table of Contents
- [Base object definition](#base-object-definition)
- [Configurable object definition](#configurable-object-definition)
- [Base object definition](#base-object-definition)
- [Reset Parameters definition](#reset-parameters-definition)
- [Appear Chance definition](#appear-chance-definition)
- [Configurable Properties](#configurable-properties)
- - [Current Day](#current-day)
- - [Resource](#resource)
- - [Experience](#experience)
- - [Hero Level](#hero-level)
- - [Mana Points](#mana-points)
- - [Mana Percentage](#mana-percentage)
- - [Movement Points](#movement-points)
- - [Movement Percentage](#movement-percentage)
- - [Primary Skills](#primary-skills)
- - [Secondary Skills](#secondary-skills)
- - [Bonus System](#bonus-system)
- - [Artifacts](#artifacts)
- - [Spells](#spells)
- - [Creatures](#creatures)
- - [Creatures Change](#creatures-change)
- - [Spell cast](#spell-cast)

## Base object definition
Rewardable object is defined similarly to other objects, with key difference being `handler`. This field must be set to `"handler" : "configurable"` in order for vcmi to use this mode.
```jsonc
{
  "baseObjectName" : {
    "name" : "Object name",
    "handler" : "configurable", 
    "types" : {
      "objectName" : 
      {
        "rmg" : {
          "value"    : 2500,
          "rarity"  : 25,
          "zoneLimit" : 1
        },
        
        // Standard definition of object templates
        "templates" : {
          "avwrhscr" : {
            "animation" : "warehouses/avwrhscr", 
            "visitableFrom" : [ "---", "-++", "+++" ], 
            "mask" : ["VVV","VVV","VBA"]
          }
        }

        // See Configurable object definition section

      }
    }
  }
}
```

## Configurable object definition
```jsonc
// List of potential rewards
"rewards" : [
  {
    // see Appear Chance definition section
    "appearChance" : {
    },

    // Conditions to receive reward. Hero can only see this reward if he fulfills limiter
    "limiter" : {
      
      // additional list of conditions. Limiter will be valid if any of these conditions are true
      "anyOf" : [
        {
          // See "Configurable Properties" section for additiona parameters
          <additional properties>
        }
      ]

      // additional list of conditions. Limiter will be valid only if none of these conditions are true
      "noneOf" : [
        {
          // See "Configurable Properties" section for additiona parameters
          <additional properties>
        }
      ]

      // See "Configurable Properties" section for additiona parameters
      <additional properties>
    }
    
    // message that will be shown if this is the only available award
    "message": "{Warehouse of Crystal}"

    // object will be disappeared after taking reward is set to true
    "removeObject": false

    // See "Configurable Properties" section for additiona parameters
    <additional properties>
  }
],

// If true, hero can not move to visitable tile of the object and will access this object from adjacent tile (e.g. Treasure Chest)
"blockedVisitable" : true,

// Message that will be shown if there are no applicable awards
"onEmptyMessage": "",

// Alternatively, rewards for empty state:
// Format is identical to "rewards" section, allowing to fine-tune behavior in this case, including giving awards or different messages to explain why object is "empty". For example, Tree of Knowledge will give different messages depending on whether it asks for gold or crystals
"onEmpty" : [
]

// Message that will be shown if there are multiple selectable awards to choose from
"onSelectMessage" : "",

// Message that will be shown if this object has been already visited before
"onVisitedMessage" : "{Warehouse of Crystal}\r\n\r\nThe owner of the storage is apologising: 'I am sorry Milord, no crystal here. Please, return next week!'",

// Alternatively, rewards for visited state:
// Format is identical to "rewards" section, allowing to fine-tune behavior of already visited object, including potentially giving bonuses to player, e.g. Warrior's Tomb give -3 morale for visiting such object.
"onVisited" : [
]

// if true, then player can refuse from reward and don't select anything
// Note that in this case object will not become "visited" and can still be revisited later
"canRefuse": true,

// Controls when object state will be reset, allowing potential revisits. See Reset Parameters definition section
"resetParameters" : {
}

// determines who can revisit object before reset
// "once",     - object can only be visited once. First visitor takes it all.
// "hero",     - object can be visited if this hero has not visited it before
// "player",   - object can be visited if this player has not visited it before
// "bonus"     - object can be visited if hero no longer has bonus from this object (including any other object of the same type)
// "unlimited" - no restriction on revisiting.
"visitMode" : "unlimited", 

//determines way to select granted rewards if multiple options are available
// "selectFirst",  - first reward which passes "limiter" will be granted to player
// "selectPlayer", - player will be allowed to choose between rewards (e.g. treasure chest)
// "selectRandom", - granted reward will be picked randomly when hero visits object
"selectMode" : "selectFirst"

}
```

## Reset Parameters definition
This property describes how object state should be reset. Objects without this field will never reset its state.
- Period describes interval between object resets in day. Periods are counted from game start and not from hero visit, so reset duration of 7 will always reset object on new week & duration of 28 will always reset on new month.
- If `visitors` is set to true, game will reset list of visitors (heroes and players) on start of new period, allowing revisits of objects with `visitMode` set to `once`, `hero`, or `player`. Objects with visit mode set to `bonus` are not affected. In order to allow revisit such objects use appropriate bonus duration (e.g. `ONE_DAY` or `ONE_WEEK`) instead.
- If `rewards` is set to true, object will re-randomize its provided rewards, similar to such H3 objects as "Fountain of Fortune" or "Windmill"

```jsonc
"resetParameters" : {
    "period" : 7,
    "visitors" : true,
    "rewards" : true
}
```

## Appear Chance definition
This property describes chance for reward to be selected.
When object is initialized on map load, game will roll a "dice" - random number in range 0-99, and pick all awards that have appear chance within selected number

```jsonc
    "appearChance": 
    {
      // (Advanced) rewards with different dice number will get different dice number
      // This allows (for example) choosing two rewards randomly, independent from each other
      // For H3 objects, this is generally not needed and this field can be omitted
      "dice": 1,
  
      // reward will be selected only if random roll value is greater or equal than this
      "min" : 33,
      
      // reward will be selected only if random roll value is lower than this
      "max" : 66
    },
```

## Configurable Properties
Unless stated othervice, all numbers in this section can be replaced with random values, e.g.
```jsonc
"minLevel" : { "min" : 5, "max" : 10 } // select random number between 5-10, including both 5 & 10
"minLevel" : [ 2, 4, 6, 8, 10] // (VCMI 1.2) select random number out of provided list, with equal chance for each
```

In this case, actual value for minLevel will be picked randomly. 
Keep in mind, that all randomization is performed on map load and on object reset (if `rewards` field in `resetParameter` was set).

### Current Day
- Can only be used as limiter. To pass, current day of week should be equal to this value. 1 = first day of the week, 7 = last day

```jsonc
"dayOfWeek" : 0 
```

- Can only be used as limiter. To pass, number of days since game started must be at equal or greater than this value

```jsonc
"daysPassed" : 8
```

### Resource
- Can be used as limiter. To pass, player needs to have specified resources. Note that limiter will NOT take resources.
- Can be used as reward to grant resources to player
- If negative value is used as reward, it will be used as cost and take resources from player

```jsonc
"resources": {
    "crystal" : 6,
    "gold" : -1000,
}, 
```
- Alternative format that allows random selection of a resource type

```jsonc
"resources": [
    {
        "list" : [ "wood", "ore" ],
        "amount" : 10
    },
    {
        "type" : "gold",
        "amount" : 1000
    }
],
```

### Experience
- Can be used as limiter
- Can be used as reward to grant experience to hero

```jsonc
"heroExperience" : 1000, 
```

### Hero Level
- Can be used as limiter. Hero requires to have at least specified level
- Can be used as reward, will grant hero experience amount equal to the difference between the hero's next level and current level (Tree of Knowledge)

```jsonc
"heroLevel" : 1,
```

### Mana Points
- Can be used as limiter. Hero must have at least specific mana amount
- Can be used as reward, to give mana points to hero. Mana points may go above mana pool limit.
- If negative value is used as reward, it will be used as cost and take mana from player

```jsonc
"manaPoints": -10, 
```

- If giving mana points puts hero above mana pool limit, any overflow will be multiplied by specified percentage. If set to 0, mana will not go above mana pool limit.

```jsonc
"manaOverflowFactor" : 50,
```

### Mana Percentage
- Can be used as limiter. Hero must have at least specific mana percentage
- Can be used to set hero mana level to specified percentage value, not restricted to mana pool limit (Magic Well, Mana Spring)

```jsonc
"manaPercentage": 200, 
```

### Movement Points
- Can NOT be used as limiter
- Can be used as reward, to give movement points to hero. Movement points may go above mana pool limit.

```jsonc
"movePoints": 200,
```
    
### Movement Percentage
- Can NOT be used as limiter
- Can be used to set hero movement points level to specified percentage value. Value of 0 will take away any remaining movement points

```jsonc
"movePercentage": 50,
```

### Primary Skills
- Can be used as limiter, hero must have primary skill at least at specified level
- Can be used as reward, to increase hero primary skills by selected value
- If reward value is negative, value will be used as cost, decreasing primary skill
- Each primary skill can be explicitly specified or randomly selected
- Possible values: `"attack", "defence", "spellpower", "knowledge"`

```jsonc
"primary": [
    {
        // Specific primary skill
        "type" : "defence",
        "amount" : 1
    },
    {
        // Primary skill will be selected randomly from the list
        "anyOf" : ["attack", "defence],
        "min" : 1,
        "max" : 3
    },
    {
        // Primary skill will be selected randomly, expect those
        "noneOf" : ["knowledge"],
        "amount" : 1
    },
    {
        // Primary skill will be selected randomly
        "amount" : 3
    }
]
```

### Secondary Skills
- Can be used as limiter, hero must have secondary skill at least at specified level
- Can be used as reward, to grant secondary skills to hero
- If hero already has specified skill, the skills will be leveled up specified number of times
- If hero does not have selected skill and have free skill slots, he will receive skill at specified level
- Possible values: 1 (basic), 2 (advanced), 3 (expert)
- Each secondary skill can be explicitly specified or randomly selected
```jsonc
"secondary": [
    {
        // Specific skill
        "type" : "wisdom",
        "amount" : 1
    },
    {
        // Skill will be selected randomly from the list
        "anyOf" : ["airMagic", "waterMagic", "earthMagic", "fireMagic"],
        "min" : 1,
        "max" : 3
    },
    {
        // Skill will be selected randomly from all allowed, expect those
        "noneOf" : ["necromancy", "leadership"],
        "amount" : 1
    },
    {
        // Skill will be selected randomly from all allowed
        "amount" : 3
    }
]
```

### Bonus System
- Can be used as reward, to grant bonus to player
- if present, MORALE and LUCK bonus will add corresponding image component to UI.
- Note that unlike most values, parameter of bonuses can NOT be randomized
- Description can be string or number of corresponding string from `arraytxt.txt`

```jsonc
"bonuses" : [
    {
        "type" : "MORALE", 
        "val" : 1, 
        "duration" : "ONE_BATTLE", 
        "desription" : 94 
    }
]
```

### Artifacts
- Can be used as limiter, hero must have artifact either equipped or in backpack
- Can be used as reward, to give new artifact to a hero
- Artifacts added as reward will be used for text substitution. First `%s` in text string will be replaced with name of an artifact

```jsonc
"artifacts": [
    "ribCage"
],
```

- Alternative format, random artifact generation.
- For artifact class possible values are "TREASURE", "MINOR", "MAJOR", "RELIC"
- Artifact value range can be specified with min value and max value

```jsonc
"artifacts": [
    {
        "class" : "TREASURE",
        "minValue" : 5000,
        "maxValue" : 10000
    }
],
```

### Spells
- Can be used as limiter
- Can be used as reward, to give new spell to a hero
- Spells added as reward will be used for text substitution. First `%s` in text string will be replaced with spell name

```jsonc
"spells": [
    "magicArrow"
],
```

- Alternative format, random spell selection
- Spell can be selected from specifically selected school

```jsonc
"spells": [
    {
        "level" : 1,
        "school" : "fire",
    }
],
```

### Creatures
- Can be used as limiter
- Can be used as reward, to give new creatures to a hero
- If hero does not have enough free slots, game will show selection dialog to pick troops to keep
- It is possible to specify probabilty to receive upgraded creature
```jsonc
"creatures" : [
    {
        "creature" : "archer",
        "upgradeChance" : 30,
        "amount" : 20,
    }
],
```

### Creatures Change
- Can NOT be used as limiter
- Can be used as reward, to replace creatures in hero army. It is possible to use this parameter both for upgrades of creatures as well as for changing them into completely unrelated creature, e.g. similar to Skeleton Transformer
- This parameter will not change creatures given by `creatures` parameter on the same visit

```jsonc
"changeCreatures" : {
    "cavalier" : "champion"
}
```

### Spell cast
- Can NOT be used as limiter
- As reward, instantly casts adventure map spell for visiting hero. All checks for spell book, wisdom or presence of mana will be ignored. It's possible to specify school level at which spell will be casted. If it's necessary to reduce player's mana or do some checks, they shall be introduced as limiters and other rewards
- School level possible values: 1 (basic), 2 (advanced), 3 (expert)

```jsonc
"spellCast" : {
    "spell" : "townPortal",
    "schoolLevel": 3
}
```