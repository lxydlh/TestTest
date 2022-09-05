# GASDocumentation
这是一个通过简单的多人示例工程展示的对UE5的GameplayAbilitySystem插件(GAS)的理解。这并不是官方文档，也不保证全部正确。

此文档的目的是解释GAS中的主要概念和类，并根据我的经验给出一些额外的注释。在社区的用户中有很多关于GAS的“部落知识”，我打算在这里分享我所有的知识，

这个示例工程和文档是基于UE5.0.X。其实还有针对老引擎版本的文档，但是那些老版本不会再维护了，并且可能有更多的BUG和失效信息。


[GASShooter](https://github.com/tranek/GASShooter) 是一个使用GAS制作的多人FPS/TPS的姊妹演示工程。

最好的文档始终是插件源码。

<a name="table-of-contents"></a>
## 目录

> 1. [GameplayAbilitySystem插件简介](#intro)
> 1. [示例工程](#sp)
> 1. [通过GAS设置工程](#setup)
> 1. [概念](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [同步模式（Replication Mode）](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [设置与初始化](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [响应`GameplayTags`的变化](#concepts-gt-change)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [`Attribute`的定义](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [`BaseValue` 与 `CurrentValue`](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [`元属性Meta Attributes`](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [响应`Attribute`的变化](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [派生属性](#concepts-a-derived)  
>    4.4 [`AttributeSet`](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [`AttributeSet`的定义](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [`AttributeSet`的设计](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [具有独立`Attributes`的子组件](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [运行中增删`AttributeSets`](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [物品的`AttributeSets` (如武器弹药)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [物品上的普通浮点数](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [物品上的`AttributeSet`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [物品上的`AbilitySystemComponent`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [定义Attributes](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [初始化Attributes](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [函数PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [函数PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [函数OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [GameplayEffect的定义](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [GameplayEffect的应用](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [GameplayEffect的移除](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [GameplayEffect修饰符](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乘除修饰符](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [修饰符上的GameplayTags](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [存储GameplayEffects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [授予Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [GameplayEffect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>
## 1. Intro to the GameplayAbilitySystem Plugin
From the [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):
>The Gameplay Ability System is a highly-flexible framework for building abilities and attributes of the type you might find in an RPG or MOBA title. You can build actions or passive abilities for the characters in your games to use, status effects that can build up or wear down various attributes as a result of these actions, implement "cooldown" timers or resource costs to regulate the usage of these actions, change the level of the ability and its effects at each level, activate particle or sound effects, and more. Put simply, this system can help you to design, implement, and efficiently network in-game abilities as simple as jumping or as complex as your favorite character's ability set in any modern RPG or MOBA title.

The GameplayAbilitySystem plugin is developed by Epic Games and comes with Unreal Engine 5 (UE5). It has been battle tested in AAA commercial games such as Paragon and Fortnite among others.

The plugin provides an out-of-the-box solution in single and multiplayer games for:
* Implementing level-based character abilities or skills with optional costs and cooldowns ([GameplayAbilities](#concepts-ga))
* Manipulating numerical `Attributes` belonging to actors ([Attributes](#concepts-a))
* Applying status effects to actors ([GameplayEffects](#concepts-ge))
* Applying `GameplayTags` to actors ([GameplayTags](#concepts-gt))
* Spawning visual or sound effects ([GameplayCues](#concepts-gc))
* Replication of everything mentioned above

In multiplayer games, GAS provides support for [client-side prediction](#concepts-p) of:
* Ability activation
* Playing animation montages
* Changes to `Attributes`
* Applying `GameplayTags`
* Spawning `GameplayCues`
* Movement via `RootMotionSource` functions connected to the `CharacterMovementComponent`.

**GAS must be set up in C++**, but `GameplayAbilities` and `GameplayEffects` can be created in Blueprint by the designers.

Current issues with GAS:
* `GameplayEffect` latency reconciliation (can't predict ability cooldowns resulting in players with higher latencies having lower rate of fire for low cooldown abilities compared to players with lower latencies).
* Cannot predict the removal of `GameplayEffects`. We can however predict adding `GameplayEffects` with the inverse effects, effectively removing them. This is not always appropriate or feasible and still remains an issue.
* Lack of boilerplate templates, multiplayer examples, and documentation. Hopefully this helps with that!

**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>
## 2. Sample Project
A multiplayer third person shooter sample project is included with this documentation aimed at people new to the GameplayAbilitySystem Plugin but not new to Unreal Engine 5. Users are expected to know C++, Blueprints, UMG, Replication, and other intermediate topics in UE5. This project provides an example of how to set up a basic third person shooter multiplayer-ready project with the `AbilitySystemComponent` (`ASC`) on the `PlayerState` class for player/AI controlled heroes and the `ASC` on the `Character` class for AI controlled minions.

The goal is to keep this project simple while showing the GAS basics and demonstrating some commonly requested abilities with well-commented code. Because of its beginner focus, the project does not show advanced topics like [predicting projectiles](#concepts-p-spawn).

Concepts demonstrated:
* `ASC` on `PlayerState` vs `Character`
* Replicated `Attributes`
* Replicated animation montages
* `GameplayTags`
* Applying and removing `GameplayEffects` inside of and externally from `GameplayAbilities`
* Applying damage mitigated by armor to change health of a character
* `GameplayEffectExecutionCalculations`
* Stun effect
* Death and respawn
* Spawning actors (projectiles) from an ability on the server
* Predictively changing the local player's speed with aim down sights and sprinting
* Constantly draining stamina to sprint
* Using mana to cast abilities
* Passive abilities
* Stacking `GameplayEffects`
* Targeting actors
* `GameplayAbilities` created in Blueprint
* `GameplayAbilities` created in C++
* Instanced per `Actor` `GameplayAbilities`
* Non-Instanced `GameplayAbilities` (Jump)
* Static `GameplayCues` (FireGun projectile impact particle effect)
* Actor `GameplayCues` (Sprint and Stun particle effects)

The hero class has the following abilities:

| Ability                    | Input Bind          | Predicted  | C++ / Blueprint | Description                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | Space Bar           | Yes        | C++             | Makes the hero jump.                                                                                                                                                         |
| Gun                        | Left Mouse Button   | No         | C++             | Fires a projectile from the hero's gun. The animation is predicted but the projectile is not.                                                                                |
| Aim Down Sights            | Right Mouse Button  | Yes        | Blueprint       | While the button is held, the hero will walk slower and the camera will zoom in to allow more precise shots with the gun.                                                    |
| Sprint                     | Left Shift          | Yes        | Blueprint       | While the button is held, the hero will run faster draining stamina.                                                                                                         |
| Forward Dash               | Q                   | Yes        | Blueprint       | The hero dashes forward at the cost of stamina.                                                                                                                              |
| Passive Armor Stacks       | Passive             | No         | Blueprint       | Every 4 seconds the hero gains a stack of armor up to a maximum of 4 stacks. Receiving damage removes one stack of armor.                                                    |
| Meteor                     | R                   | No         | Blueprint       | Player targets a location to drop a meteor on the enemies causing damage and stunning them. The targeting is predicted while spawning the meteor is not.                     |

It does not matter if `GameplayAbilities` are created in C++ or Blueprint. A mixture of the two were used here for example of how to do them in each language.

Minions do not come with any predefined `GameplayAbilities`. The Red Minions have more health regen while the Blue Minions have higher starting health.

For `GameplayAbility` naming, I used the suffix `_BP` to denote the `GameplayAbility's` logic was created in Blueprint. The lack of suffix means the logic was created in C++.

**Blueprint Asset Naming Prefixes**

| Prefix      | Asset Type          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 3. Setting Up a Project Using GAS
Basic steps to set up a project using GAS:
1. Enable GameplayAbilitySystem plugin in the Editor
1. Edit `YourProjectName.Build.cs` to add `"GameplayAbilities", "GameplayTags", "GameplayTasks"` to your `PrivateDependencyModuleNames`
1. Refresh/Regenerate your Visual Studio project files
1. Starting with 4.24, it is now mandatory to call `UAbilitySystemGlobals::Get().InitGlobalData()` to use [`TargetData`](#concepts-targeting-data). The Sample Project does this in `UAssetManager::StartInitialLoading()`. See [`InitGlobalData()`](#concepts-asg-initglobaldata) for more information.

That's all that you have to do to enable GAS. From here, add an [`ASC`](#concepts-asc) and [`AttributeSet`](#concepts-as) to your `Character` or `PlayerState` and start making [`GameplayAbilities`](#concepts-ga) and [`GameplayEffects`](#concepts-ge)!

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
The `AbilitySystemComponent` (`ASC`) is the heart of GAS. It's a `UActorComponent` ([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)) that handles all interactions with the system. Any `Actor` that wishes to use [`GameplayAbilities`](#concepts-ga), have [`Attributes`](#concepts-a), or receive [`GameplayEffects`](#concepts-ge) must have one `ASC` attached to them. These objects all live inside of and are managed and replicated by (with the exception of `Attributes` which are replicated by their [`AttributeSet`](#concepts-as)) the `ASC`. Developers are expected but not required to subclass this.

The `Actor` with the `ASC` attached to it is referred to as the `OwnerActor` of the `ASC`. The physical representation `Actor` of the `ASC` is called the `AvatarActor`. The `OwnerActor` and the `AvatarActor` can be the same `Actor` as in the case of a simple AI minion in a MOBA game. They can also be different `Actors` as in the case of a player controlled hero in a MOBA game where the `OwnerActor` is the `PlayerState` and the `AvatarActor` is the hero's `Character` class. Most `Actors` will have the `ASC` on themselves. If your `Actor` will respawn and need persistence of `Attributes` or `GameplayEffects` between spawns (like a hero in a MOBA), then the ideal location for the `ASC` is on the `PlayerState`.

**Note:** If your `ASC` is on your `PlayerState`, then you will need to increase the `NetUpdateFrequency` of your `PlayerState`. It defaults to a very low value on the `PlayerState` and can cause delays or perceived lag before changes to things like `Attributes` and `GameplayTags` happen on the clients. Be sure to enable [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency), Fortnite uses it.

Both, the `OwnerActor` and the `AvatarActor` if different `Actors`, should implement the `IAbilitySystemInterface`. This interface has one function that must be overriden, `UAbilitySystemComponent* GetAbilitySystemComponent() const`, which returns a pointer to its `ASC`. `ASCs` interact with each other internally to the system by looking for this interface function.

The `ASC` holds its current active `GameplayEffects` in `FActiveGameplayEffectsContainer ActiveGameplayEffects`.

The `ASC` holds its granted `Gameplay Abilities` in `FGameplayAbilitySpecContainer ActivatableAbilities`. Any time that you plan to iterate over `ActivatableAbilities.Items`, be sure to add `ABILITYLIST_SCOPE_LOCK();` above your loop to lock the list from changing (due to removing an ability). Every `ABILITYLIST_SCOPE_LOCK();` in scope increments `AbilityScopeLockCount` and then decrements when it falls out of scope. Do not try to remove an ability inside the scope of `ABILITYLIST_SCOPE_LOCK();` (the clear ability functions check `AbilityScopeLockCount` internally to prevent removing abilities if the list is locked).

<a name="concepts-asc-rm"></a>
### 4.1.1 Replication Mode
The `ASC` defines three different replication modes for replicating `GameplayEffects`, `GameplayTags`, and `GameplayCues` - `Full`, `Mixed`, and `Minimal`. `Attributes` are replicated by their `AttributeSet`.

| Replication Mode   | When to Use                             | Description                                                                                                                    |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | Single Player                           | Every `GameplayEffect` is replicated to every client.                                                                          |
| `Mixed`            | Multiplayer, player controlled `Actors` | `GameplayEffects` are only replicated to the owning client. Only `GameplayTags` and `GameplayCues` are replicated to everyone. |
| `Minimal`          | Multiplayer, AI controlled `Actors`     | `GameplayEffects` are never replicated to anyone. Only `GameplayTags` and `GameplayCues` are replicated to everyone.           |

**Note:** `Mixed` replication mode expects the `OwnerActor's` `Owner` to be the `Controller`. `PlayerState's` `Owner` is the `Controller` by default but `Character's` is not. If using `Mixed` replication mode with the `OwnerActor` not the `PlayerState`, then you need to call `SetOwner()` on the `OwnerActor` with a valid `Controller`.

Starting with 4.24, `PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 Setup and Initialization
`ASCs` are typically constructed in the `OwnerActor's` constructor and explicitly marked replicated. **This must be done in C++**.

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

The `ASC` needs to be initialized with its `OwnerActor` and `AvatarActor` on both the server and the client. You want to initialize after the `Pawn's` `Controller` has been set (after possession). Single player games only need to worry about the server path.

For player controlled characters where the `ASC` lives on the `Pawn`, I typically initialize on the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `PlayerController's` `AcknowledgePossession()` function.

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

For player controlled characters where the `ASC` lives on the `PlayerState`, I typically initialize the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `Pawn's` `OnRep_PlayerState()` function. This ensures that the `PlayerState` exists on the client.

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

If you get the error message `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` then you did not initialize your `ASC` on the client.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) are hierarchical names in the form of `Parent.Child.Grandchild...` that are registered with the `GameplayTagManager`. These tags are incredibly useful for classifying and describing the state of an object. For example, if a character is stunned, we could give it a `State.Debuff.Stun` `GameplayTag` for the duration of the stun.

You will find yourself replacing things that you used to handle with booleans or enums with `GameplayTags` and doing boolean logic on whether or not objects have certain `GameplayTags`.

When giving tags to an object, we typically add them to its `ASC` if it has one so that GAS can interact with them. `UAbilitySystemComponent` implements the `IGameplayTagAssetInterface` giving functions to access its owned `GameplayTags`.

Multiple `GameplayTags` can be stored in an `FGameplayTagContainer`. It is preferable to use a `GameplayTagContainer` over a `TArray<FGameplayTag>` since the `GameplayTagContainers` add some efficiency magic. While tags are standard `FNames`, they can be efficiently packed together in `FGameplayTagContainers` for replication if `Fast Replication` is enabled in the project settings. `Fast Replication` requires that the server and the clients have the same list of `GameplayTags`. This generally shouldn't be a problem so you should enable this option. `GameplayTagContainers` can also return a `TArray<FGameplayTag>` for iteration.

`GameplayTags` stored in `FGameplayTagCountContainer` have a `TagMap` that stores the number of instances of that `GameplayTag`. A `FGameplayTagCountContainer` may still have the `GameplayTag` in it but its `TagMapCount` is zero. You may encounter this while debugging if an `ASC` still has a `GameplayTag`. Any of the `HasTag()` or `HasMatchingTag()` or similar functions will check the `TagMapCount` and return false if the `GameplayTag` is not present or its `TagMapCount` is zero.

`GameplayTags` must be defined ahead of time in the `DefaultGameplayTags.ini`. The UE5 Editor provides an interface in the project settings to let developers manage `GameplayTags` without needing to manually edit the `DefaultGameplayTags.ini`. The `GameplayTag` editor can create, rename, search for references, and delete `GameplayTags`.

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

Searching for `GameplayTag` references will bring up the familiar `Reference Viewer` graph in the Editor showing all the assets that reference the `GameplayTag`. This will not however show any C++ classes that reference the `GameplayTag`.

Renaming `GameplayTags` creates a redirect so that assets still referencing the original `GameplayTag` can redirect to the new `GameplayTag`. I prefer if possible to instead create a new `GameplayTag`, update all the references manually to the new `GameplayTag`, and then delete the old `GameplayTag` to avoid creating a redirect.

In addition to `Fast Replication`, the `GameplayTag` editor has an option to fill in commonly replicated `GameplayTags` to optimize them further.

`GameplayTags` are replicated if they're added from a `GameplayEffect`. The `ASC` allows you to add `LooseGameplayTags` that are not replicated and must be managed manually. The Sample Project uses a `LooseGameplayTag` for `State.Dead` so that the owning clients can immediately respond to when their health drops to zero. Respawning manually sets the `TagMapCount` back to zero. Only manually adjust the `TagMapCount` when working with `LooseGameplayTags`. It is preferable to use the `UAbilitySystemComponent::AddLooseGameplayTag()` and `UAbilitySystemComponent::RemoveLooseGameplayTag()` functions than manually adjusting the `TagMapCount`.

Getting a reference to a `GameplayTag` in C++:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

For advanced `GameplayTag` manipulation like getting the parent or children `GameplayTags`, look at the functions offered by the `GameplayTagManager`. To access the `GameplayTagManager`, include `GameplayTagManager.h` and call it with `UGameplayTagManager::Get().FunctionName`. The `GameplayTagManager` actually stores the `GameplayTags` as relational nodes (parent, child, etc) for faster processing than constant string manipulation and comparisons.

`GameplayTags` and `GameplayTagContainers` can have the optional `UPROPERTY` specifier `Meta = (Categories = "GameplayCue")` that filters the tags in the Blueprint to show only `GameplayTags` that have the parent tag of `GameplayCue`. This is useful when you know the `GameplayTag` or `GameplayTagContainer` variable should only be used for `GameplayCues`.

Alternatively, there's a separate structure called `FGameplayCueTag` that encapsulates a `FGameplayTag` and also automatically filters `GameplayTags` in Blueprint to only show those tags with the parent tag of `GameplayCue`.

If you want to filter a `GameplayTag` parameter in a function, use the `UFUNCTION` specifier `Meta = (GameplayTagFilter = "GameplayCue")`. `GameplayTagContainer` parameters in functions can not be filtered. If you would like to edit your engine to allow this, look at how `SGameplayTagGraphPin::ParseDefaultValueData()` from `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` calls `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);` and passes `FilterString` to `SGameplayTagWidget` in `SGameplayTagGraphPin::GetListContent()`. The `GameplayTagContainer` version of these functions in `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` do not check for the meta field properties and pass along the filter.

The Sample Project extensively uses `GameplayTags`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 Responding to Changes in Gameplay Tags
The `ASC` provides a delegate for when `GameplayTags` are added or removed. It takes in a `EGameplayTagEventType` that can specify only to fire when the `GameplayTag` is added/removed or for any change in the `GameplayTag's` `TagMapCount`.

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

The callback function has a parameter for the `GameplayTag` and the new `TagCount`.
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute Definition
`Attributes` are float values defined by the struct [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html). These can represent anything from the amount of health a character has to the character's level to the number of charges that a potion has. If it is a gameplay-related numerical value belonging to an `Actor`, you should consider using an `Attribute` for it. `Attributes` should generally only be modified by [`GameplayEffects`](#concepts-ge) so that the ASC can [predict](#concepts-p) the changes.

`Attributes` are defined by and live in an [`AttributeSet`](#concepts-as). The `AttributeSet` is responsible for replicating `Attributes` that are marked for replication. See the section on [`AttributeSets`](#concepts-as) for how to define `Attributes`.

**Tip:** If you don't want an `Attribute` to show up in the Editor's list of `Attributes`, you can use the `Meta = (HideInDetailsView)` `property specifier`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs CurrentValue
An `Attribute` is composed of two values - a `BaseValue` and a `CurrentValue`. The `BaseValue` is the permanent value of the `Attribute` whereas the `CurrentValue` is the `BaseValue` plus temporary modifications from `GameplayEffects`. For example, your `Character` may have a movespeed `Attribute` with a `BaseValue` of 600 units/second. Since there are no `GameplayEffects` modifying the movespeed yet, the `CurrentValue` is also 600 u/s. If she gets a temporary 50 u/s movespeed buff, the `BaseValue` stays the same at 600 u/s while the `CurrentValue` is now 600 + 50 for a total of 650 u/s. When the movespeed buff expires, the `CurrentValue` reverts back to the `BaseValue` of 600 u/s.

Often beginners to GAS will confuse `BaseValue` with a maximum value for an `Attribute` and try to treat it as such. This is an incorrect approach. Maximum values for `Attributes` that can change or are referenced in abilities or UI should be treated as separate `Attributes`. For hardcoded maximum and minimum values, there is a way to define a `DataTable` with `FAttributeMetaData` that can set maximum and minimum values, but Epic's comment above the struct calls it a "work in progress". See `AttributeSet.h` for more information. To prevent confusion, I recommend that maximum values that can be referenced in abilities or UI be made as separate `Attributes` and hardcoded maximum and minimum values that are only used for clamping `Attributes` be defined as hardcoded floats in the `AttributeSet`. Clamping of `Attributes` is discussed in [PreAttributeChange()](#concepts-as-preattributechange) for changes to the `CurrentValue` and [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) for changes to the `BaseValue` from `GameplayEffects`.

Permanent changes to the `BaseValue` come from `Instant` `GameplayEffects` whereas `Duration` and `Infinite` `GameplayEffects` change the `CurrentValue`. Periodic `GameplayEffects` are treated like instant `GameplayEffects` and change the `BaseValue`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes
Some `Attributes` are treated as placeholders for temporary values that are intended to interact with `Attributes`. These are called `Meta Attributes`. For example, we commonly define damage as a `Meta Attribute`. Instead of a `GameplayEffect` directly changing our health `Attribute`, we use a `Meta Attribute` called damage as a placeholder. This way the damage value can be modified with buffs and debuffs in an [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) and can be further manipulated in the `AttributeSet`, for example subtracting the damage from a current shield `Attribute`, before finally subtracting the remainder from the health `Attribute`. The damage `Meta Attribute` has no persistence between `GameplayEffects` and is overriden by every one. `Meta Attributes` are not typically replicated.

`Meta Attributes` provide a good logical separation for things like damage and healing between "How much damage did we do?" and "What do we do with this damage?". This logical separation means our `Gameplay Effects` and `Execution Calculations` don't need to know how the Target handles the damage. Continuing our damage example, the `Gameplay Effect` determines how much damage and then the `AttributeSet` decides what to do with that damage. Not all characters may have the same `Attributes`, especially if you use subclassed `AttributeSets`. The base `AttributeSet` class may only have a health `Attribute`, but a subclassed `AttributeSet` may add a shield `Attribute`. The subclassed `AttributeSet` with the shield `Attribute` would distribute the damage received differently than the base `AttributeSet` class.

While `Meta Attributes` are a good design pattern, they are not mandatory. If you only ever have one `Execution Calculation` used for all instances of damage and one `Attribute Set` class shared by all characters, then you may be fine doing the damage distribution to health, shields, etc. inside of the `Execution Calculation` and directly modifying those `Attributes`. You'll only be sacrificing flexibility, but that may be okay for you.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Responding to Attribute Changes
To listen for when an `Attribute` changes to update the UI or other gameplay, use `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`. This function returns a delegate that you can bind to that will be automatically called whenever an `Attribute` changes. The delegate provides a `FOnAttributeChangeData` parameter with the `NewValue`, `OldValue`, and `FGameplayEffectModCallbackData`. **Note:** The `FGameplayEffectModCallbackData` will only be set on the server.

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

The Sample Project binds to the `Attribute` value changed delegates on the `GDPlayerState` to update the HUD and to respond to player death when health reaches zero.

A custom Blueprint node that wraps this into an `ASyncTask` is included in the Sample Project. It is used in the `UI_HUD` UMG Widget to update the health, mana, and stamina values. This `AsyncTask` will live forever until manually called `EndTask()`, which we do in the UMG Widget's `Destruct` event. See `AsyncTaskAttributeChanged.h/cpp`.

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attributes
To make an `Attribute` that has some or all of its value derived from one or more other `Attributes`, use an `Infinite` `GameplayEffect` with one or more `Attribute Based` or [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods). The `Derived Attribute` will update automatically when an `Attribute` that it depends on is updated.

The final formula for all the `Modifiers` on a `Derived Attribute` is the same formula for `Modifier Aggregators`. If you need calculations to happen in a certain order, do it all inside of an `MMC`.

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**Note:** If playing with multiple clients in PIE, you need to disable `Run Under One Process` in the Editor Preferences otherwise the `Derived Attributes` will not update when their independent `Attributes` update on clients other than the first.

In this example, we have an `Infinite` `GameplayEffect` that derives the value of `TestAttrA` from the `Attributes`, `TestAttrB` and `TestAttrC`, in the formula `TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`. `TestAttrA` recalculates its value automatically whenever any of the `Attributes` update their values.

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Set Definition
The `AttributeSet` defines, holds, and manages changes to `Attributes`. Developers should subclass from [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html). Creating an `AttributeSet` in an `OwnerActor's` constructor automatically registers it with its `ASC`. **This must be done in C++**.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Set Design
An `ASC` may have one or many `AttributeSets`. AttributeSets have negligible memory overhead so how many `AttributeSets` to use is an organizational decision left up to the developer.

It is acceptable to have one large monolithic `AttributeSet` shared by every `Actor` in your game and only use attributes if needed while ignoring unused attributes.

Alternatively, you may choose to have more than one `AttributeSet` representing groupings of `Attributes` that you selectively add to your `Actors` as needed. For example, you could have an `AttributeSet` for health related `Attributes`, an `AttributeSet` for mana related `Attributes`, and so on. In a MOBA game, heroes might need mana but minions might not. Therefore the heroes would get the mana `AttributeSet` and minions would not.

Additionally, `AttributeSets` can be subclassed as another means of selectively choosing which `Attributes` an `Actor` has. `Attributes` are internally referred to as `AttributeSetClassName.AttributeName`. When you subclass an `AttributeSet`, all of the `Attributes` from the parent class will still have the parent class's name as the prefix.

While you can have more than one `AttributeSet`, you should not have more than one `AttributeSet` of the same class on an `ASC`. If you have more than one `AttributeSet` from the same class, it won't know which `AttributeSet` to use and will just pick one.

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 Subcomponents with Individual Attributes
In the scenario where you have multiple damageable components on a `Pawn` like individually damageable armor pieces, I recommend that if you know the maximum number of damageable components that a `Pawn` could have to make that many health `Attributes` on one `AttributeSet` - DamageableCompHealth0, DamageableCompHealth1, etc. to represent logical 'slots' for those damageable components. In your damageable component class instance, assign the slot number `Attribute` that can be read by `GameplayAbilities` or [`Executions`](#concepts-ge-ec) to know which `Attribute` to apply damage to. `Pawns` that have less than the maximum number or zero of damageable components are fine. Just because a `AttributeSet` has an `Attribute`, doesn't mean that you have to use it. Unused `Attributes` take up trivial amount of memory.

If your subcomponents need many `Attributes` each, there's potentially an unbounded number of subcomponents, the subcomponents can detach and be used by other players (e.g. weapons), or for any other reason this approach doesn't work for you, I'd recommend switching away from `Attributes` and instead store plain old floats on the components. See [Item Attributes](#concepts-as-design-itemattributes).

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 Adding and Removing AttributeSets at Runtime
`AttributeSets` can be added and removed from an `ASC` at runtime; however, removing `AttributeSets` can be dangerous. For example, if an `AttributeSet` is removed on a client before the server and an `Attribute` value change is replicated to client, the `Attribute` won't find its `AttributeSet` and crash the game.

On weapon add to inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

On weapon remove from inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 Item Attributes (Weapon Ammo)
There's a few ways to implement equippable items with `Attributes` (weapon ammo, armor durability, etc). All of these approaches store values directly on the item. This is necessary for items that can be equipped by more than one player over its lifetime.

> 1. Use plain floats on the item (**Recommended**)
> 1. Separate `AttributeSet` on the item
> 1. Separate `ASC` on the item

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 Plain Floats on the Item
Instead of `Attributes`, store plain float values on the item class instance. Fortnite and [GASShooter](https://github.com/tranek/GASShooter) handle gun ammo this way. For a gun, store the max clip size, current ammo in clip, reserve ammo, etc directly as replicated floats (`COND_OwnerOnly`) on the gun instance. If weapons share reserve ammo, you would move the reserve ammo onto the character as an `Attribute` in a shared ammo `AttributeSet` (reload abilities can use a `Cost GE` to pull from reserve ammo into the gun's float clip ammo). Since you're not using `Attributes` for current clip ammo, you will need to override some functions in `UGameplayAbility` to check and apply cost against the floats on the gun. Making the gun the `SourceObject` in the [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) when granting the ability means you'll have access to the gun that granted the ability inside the ability.

To prevent the gun from replicating back the ammo amount and clobbering the local ammo amount during automatic fire, disable replication while the player has a `IsFiring` `GameplayTag` in `PreReplication()`. You're essentially doing your own local prediction here.

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

Benefits:
1. Avoids limitations of using `AttributeSets` (see below)

Limitations:
1. Can not use existing `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Requires work to override key functions on `UGameplayAbility` to check and apply ammo costs against the gun's floats

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 `AttributeSet` on the Item
Using a separate `AttributeSet` on the item that gets [added to the player's `ASC` on adding it to the player's inventory](#concepts-as-design-addremoveruntime) can work, but it has some major limitations. I had this working in early versions of [GASShooter](https://github.com/tranek/GASShooter) for the weapon ammo. The weapon stores its `Attributes` such as max clip size, current ammo in clip, reserve ammo, etc in an `AttributeSet` that lives on the weapon class. If weapons share reserve ammo, you would move the reserve ammo onto the character in a shared ammo `AttributeSet`. When a weapon is added to the player's inventory on the server, the weapon would add its `AttributeSet` to the player's `ASC::SpawnedAttributes`. The server would then replicate this down to the client. If the weapon is removed from the inventory, it would remove its `AttributeSet` from the `ASC::SpawnedAttributes`.

When the `AttributeSet` lives on something other than the `OwnerActor` (say a weapon), you'll initially get some compilation errors in the `AttributeSet`. The fix is to construct the `AttributeSet` in `BeginPlay()` instead of in the constructor and to implement `IAbilitySystemInterface` (set the pointer to the `ASC` when you add the weapon to the player inventory) on the weapon.

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

You can see it in practice by checking out this [older version of GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735).

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Simple to setup for a very small set of items

Limitations:
1. You have to make a new `AttributeSet` class for every weapon type. `ASCs` can only functionally have one `AttributeSet` instance of a class since changes to an `Attribute` look for the first instance of their `AttributeSet` class in the `ASCs` `SpawnedAttributes` array. Additional instances of the same `AttributeSet` class are ignored.
1. You can only have one of each type of weapon in the player's inventory due to previous reason of one `AttributeSet` instance per `AttributeSet` class.
1. Removing an `AttributeSet` is dangerous. In GASShooter if the player killed himself from a rocket, the player would immediately remove the rocket launcher from his inventory (including its `AttributeSet` from the `ASC`). When the server replicated that the rocket launcher's ammo `Attribute` changed, the `AttributeSet` no longer existed on the client's `ASC` and the game crashed.

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` on the Item
Putting a whole `AbilitySystemComponent` on each item is an extreme approach. I have not personally done this nor have I seen it in the wild. It would take a lot of engineering to make it work.

> Is it viable to have several AbilitySystemComponents which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Owner set to PlayerState)?
> 
> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out -HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE -which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
> 
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.

*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Can reuse `AttributeSet` classes (one on each weapon's ASC)

Limitations:
1. Unknown engineering cost
1. Is it even possible?

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Defining Attributes
**`Attributes` can only be defined in C++** in the `AttributeSet's` header file. It is recommended to add this block of macros to the top of every `AttributeSet` header file. It will automatically generate getter and setter functions for your `Attributes`.

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

A replicated health attribute would be defined like this:

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

Also define the `OnRep` function in the header:
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

The .cpp file for the `AttributeSet` should fill in the `OnRep` function with the `GAMEPLAYATTRIBUTE_REPNOTIFY` macro used by the prediction system:
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

Finally, the `Attribute` needs to be added to `GetLifetimeReplicatedProps`:
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` tells the `OnRep` function to trigger if the local value is already equal to the value being repped down from the Server (due to prediction). By default it won't trigger the `OnRep` function if the local value is the same as the value being repped down from the Server.

If the `Attribute` is not replicated like a `Meta Attribute`, then the `OnRep` and `GetLifetimeReplicatedProps` steps can be skipped.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Initializing Attributes
There are multiple ways to initialize `Attributes` (set their `BaseValue` and consequently their `CurrentValue` to some initial value). Epic recommends using an instant `GameplayEffect`. This is the method used in the Sample Project too.

See `GE_HeroAttributes` Blueprint in the Sample Project for how to make an instant `GameplayEffect` to initialize `Attributes`. Application of this `GameplayEffect` happens in C++.

If you used the `ATTRIBUTE_ACCESSORS` macro when you defined your `Attributes`, an initialization function will automatically be generated on the `AttributeSet` for each `Attribute` that you can call at your leisure in C++.

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

See `AttributeSet.h` for more ways to initialize `Attributes`.

**Note:** Prior to 4.24, `FAttributeSetInitterDiscreteLevels` did not work with `FGameplayAttributeData`. It was created when `Attributes` were raw floats and will complain about `FGameplayAttributeData` not being `Plain Old Data` (`POD`). This is fixed in 4.24 https://issues.unrealengine.com/issue/UE-76557.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` is one of the main functions in the `AttributeSet` to respond to changes to an `Attribute's` `CurrentValue` before the change happens. It is the ideal place to clamp incoming changes to `CurrentValue` via the reference parameter `NewValue`.

For example to clamp movespeed modifiers the Sample Project does it like so:
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
The `GetMoveSpeedAttribute()` function is created by the macro block that we added to the `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes)).

This is triggered from any changes to `Attributes`, whether using `Attribute` setters (defined by the macro block in `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes))) or using [`GameplayEffects`](#concepts-ge).

**Note:** Any clamping that happens here does not permanently change the modifier on the `ASC`. It only changes the value returned from querying the modifier. This means anything that recalculates the `CurrentValue` from all of the modifiers like [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) and [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) need to implement clamping again.

**Note:** Epic's comments for `PreAttributeChange()` say not to use it for gameplay events and instead use it mainly for clamping. The recommended place for gameplay events on `Attribute` change is `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` ([Responding to Attribute Changes](#concepts-a-changes)).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` only triggers after changes to the `BaseValue` of an `Attribute` from an instant [`GameplayEffect`](#concepts-ge). This is a valid place to do more `Attribute` manipulation when they change from a `GameplayEffect`.

For example, in the Sample Project we subtract the final damage `Meta Attribute` from the health `Attribute` here. If there was a shield `Attribute`, we would subtract the damage from it first before subtracting the remainder from health. The Sample Project also uses this location to apply hit react animations, show floating Damage Numbers, and assign experience and gold bounties to the killer. By design, the damage `Meta Attribute` will always come through an instant `GameplayEffect` and never the `Attribute` setter.

Other `Attributes` that will only have their `BaseValue` changed from instant `GameplayEffects` like mana and stamina can also be clamped to their maximum value counterpart `Attributes` here.

**Note:** When `PostGameplayEffectExecute()` is called, changes to the `Attribute` have already happened, but they have not replicated back to clients yet so clamping values here will not cause two network updates to clients. Clients will only receive the update after clamping.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` triggers when an `Aggregator` is created for an `Attribute` in this set. It allows custom setup of [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html). `AggregatorEvaluateMetaData` is used by the `Aggregator` in evaluating the `CurrentValue` of an `Attribute` based on all the [`Modifiers`](#concepts-ge-mods) applied to it. By default, `AggregatorEvaluateMetaData` is only used by the `Aggregator` to determine which `Modifiers` qualify with the example of `MostNegativeMod_AllPositiveMods` which allows all positive `Modifiers` but restricts negative `Modifiers` to only the most negative one. This was used by Paragon to only allow the most negative move speed slow effect to apply to a player regardless of how many slow effects where on them at any one time while applying all positive move speed buffs. `Modifiers` that don't qualify still exist on the `ASC`, they just aren't aggregated into the final `CurrentValue`. They can potentially qualify later once conditions change, like in the case if the most negative `Modifier` expires, the next most negative `Modifier` (if one exists) then qualifies.

To use AggregatorEvaluateMetaData in the example of only allowing the most negative `Modifier` and all positive `Modifiers`:

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

Your custom `AggregatorEvaluateMetaData` for qualifiers should be added to `FAggregatorEvaluateMetaDataLibrary` as static variables.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 Gameplay Effect Definition
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`) are the vessels through which abilities change [`Attributes`](#concepts-a) and [`GameplayTags`](#concepts-gt) on themselves and others. They can cause immediate `Attribute` changes like damage or healing or apply long term status buff/debuffs like a movespeed boost or stunning. The `UGameplayEffect` class is a meant to be a **data-only** class that defines a single gameplay effect. No additional logic should be added to `GameplayEffects`. Typically designers will create many Blueprint child classes of `UGameplayEffect`.

`GameplayEffects` change `Attributes` through [`Modifiers`](#concepts-ge-mods) and [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec).

`GameplayEffects` have three types of duration: `Instant`, `Duration`, and `Infinite`.

Additionally, `GameplayEffects` can add/execute [`GameplayCues`](#concepts-gc). An `Instant` `GameplayEffect` will call `Execute` on the `GameplayCue` `GameplayTags` whereas a `Duration` or `Infinite` `GameplayEffect` will call `Add` and `Remove` on the `GameplayCue` `GameplayTags`.

| Duration Type | GameplayCue Event | When to use                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | For immediate permanent changes to `Attribute's` `BaseValue`. `GameplayTags` will not be applied, not even for a frame.                                                                                                                    |
| `Duration`    | Add & Remove      | For temporary changes to `Attribute's` `CurrentValue` and to apply `GameplayTags` that will be removed when the `GameplayEffect` expires or is manually removed. The duration is specified in the `UGameplayEffect` class/Blueprint.       |
| `Infinite`    | Add & Remove      | For temporary changes to `Attribute's` `CurrentValue` and to apply `GameplayTags` that will be removed when the `GameplayEffect` is removed. These will never expire on their own and must be manually removed by an ability or the `ASC`. |

`Duration` and `Infinite` `GameplayEffects` have the option of applying `Periodic Effects` that apply its `Modifiers` and `Executions` every `X` seconds as defined by its `Period`. `Periodic Effects` are treated as `Instant` `GameplayEffects` when it comes to changing the `Attribute's` `BaseValue` and `Executing` `GameplayCues`. These are useful for damage over time (DOT) type effects. **Note:** `Periodic Effects` cannot be [predicted](#concepts-p).

`Duration` and `Infinite` `GameplayEffects` can be temporarily turned off and on after application if their `Ongoing Tag Requirements` are not met/met ([Gameplay Effect Tags](#concepts-ge-tags)). Turning off a `GameplayEffect` removes the effects of its `Modifiers` and applied `GameplayTags` but does not remove the `GameplayEffect`. Turning the `GameplayEffect` back on reapplies its `Modifiers` and `GameplayTags`.

If you need to manually recalculate the `Modifiers` of a `Duration` or `Infinite` `GameplayEffect` (say you have an `MMC` that uses data that doesn't come from `Attributes`), you can call `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)` with the same level that it already has using `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`. `Modifiers` that are based on backing `Attributes` automatically update when those backing `Attributes` update. The key functions of `SetActiveGameplayEffectLevel()` to update the `Modifiers` are:

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects` are not typically instantiated. When an ability or `ASC` wants to apply a `GameplayEffect`, it creates a [`GameplayEffectSpec`](#concepts-ge-spec) from the `GameplayEffect's` `ClassDefaultObject`. Successfully applied `GameplayEffectSpecs` are then added to a new struct called `FActiveGameplayEffect` which is what the `ASC` keeps track of in a special container struct called `ActiveGameplayEffects`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 Applying Gameplay Effects
`GameplayEffects` can be applied in many ways from functions on [`GameplayAbilities`](#concepts-ga) and functions on the `ASC` and usually take the form of `ApplyGameplayEffectTo`. The different functions are essentially convenience functions that will eventually call `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()` on the `Target`.

To apply `GameplayEffects` outside of a `GameplayAbility` for example from a projectile, you need to get the `Target's` `ASC` and use one of its functions to `ApplyGameplayEffectToSelf`.

You can listen for when any `Duration` or `Infinite` `GameplayEffects` are applied to an `ASC` by binding to its delegate:
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
The callback function:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

The server will always call this function regardless of replication mode. The autonomous proxy will only call this for replicated `GameplayEffects` in `Full` and `Mixed` replication modes. Simulated proxies will only call this in `Full` [replication mode](#concepts-asc-rm).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 Removing Gameplay Effects
`GameplayEffects` can be removed in many ways from functions on [`GameplayAbilities`](#concepts-ga) and functions on the `ASC` and usually take the form of `RemoveActiveGameplayEffect`. The different functions are essentially convenience functions that will eventually call `FActiveGameplayEffectsContainer::RemoveActiveEffects()` on the `Target`.

To remove `GameplayEffects` outside of a `GameplayAbility`, you need to get the `Target's` `ASC` and use one of its functions to `RemoveActiveGameplayEffect`.

You can listen for when any `Duration` or `Infinite` `GameplayEffects` are removed from an `ASC` by binding to its delegate:
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
The callback function:
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

The server will always call this function regardless of replication mode. The autonomous proxy will only call this for replicated `GameplayEffects` in `Full` and `Mixed` replication modes. Simulated proxies will only call this in `Full` [replication mode](#concepts-asc-rm).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect Modifiers
`Modifiers` change an `Attribute` and are the only way to [predictively](#concepts-p) change an `Attribute`. A `GameplayEffect` can have zero or many `Modifiers`. Each `Modifier` is responsible for changing only one `Attribute` via a specified operation.

| Operation  | Description                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | Adds the result to the `Modifier's` specified `Attribute`. Use a negative value for subtraction.                    |
| `Multiply` | Multiplies the result to the `Modifier's` specified `Attribute`.                                                    |
| `Divide`   | Divides the result against the `Modifier's` specified `Attribute`.                                                  |
| `Override` | Overrides the `Modifier's` specified `Attribute` with the result.                                                   |

The `CurrentValue` of an `Attribute` is the aggregate result of all of its `Modifiers` added to its `BaseValue`. The formula for how `Modifiers` are aggregated is defined as follows in `FAggregatorModChannel::EvaluateWithBase` in `GameplayEffectAggregator.cpp`:
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

Any `Override` `Modifiers` will override the final value with the last applied `Modifier` taking precedence.

**Note:** For percentage based changes, make sure to use the `Multiply` operation so that it happens after addition.

**Note:** [Prediction](#concepts-p) has trouble with percentage changes.

There are four types of `Modifiers`: Scalable Float, Attribute Based, Custom Calculation Class, and Set By Caller. They all generate some float value that is then used to change the specified `Attribute` of the `Modifier` based on its operation.

| `Modifier` Type            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` are a structure that can point to a Data Table that has the variables as rows and levels as columns. The Scalable Floats will automatically read the value of the specified table row at the ability's current level (or different level if overriden on the [`GameplayEffectSpec`](#concepts-ge-spec)). This value can further be manipulated by a coefficient. If no Data Table/Row is specified, it treats the value as a 1 so the coefficient can be used to hard code in a single value at all levels. ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Attribute Based`          | `Attribute Based` `Modifiers` take the `CurrentValue` or `BaseValue` of a backing `Attribute` on the `Source` (who created the `GameplayEffectSpec`) or `Target` (who received the `GameplayEffectSpec`) and further modifies it with a coefficient and pre and post coefficient additions. `Snapshotting` means the backing `Attribute` is captured when the `GameplayEffectSpec` is created whereas no snapshotting means the `Attribute` is captured when the `GameplayEffectSpec` is applied.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Custom Calculation Class` | `Custom Calculation Class` provides the most flexibility for complex `Modifiers`. This `Modifier` takes a [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) class and can further manipulate the resulting float value with a coefficient and pre and post coefficient additions.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Set By Caller`            | `SetByCaller` `Modifiers` are values that are set outside of the `GameplayEffect` at runtime by the ability or whoever made the `GameplayEffectSpec` on the `GameplayEffectSpec`. For example, you would use a `SetByCaller` if you want to set the damage to be based on how long the player held down a button to charge the ability. `SetByCallers` are essentially `TMap<FGameplayTag, float>` that live on the `GameplayEffectSpec`. The `Modifier` is just telling the `Aggregator` to look for a `SetByCaller` value associated with the supplied `GameplayTag`. The `SetByCallers` used by `Modifiers` can only use the `GameplayTag` version of the concept. The `FName` version is disabled here. If the `Modifier` is set to `SetByCaller` but a `SetByCaller` with the correct `GameplayTag` does not exist on the `GameplayEffectSpec`, the game will throw a runtime error and return a value of 0. This might cause issues in the case of a `Divide` operation. See [`SetByCallers`](#concepts-ge-spec-setbycaller) for more information on how to use `SetByCallers`. |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 Multiply and Divide Modifiers
By default, all `Multiply` and `Divide` `Modifiers` are added together before multiplying or dividing them into the `Attribute`'s `BaseValue`.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*from `GameplayEffectAggregator.cpp`*

Both `Multiply` and `Divide` `Modifiers` have a `Bias` value of `1` in this formula (`Addition` has a `Bias` of `0`). So it would look something like:

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

This formula leads to some unexpected results. Firstly, this formula adds all the modifiers together before multiplying or dividing them into the `BaseValue`. Most people would expect it to multiply or divide them together. For example, if you have two `Multiply` modifiers of `1.5`, most people would expect the `BaseValue` to be multiplied by `1.5 x 1.5 = 2.25`. Instead, this adds the `1.5`s together to multiply the `BaseValue` by `2` (`50% increase + another 50% increase = 100% increase`). This was for the example from `GameplayPrediction.h` of a `10%` speed buff on `500` base speed would be `550`. Add another `10%` speed buff and it will be `600`.

Secondly, this formula has some undocumented rules about what values can be used as it was designed with Paragon in mind.

Rules for `Multiply` and `Divide` multiplication addition formula:
* `(No more than one value < 1) AND (Any number of values [1, 2))`
* `OR (One value >= 2)`

The `Bias` in the formula basically subtracts out the integer digit of numbers in the range `[1, 2)`. The first `Modifier`'s `Bias` subtracts out from the starting `Sum` value (set to the `Bias` before the loop) which is why any value by itself works and why one value `< 1` will work with the numbers in the range `[1, 2)`.

Some examples with `Multiply`:  
Multipliers: `0.5`  
`1 + (0.5 - 1) = 0.5`, correct

Multipliers: `0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0`, incorrect expected `1`? Multiple values less than `1` don't make sense for adding multipliers. Paragon was designed to only use the [greatest negative value for `Multiply` `Modifiers`](#cae-nonstackingge) so there would only ever be at most one value less than `1` multiplying into the `BaseValue`.

Multipliers: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`, correct

Multipliers: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`, incorrect expected `10`. Will always be the `sum of the Modifiers - number of Modifiers + 1`.

Many games will want their `Multiply` and `Divide` `Modifiers` to multiply and divide together before applying to the `BaseValue`. To achieve this, you will need to **change the engine code** for `FAggregatorModChannel::EvaluateWithBase()`.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 Gameplay Tags on Modifiers

`SourceTags` and `TargetTags` can be set for each [Modifier](#concepts-ge-mods). They work the same like the [`Application Tag requirements`](#concepts-ge-tags) of a `GameplayEffect`. So the tags are considered only when the effect is applied. I.e. when having a periodic, infinite effect, they are only taken into consideration on the first application of the effect but *not* on each periodic execution.

`Attribute Based` Modifiers can also set `SourceTagFilter` and `TargetTagFilter`. When determining the magnitude of the attribute which is the source of the `Attribute Based` Modifier, these filters are used to exclude certain Modifiers to that attribute. Modifiers which source or target didn't have all of the tags of the filter are excluded.

This means in detail: The tags of the source ASC and the target ASC are captured by `GameplayEffects`. The source ASC tags are captured, when the `GameplayEffectSpec` is created, the target ASC tags are captured on execution of the effect. When determining, if a Modifier of an infinite or duration effect "qualifies" to be applied (i.e. its Aggregator qualifies) and those filters are set, the captured tags are compared against the filters.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 存储Gameplay Effects
默认情况下，`GameplayEffects` 将申请新的`GameplayEffectSpec`实例，这个新实例不知道或者不关心之前已经存在的`GameplayEffectSpec`。`GameplayEffects`可以设置为存储的，以替代每次都创建添加新的实例，当前存在的`GameplayEffectSpec`堆栈计数被更改。存储模式只对`Duration` 和`Infinite`的`GameplayEffects`生效。

两种存储模式: 按来源方归类与按目标方归类。

| 存储类型       | 描述                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 按来源 | 在目标上，独立的存储每个来源`ASC`的堆栈实例。每个来源可以申请若干数量的堆栈。           |
| 按目标 | 不管来源是什么，目标上只有一个堆栈实例。再共享堆栈超限前每个来源可以申请一个堆栈|

堆栈有过期、持续时间刷新与周期重置策略。他们在鼠标滑过`GameplayEffect`蓝图上时会有帮助提示。

示例工程包含一个自定义蓝图节点以监听`GameplayEffect`堆栈的变化。UI使用它来更新玩家的被动护甲值。这个 This `AsyncTask`将持续存在直到手动调用`EndTask()`,即我们在UI的 `Destruct`中所做的。详见`AsyncTaskEffectStackChanged.h/cpp`.

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 授予Abilities
`GameplayEffects`可以赋予一个新的[`GameplayAbilities`](#concepts-ga)给`ASCs`. 只有`Duration`和 `Infinite`的`GameplayEffects` 可以授予Abilities.

常见的使用场景是当你想强制其他玩家进行诸如击退、拉近等移动时。你可以为他们申请一个`GameplayEffect`并以自动触发的方式授予他们(详见[Passive Abilities](#concepts-ga-activating-passive) 来了解当被授予一个Ability时如何自动触发)来让其进行设计中的行为.

设计师可以选择一个`GameplayEffect`可以授予什么Abilities, 以授予什么级别，什么[按键绑定](#concepts-ga-input)以及移除策略。

| 移除策略             | 描述                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 立即取消 | 当授予Ability的`GameplayEffect`从目标移除时，被授予的Ability立即取消并移除。                              |
| 结束时移除      | 被授予的Ability自然结束时从目标移除。                                                                                                  |
| 什么都不做      | 即使授予Ability的`GameplayEffect`被移除， Ability不因执行授予操作的`GameplayEffect`的移除而移除。目标永久持有Ability直到被手动移除。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 GameplayEffect Tags
`GameplayEffects`携带多个[`GameplayTagContainers`](#concepts-gt). 设计者将为每个类别编辑`Added`、 `Removed` `GameplayTagContainers`，结果将显示在编译时的`Combined` `GameplayTagContainer`中。'Added`标签是`GameplayEffect`添加的新标签，这是它的父辈以前没有的。' Removed '标签是父类有但子类没有的标签。

Designers will edit the `Added` and `Removed` `GameplayTagContainers` for each category and the result will show up in the `Combined` `GameplayTagContainer` on compilation. `Added` tags are new tags that this `GameplayEffect` adds that its parents did not previously have. `Removed` tags are tags that parent classes have but this subclass does not have.

| 标签                         | 描述                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GameplayEffect Asset Tags        | `GameplayEffect`拥有的Tags. 他们本身没有任何功能，只是为了描述`GameplayEffect`.                                                                                                                                                                                          
| Granted Tags                     | 存在于`GameplayEffect`上的Tags，同时此Tags也给与了`GameplayEffect`所应用于的`ASC`。 这些Tags随`GameplayEffect`从`ASC`上移除而移除。只在`Duration`和`Infinite`的`GameplayEffects`上生效。                                                                             				
| Ongoing Tag Requirements          | 一旦应用，这些标签将决定`GameplayEffect`是开启还是关闭。一个 `GameplayEffect`可以在应用中被关闭。如果一个`GameplayEffect`因未满足要求的存续Tag而被关闭，但随后要求的存续Tag被满足了，那这个`GameplayEffect`将被开启，并重新启用其修改器。只在`Duration`和`Infinite`的`GameplayEffects`上生效。               
| Application Tag Requirements      | 目标上的Tags决定了`GameplayEffect`是否能应用于目标。如果需求未满足，则`GameplayEffect`无法生效。                                                                                                                                                                                                       |
| Remove Gameplay Effects with Tags | 当此`GameplayEffect`被成功应用时，将把其他的`GameplayEffect`（那些在`AssetTags`或`GrantedTags`中有相同Tags的）移除。                                                                                                                                                                               |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 豁免
`GameplayEffects`可以授予豁免，以有效阻止其他`GameplayEffects`的应用，基于[`GameplayTags`](#concepts-gt)。虽然豁免效果可以通过其他方式实现，例如`Application Tag Requirements`，但使用豁免方式可以提供一个当`GameplayEffects`因豁免而被阻止时调用的委托`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`。

`GrantedApplicationImmunityTags`检查源`ASC`（包含来自源Ability的`AbilityTags`）是否拥有任何指定Tags。这是一种基于特定角色或来源的Tags来另其可以豁免`GameplayEffects`的方式。

`GrantedApplicationImmunityQuery`检查传入的`GameplayEffectSpec`，根据查询的匹配结果来阻止或通过这些应用。

这些查询在`GameplayEffect`蓝图中有很有帮助的鼠标悬停提示。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec

[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) 可以被认为是`GameplayEffects`的实例。他们持有一个他们所代表的`GameplayEffect`类的引用，他是在什么级别被创建的，谁创建的。与`GameplayEffects`不同，它们可以在应用程序运行之前在运行时自由创建和修改，而`GameplayEffects`应该在运行之前由设计者创建。当应用`GameplayEffect`时，一个`GameplayEffectSpec`将从`GameplayEffect`创建，而这也是“应用到目标”所实际做的事。

`GameplayEffectSpecs`是通过`GameplayEffects`使用`UAbilitySystemComponent::MakeOutgoingSpec()`这个`BlueprintCallable`函数创建的。`GameplayEffectSpecs`不需要立即应用。通常情况下会把`GameplayEffectSpec`传递给一个Ability创建的投射物（子弹），这个投射物命中目标后应用`GameplayEffectSpec`。当`GameplayEffectSpecs`成功应用后，将会返回一个被称为`FActiveGameplayEffect`的结构体。

 `GameplayEffectSpec`重点:
* `GameplayEffectSpecs`由`GameplayEffects`创建。
* `GameplayEffectSpec`的等级。 通常与创建`GameplayEffectSpec`的Ability相同，但可以不同。
* `GameplayEffectSpec`的持续时间。 默认为`GameplayEffect`的持续时间，但可以不同。
* `GameplayEffectSpec`的周期。默认为`GameplayEffect`的周期，但可以不同。
* `GameplayEffectSpec`的堆栈计数。 堆栈限制在`GameplayEffect`上。
* [`GameplayEffectContextHandle`](#concepts-ge-context)告诉我们是谁创建了`GameplayEffectSpec`.
* `Attributes`在`GameplayEffectSpec`创建的瞬间被捕获。
* 除了`GameplayEffect`授予`GameplayTags`之外，`GameplayEffectSpec`还授予目标`DynamicGrantedTags`。
* 除了`GameplayEffect`拥有的`AssetTags`之外，`GameplayEffectSpec`还拥有`DynamicAssetTags`。
* `SetByCaller` `TMaps`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers`允许`GameplayEffectSpec`携带与`GameplayTag`或`FName`相关的浮点值。他们分别存储在`GameplayEffectSpec`的`TMaps`: `TMap<FGameplayTag, float>`、`TMap<FName, float>`中。他们可以被用作`GameplayEffect`的`Modifiers`，或者通用的float值传递方法。通过`SetByCallers`将Ability内部产生的数据传递给[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)或[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)非常常见。


| `SetByCaller` 使用方式 | 注释                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | 必须提前在`GameplayEffect`中定义. 只能用于`GameplayTag`。如果在`GameplayEffect`定义但在`GameplayEffectSpec`中没有相应的Tag、Float键值对，那`GameplayEffectSpec`应用时将会出现一个运行时错误并return 0。这是一个`Divide`操作时的潜在问题。 详见[`Modifiers`](#concepts-ge-mods)。 |
| 其他         | 不需要再任何地方提前定义。读取`GameplayEffectSpec`中不存在的`SetByCaller`可以返回开发者定义的默认值并带有可选警告。                                                                                                                                                                                                                 |

使用你想要的蓝图节点(`GameplayTag`或`FName`)在蓝图中注册`SetByCaller`值：

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

要在蓝图中读取`SetByCaller`值，需要在写自定义蓝图节点。
要在C++中注册`SetByCaller`值，要使用对应的函数(`GameplayTag`或`FName`)：

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

要在C++中读取`SetByCaller`值， 要使用对应的函数(`GameplayTag`或`FName`)：

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

相比于`FName`，更推荐使用`GameplayTag`。这样可以防止蓝图中的拼写错误，当`GameplayEffectSpec` `replicate`时，`GameplayTags`通过网络发送比`FNames`更有效，因为`TMaps`也复制了。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context

 [`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html)结构保存了`GameplayEffectSpec`的发起者和[`TargetData`](#concepts-targeting-data)的信息。这也是一个很好的结构，子类可以在[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)、 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)、 [`AttributeSets`](#concepts-as)和[`GameplayCues`](#concepts-gc)间传递任意数据。


子类化`GameplayEffectContext`:

1. 子类 `FGameplayEffectContext`
1. 重载 `FGameplayEffectContext::GetScriptStruct()`
1. 重载 `FGameplayEffectContext::Duplicate()`
1. 重载 `FGameplayEffectContext::NetSerialize()` if your new data needs to be replicated
1. 像父类`FGameplayEffectContext`那样在子类实现`TStructOpsTypeTraits`
1. 在你的[`AbilitySystemGlobals`](#concepts-asg)中重载`AllocGameplayEffectContext()`以返回一个子类的新对象，

[GASShooter](https://github.com/tranek/GASShooter)使用子类`GameplayEffectContext`来添加可以被`GameplayCues`访问的`TargetData`，这是为散弹枪这种可以命中复数敌人的情况专门设计的、

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation

[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc`或`MMC`)是`GameplayEffects`中用作[`Modifiers`](#concepts-ge-mods)的一个功能强大的类。他的功能类似于[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)但没有那么强大，但重要的是他是[predicted](#concepts-p)的。他的唯一目的是从`CalculateBaseMagnitude_Implementation()`返回一个浮点值。你可以在蓝图或C++中将其子类化并重写此函数。

`MMCs`可用于任何持续时间的`GameplayEffects`，`Instant`, `Duration`, `Infinite`, `Periodic`都可以。

`MMC`的优势在于它们能够获取`GameplayEffect`的`Source`或`Target`上任何数量的`Attributes`的值，并完全访问`GameplayEffectSpec`以读取`GameplayTags`和`SetByCallers`。`Attributes`快不快照（snapshotted）都行。当创建`GameplayEffectSpec`时捕获快照的`Attributes`，而当应用`GameplayEffectSpec`时捕获非快照的`Attributes`，并在`Infinite`或`Duration`的`GameplayEffects`的`Attributes`改变时自动更新。捕获`Attributes`会从`ASC`上现有的mod中重新计算它们的`CurrentValue`。这个重新计算**不会**在`AbilitySet`中运行[`PreAttributeChange()`](#concepts-as-preattributechange)，所以任何`Clamping`都必须在这再次进行。

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |当`Attribute`改为`Infinite`或`Duration` `GE`时自动更新 |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |


`MMC`中产生的浮点数可以在`GameplayEffect`的`Modifier`中通过系数和前后系数加法进行进一步修改。

例如`MMC`捕获`Target`的魔法`Attribute`，使其从毒药效果中减少，减少的数量取决于`Target`有多少法力值和`Target`可能有的`Tag`:

```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

如果你没有在`MMC`的构造函数中将`FGameplayEffectAttributeCaptureDefinition`添加到`RelevantAttributesToCapture`并试图捕获`Attributes`，你将会在捕获时得到一个关于丢失`Spec`的错误。如果你不需要捕获`Attributes`，那么你就不需要添加任何东西到`RelevantAttributesToCapture`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 GameplayEffect Execution Calculation

[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation`, `Execution`或`ExecCalc` (您将经常在插件的源代码中看到这个术语))是`GameplayEffects`改变`ASC`最强大的方式。 就像[`ModifierMagnitudeCalculations`](#concepts-ge-mmc), 这些可以捕获`Attributes`，并可选地对它们进行快照。 与`MMC`不同的是，它们可以改变多个`Attribute`，并且基本上可以做任何程序员想要的事情。 这种功能和灵活性的缺点是它们不能被预测[predicted（预测）](#concepts-p)，且必须用C++实现。

`ExecutionCalculations`只能与`Instant`、`Periodic`和`GameplayEffects`一起使用。任何带有`Execute`一词的内容都指代这两种类型的`GameplayEffects`。

快照在创建`GameplayEffectSpec`时捕获`Attribute`，而非快照在应用`GameplayEffectSpec`时捕获`Attribute`。捕获`Attributes`会从`ASC`上现有的mod中重新计算它们的`CurrentValue`。此重新计算**不会**在`AbilitySet`中运行[`PreAttributeChange()`](#concepts-as-preattributechange)，所以必须在这里再次`Clamping`。


| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

为了设置`Attribute`捕获，我们遵循Epic的ActionRPG Sample Project的模式，即定义一个结构体并定义我们如何捕获`Attributes`，并在结构体的构造函数中创建一个它的副本。对于每个`ExecCalc`，你都会有一个这样的结构体。**注意:**每个结构体需要一个唯一的名称，因为它们共享相同的命名空间。使用相同名称的结构体将捕获`Attributes`时产生不正确的行为(主要是捕获错误的`Attributes`的值)。

对于`Local Predicted`、`Server Only`和`Server Initiated` [`GameplayAbilities`](#concepts-ga)，只能在服务器调用`ExecCalc`。

基于从`Source`和`Target`的许多属性中读取的复杂公式来计算伤害是`ExecCalc`最常见的例子。示例项目中有一个简单的`ExecCalc`用于计算伤害，它从`GameplayEffectSpec`的[`SetByCaller`](#concepts-ge-spec-setbycaller) 中读取伤害值，然后根据从`Target`获取的护甲`Attribute`减轻该值。详见`GDDamageExecCalculation.cpp/.h`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 发送数据到Execution Calculations

除了捕获`Attributes`之外，还有一些方法可以将数据发送到`ExecutionCalculation`。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
任何[`GameplayEffectSpec`上的`SetByCallers`](#concepts-ge-spec-setbycaller)可以直接在`ExecutionCalculation`中读取。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier

如果你想将数值硬编码到`GameplayEffect`中，你可以使用`CalculationModifier`将它们传递进来，该“`CalculationModifier`使用一个捕获的`Attributes`作为支持数据。

在这个截图示例中，我们将在捕获的伤害`Attribute`上+50。你也可以将其设置为`Override`，只接收硬编码的值。

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

 `ExecutionCalculation`在捕获`Attribute`时读取这个值。

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 Backing Data Temporary Variable Calculation Modifier

如果你想将数值硬编码到`GameplayEffect`中，你可以像在C++中那样使用`CalculationModifier`的`临时变量`或`临时聚合器`来传递它们。`临时变量`与`GameplayTag`相关联。

在这个截图示例中，我们使用`Data.Damage` `GameplayTag`给`临时变量`+50。

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

Add backing `Temporary Variables` to your `ExecutionCalculation`'s constructor:

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation`使用类似于`Attribute`捕获函数的特殊捕获函数读取这个值。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context

你可以通过一个自定义的[`GameplayEffectSpec`上的`GameplayEffectContext`](#concepts-ge-context)将数据发送给`ExecutionCalculation`。

在`ExecutionCalculation`中，你可以通过`FGameplayEffectCustomExecutionParameters`访问`EffectContext`。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你想更改`GameplayEffectSpec`或`EffectContext`上的某些东西：

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

Use caution if modifying the `GameplayEffectSpec` in the `ExecutionCalculation`. See the comment for `GetOwningSpecForPreExecuteMod()`.


在`ExecutionCalculation`中修改`GameplayEffectSpec`需谨慎。参见`GetOwningSpecForPreExecuteMod()`的注释。

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 Custom Application Requirement

[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) 类让设计者无论是在`GameplayEffect`可被应用还是简单的`GameplayEffect`检测`GameplayTag`时都有更高效的控制。这些可以通过在蓝图中重载`CanApplyGameplayEffect()`以及在C++中重载`CanApplyGameplayEffect_Implementation()`来实现。

`CARs`使用时机示例:
* `Target`需要有一定数量的`Attribute`
* `Target` 需要有一定数量的`GameplayEffect`

`CARs`支持进行更多的进阶操作，比如检测一个`GameplayEffect`实例是否已经存在于`Target`上并[改变其持续时间](#concepts-ge-duration)以替代应用一个新的实例(`CanApplyGameplayEffect()`返回false)。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost Gameplay Effect

[`GameplayAbilities`](#concepts-ga) 有一个可选的`GameplayEffect`，专门用于作为能力的费用。费用是指`Attribute`和 `ASC`激活`GameplayAbility`所需的东西。如果一个`GA`不能负担`Cost GE`，则他们不能被激活。 这个`Cost GE`应当是一个`Instant`的`GameplayEffect`，并带有一个或多个减少`Attributes`的`Modifiers`。默认情况下， `Cost GEs`意味着`预测Predicted`，建议保留此功能，也就是说不要使用`ExecutionCalculations`。`MMCs`完全可接受并鼓励复杂的费用计算。

在开始时，你很可能对每个需要成本的`GA`都有一个独立的`Cost GE`。更高级的方式是多个`GAs`复用一个`Cost GE`，并使用`GA`的特定数据(成本值在`GA`上定义)修改`GameplayEffectSpec`。**这只适用于“实例化的”能力。**

两个复用`Cost GE`的技巧:

1. **使用`MMC`.** 这是最简单的方式。创建一个从`GameplayEffectSpec`中获取的`GameplayAbility`的实例中读取费用值的[`MMC`]。

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在这个例子中，成本值是一个我添加到`GameplayAbility`子类的`FScalableFloat`。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重载`UGameplayAbility::GetCostGameplayEffect()`**。重载此方法并且[在运行中创建一个`GameplayEffect`](#concepts-ge-dynamic)，这样就可以在`GameplayAbility`读取费用值了。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect

[`GameplayAbilities`](#concepts-ga) 有一个可选的`GameplayEffect`，专门用于作为能力的冷却。 冷却是指能力被激活后多久才能再次激活。如果一个`GA`仍在冷却中，那他就不能激活。 这个`Cooldown GE`应是一个没有`Modifiers`，并且每个`GameplayAbility`各有不同的`GameplayTag`，或者`GameplayEffect`的`GrantedTags`的每个能力槽位（如果你的游戏是技能槽中的技能可换，冷却按技能槽走的）各有不同的`GameplayTag`。实际上`GA`检测的是`冷却Tag`而不是`冷却GE`。默认情况下， `Cooldown GE`意味着`预测Predicted`，建议保留此功能，也就是说不要使用`ExecutionCalculations`。 `MMCs`完全可接受并鼓励复杂的冷却计算。

在开始时，你很可能对每个需要成本的`GA`都有一个独立的`Cooldown GE`。更高级的方式是多个`GAs`复用一个`Cooldown GE`，并使用`GA`的特定数据(冷却时间与`Cooldown Tag`在`GA`上定义)修改`GameplayEffectSpec`。**这只适用于“实例化的”能力。**

两个复用`Cooldown GE`的技巧:

1. **使用[`SetByCaller`](#concepts-ge-spec-setbycaller)。** 这是最简单的方式。使用`GameplayTag`将你共享的`Cooldown GE`设置到`SetByCaller`。在你的`GameplayAbility`上, 为冷却时间定义一个 `浮点数`/`FScalableFloat`，为独立的`Cooldown Tag`定义一个`FGameplayTagContainer`，以及一个临时的`FGameplayTagContainer`，我们将使用它作为`Cooldown Tag`和`Cooldown GE`的`Tags`的返回指针。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```
接着重载`UGameplayAbility::GetCooldownTags()`来返回`Cooldown Tags`和任何现有的`Cooldown GE`的`Tags`的合并后的`Tags`。

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重载`UGameplayAbility::ApplyCooldown()`以注入我们的`Cooldown Tags`，并将`SetByCaller`添加到冷却`GameplayEffectSpec`中。

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在此图中，冷却的持续时间`Modifier`通过一个值为`Data.Cooldown`的`Data Tag`被设置为`SetByCaller`。`Data.Cooldown`就是上面代码中的`OurSetByCallerTag`。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用[`MMC`](#concepts-ge-mmc)。** 除了将`SetByCaller`设置为`Cooldown GE`和`ApplyCooldown`的持续时间外，其他设置与上面相同。此处要将持续时间设置为一个`自定义计算类`并指向我们将要创建的新`MMC`。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

接着重载`UGameplayAbility::GetCooldownTags()`来返回`Cooldown Tags`和任何现有的`Cooldown GE`的`Tags`的合并后的`Tags`。

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重载`UGameplayAbility::ApplyCooldown()`以注入我们的`Cooldown Tags`，并将`SetByCaller`添加到冷却`GameplayEffectSpec`中。

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 获取Cooldown Gameplay Effect的剩余时间
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意：** 要在客户端上查到剩余的冷去时间，需要他们可以接受到`复制`的`GameplayEffects`。这取决于他们的`ASC's` [replication mode](#concepts-asc-rm)。

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 监听冷却的起始和结束

为了监听冷却时间何时开始，你可以通过绑定`AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`来响应`Cooldown GE`的应用，或者通过绑定`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`来响应`Cooldown Tag`的添加。我建议监听`Cooldown GE`何时被添加，因为你还可以访问应用它的`GameplayEffectSpec`。由此你可以确定`Cooldown GE`是本地预测的还是服务器的修正值。

要监听冷却时间何时结束，你可以通过绑定`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`来响应`Cooldown GE`何时被移除，或者通过绑定`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`来响应`Cooldown Tag`何时被移除。我建议监听`Cooldown Tag`何时被移除，因为当服务器修正的`Cooldown GE`传入时，它将删除我们本地预测的`Cooldown Tag`，从而导致`OnAnyGameplayEffectRemovedDelegate()`被触发，即使我们仍然处于冷却时间。在移除预测的`Cooldown GE`和应用服务器修正的`Cooldown GE`期间，`Cooldown Tag`不会改变。

**注意：** 要在客户端监听一个`GameplayEffect`的添加或移除需要他们可以接收复制的`GameplayEffects`。这取决于他们的`ASC's` [replication mode](#concepts-asc-rm).

示例工程包含了一个自定义蓝图节点来监听冷却时间的开始和结束。UI使用他来更新流星技能的剩余冷却时间。这个`AsyncTask`将在手动调用`EndTask()`前一直存在, 就像我们在UI的`Destruct`中做的那样。详见[`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp).


![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 Predicting Cooldowns

目前冷却时间还不能被预测。我们可以在`Cooldown GE`本地预测被应用，但`GameplayAbility`的实际冷却由服务器的冷却时间决定时启动UI的冷却计时器。根据玩家的延迟，本地预测的冷却时间可能会到期，但`GameplayAbility`仍然会在服务器上处于冷却时间，这将阻止`GameplayAbility`立即重新激活，直到服务器的冷却时间到期。

示例项目通过在本地预测的冷却开始时让流星技能的UI图标变灰，并在接到服务器修正的`Cooldown GE`时启动冷却计时器的方法来解决此问题。

这样做的一个玩法上的结果是，高延迟的玩家在短CD能力下的射击速度低于具有较低延迟的玩家，这使他们处于劣势。《堡垒之夜》避免了这一问题，因为他们的武器拥有自定义标记，而不使用冷却`GameplayEffects`。

允许真正的冷却预测功能（玩家可以在本地冷却到时但服务器仍在冷却中的情况下激活`GameplayAbility`）是Epic希望有朝一日在[GAS的未来迭代](#concepts-p-future)中实现的东西。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 更改激活中的GameplayEffect持续时间

要更改`Cooldown GE`或任何`Duration`的`GameplayEffect`的剩余时间，我们需要更改`GameplayEffectSpec`的`Duration`，更新它的`StartServerWorldTime`，更新它的`CachedStartServerWorldTime`，更新它的`StartWorldTime`，并使用`CheckDuration()`重新运行对持续时间的检查。在服务器上执行此操作并对`FActiveGameplayEffect`加上脏标记将把更改复制到客户端。

**注意：** 这确实涉及到`const_cast`，并且可能不是Epic想要改变持续时间的方式，但到目前为止它似乎工作得很好。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 运行中创建动态GameplayEffects

运行中创建动态`GameplayEffects`是一个进阶话题。你不该经常这样做。

只有`Instant`的`GameplayEffects`可以在运行时用c++从头开始创建。`Duration`和`Infinite`的`GameplayEffects`无法在运行时动态创建，因为当它们复制时，它们会寻找不存在的`GameplayEffect`类定义。为了实现这个功能，你应该创建一个原型类`GameplayEffect`，就像你通常在编辑器中做的那样。然后自定义运行中所需的`GameplayEffectSpec`实例。

在运行时创建的`Instant` `GameplayEffects`也可以从[本地预测](#concepts-p)的`GameplayAbility`中被调用。然而，目前还不清楚动态创建是否会产生副作用。

##### 举例

示例项目创建了一个例子，他会在角色在其`AttributeSet`中遭受致命一击时，将金币和经验值发送给击杀者。

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个例子展示了在本地预测的`GameplayAbility`中创建的运行时`GameplayEffect`。自担风险(参见代码中的注释)!

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 Gameplay Effect Containers

Epic的[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)实现了一个名为`FGameplayEffectContainer`的结构。它不是原生GAS的一部分，但对于容纳`GameplayEffects`和[`TargetData`](#concepts- targets -data)非常方便。它将一些工作自动化，例如从`GameplayEffects`创建`GameplayEffectSpecs`，并在其`GameplayEffectContext`中设置默认值。在`GameplayAbility`中创建一个`GameplayEffectContainer`并将其传递给生成的发射物是非常简单直接的。我选择在示例项目中不实现`FGameplayEffectContainers`，以展示你在没有它们的情况下如何在原生的GAS中工作，但我强烈建议你研究一下它们并考虑将它们添加到你的项目中。

要访问`GameplayEffectContainers`中的`GESpecs`来做一些类似添加`SetByCallers`的事情，`Break` `FGameplayEffectContainer`并通过`GESpecs`数组中的索引来访问`GESpec`引用。这要求你在访问`GESpec`之前就知道它的索引。

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` also contain an optional efficient means of [targeting](#concepts-targeting-containers).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 GameplayAbility定义

[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`) 是指`Actor`在游戏中可以执行的任何行动或技能。可以同时激活多个`GameplayAbility`，例如冲刺和射击。它们可以在蓝图或c++中创建。

`GameplayAbilities`举例:
* 跳跃
* 冲刺
* 开枪
* 被动地每隔X秒阻挡一次攻击
* 喝药
* 开门
* 收集资源
* 造房子

不该用`GameplayAbilities`实现的事情:
* 基础移动
* 与UI的交互部分--不要用`GameplayAbility`从商店买东西。

他们并不是规则，只是我的建议。你的设计和实现可能会有所不同。

`GameplayAbility `带有分级别的修改属性的变化或更改`GameplayAbility`功能的默认功能。。

`GameplayAbilities`根据[`网络执行策略NetExecutionPolicy`](#concepts-ga-net)在拥有的客户端和/或服务器上运行，而不是模拟代理。`NetExecutionPolicy`决定`GameplayAbility`是否会被本地[预测predicted](#concepts-p)。它们包括[可选的成本和冷却`GameplayEffects`](#concepts-ga-commit)的默认行为。`GameplayAbilities`使用[`AbilityTasks`](#concepts-at)来表示随着时间推移而发生的操作，例如等待事件发生，等待属性改变，等待玩家选择目标，或者使用`Root Motion Source`移动一个`Character`。**模拟的客户端不会运行`GameplayAbilities`**。相反，当服务器运行`Ability`时，任何视觉上需要在模拟代理上播放的内容(如`AnimationMontages`)将通过`AbilityTasks`或[`GameplayCues`](#concepts-gc)进行复制或RPC处理，如声音和粒子等外观内容。

所有的`GameplayAbilities`都有它们自己的被玩法逻辑重载的`ActivateAbility()`。当`GameplayAbility`完成或被取消时，可以将额外的逻辑添加到`EndAbility()`中。

一个简单`GameplayAbility`的流程图:
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)


一个复杂些的`GameplayAbility`的流程图:
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

复杂的`Abilities`可以通过多个相互作用(激活、取消等)的`GameplayAbilities`来实现。

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 Replication Policy

不要使用这个选项。这个名字有误导性，你不需要它。默认情况下，[`GameplayAbilitySpecs`](#concepts-ga-spec)会从服务器复制到拥有它的客户端。如上所述，**`GameplayAbilities`不会在模拟代理上运行**。他们使用`AbilityTasks`和`GameplayCues`来`Replicate`或`RPC（远程调用）` 视觉变化到模拟代理。来自Epic的Dave Ratti表示他希望[在未来删除这个选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation

这个选项往往会带来麻烦。这意味着如果客户端的`GameplayAbility`由于取消或自然完成而结束，它将强制服务器的版本结束，无论它是否完成。后一个问题非常重要，特别是对于具有高延迟的玩家所使用的本地预测的`GameplayAbility`。一般情况下，您需要禁用此选项。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly

设置此选项将始终向服务器复制输入的按下和释放事件。如果你的[输入绑定到你的`ASC`](#concepts-ga-input)， Epic建议不要使用它，而是依赖于内置在现有输入相关的[`AbilityTasks`](#concepts-at)中的`Generic Replicated Events`。

Epic's comment:
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 Binding Input to the ASC

`ASC`允许你直接将`输入操作InputAction`绑定到它，并在授予它们时将这些输入分配给`GameplayAbilities`。按下注册给`GameplayAbilities`的`输入操作InputAction`且满足`GameplayTag`需求，则自动激活`GameplayAbilities`。指定的`输入操作InputAction`需要使用内置的响应输入的`AbilityTasks`。

除了指定用于激活`GameplayAbilities`的`输入操作InputAction`，`ASC`还接受通用的`Confirm`和`Cancel`输入。这些特殊的输入被`AbilityTasks`用于确认或取消诸如[`Target Actors`](#concepts- targets - Actors)之类的事情。

要将Input绑定到`ASC`，你必须首先创建一个将输入操作名称转换为字节的枚举。枚举名称必须与项目设置中用于输入操作的名称完全匹配。`DisplayName`并不重要。

示例项目中：
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

如果你的`ASC`存在于`Character`中，那么在`SetupPlayerInputComponent()`中包含绑定到`ASC`的函数:

```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你的`ASC`存在于`PlayerState`中，那么在`SetupPlayerInputComponent()`中存在一个潜在的竞争条件，即`PlayerState`可能还没有复制到客户端。因此，我建议尝试在`SetupPlayerInputComponent()`和`OnRep_PlayerState()`中绑定input。`OnRep_PlayerState()`本身是不够的，因为当`PlayerState`的`复制Replicates`早于`PlayerController`告诉客户端调用`ClientRestart()`创建`InputComponent`时，`Actor`的`InputComponent`可能为null。示例项目演示了如何在两个位置上使用布尔值控制绑定流程，这样它实际上只绑定一次输入。

**注意:** 在示例项目中，枚举中的`Confirm`和`Cancel`与项目设置中的输入操作名称(`ConfirmTarget`和`CancelTarget`)不匹配，但我们在`BindAbilityActivationToInputComponent()`中提供了它们之间的映射。这很特别，因为我们提供的映射让他们可以在不真的匹配的情况下实现匹配。枚举中的所有其他输入必须与项目设置中的输入操作名称匹配。

对于`GameplayAbilities`，它只会被一个输入激活(它们总是存在于相同的“插槽”中，就像MOBA一样)，我更喜欢在我的`UGameplayAbility`子类中添加一个变量，我可以定义它们的输入。然后我可以在授予能力时从`ClassDefaultObject`中读取它。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 Binding to Input without Activating Abilities

如果你不想让你的`GameplayAbilities`在输入被按下时自动激活，但仍然将它们绑定到输入并与`AbilityTasks`一起使用，你可以在你的`UGameplayAbility`子类中添加一个新的bool变量`bActivateOnInput`，默认值为`true`，并重载`UAbilitySystemComponent::AbilityLocalInputPressed()`。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 Granting Abilities

将`GameplayAbility`授予`ASC`将其添加到`ASC`的`ActivatableAbilities`列表中，允许其满足[`GameplayTag`需求](#concepts-ga-tags)时随意激活`GameplayAbility`。

我们在服务器上授予`GameplayAbility`，然后自动将[`GameplayAbilitySpec`](#concepts-ga-spec)复制到拥有客户端。其他客户端/模拟代理不接收`GameplayAbilitySpec`。

示例工程在`Character`类中存储了一个在游戏开始时读取并授予的`TArray<TSubclassOf<UGDGameplayAbility>>`:

```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->CharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->CharacterAbilitiesGiven = true;
}
```

当授予这些`GameplayAbility`时，我们使用`UGameplayAbility`类、能力级别、它绑定的输入以及`SourceObject`或其他将此`GameplayAbility`赋予此`ASC`的对象来创建`GameplayAbilitySpecs`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 激活Abilities

如果给`GameplayAbility`指定了一个输入动作，那么当输入被按下且满足`GameplayTag`需求时它将自动激活。这可能并不总是激活`GameplayAbility`的理想方式。`ASC`还提供了其他四种激活`GameplayAbility`的方法:通过`GameplayTag`、`GameplayAbility`类、`GameplayAbilitySpec`句柄，以及通过事件。通过事件激活`GameplayAbility`允许你[通过事件传递有效载荷数据（payload data）](#concepts-ga-data)。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```

要通过事件激活`GameplayAbility`， `GameplayAbility`必须在`GameplayAbility`中设置它的`Triggers`。指定一个`GameplayTag`并为`GameplayEvent`选择一个选项。要发送事件，使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。通过事件激活`GameplayAbility`允许你传入一个带有数据的`有效载荷（payload data）`。

`GameplayAbility` `Triggers`也允许你在添加或删除`GameplayTag`时激活`GameplayAbility`。

**注意：** 当在蓝图中通过事件激活`GameplayAbility`时，你必须使用`ActivateAbilityFromEvent`节点，且标准的`ActivateAbility`节点**不能出现**在你的Graph中。如果`ActivateAbility`节点存在，那它将始终替代`ActivateAbilityFromEvent`被调用。

**注意：** 当`GameplayAbility`终止时，不要忘记调用`EndAbility()`，除非你的`GameplayAbility`总是像被动技能一样运行。

**本地预测（predicted）**的`GameplayAbilities`的激活序列:

1. **Owning client**调用 `TryActivateAbility()`
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()`并返回`GameplayTag`是否满足要求，`ASC`是否能承担成本，`GameplayAbility`是否在CD中，以及当前有没有其他实例处于活动状态
1. 调用 `CallServerTryActivateAbility()`并传递它生成的`Prediction Key`
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()`，Epic称其为“初始化模板”
1. 调用 `ActivateAbility()`，最终激活`Abilities`

**服务器** 接受 `CallServerTryActivateAbility()`
1. 调用 `ServerTryActivateAbility()`
1. 调用 `InternalServerTryActivateAbility()` 
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()`并返回`GameplayTag`是否满足要求，`ASC`是否能承担成本，`GameplayAbility`是否在CD中，以及当前有没有其他实例处于活动状态
1. 调用 `ClientActivateAbilitySucceed()`，如果成功则告诉它更新它的`ActivationInfo`，它的激活已经被服务器确认，并广播`OnConfirmDelegate`委托。这与输入确认不同。
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()`，Epic称其为“初始化模板”
1. 调用 `ActivateAbility()`，最终激活`Abilities`

服务器如果在任何发生激活失败，它将调用`ClientActivateAbilityFailed()`，立即终止客户端的`GameplayAbility`并撤销任何预期的更改。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 被动Abilities

要实现自动激活并持续运行的被动`GameplayAbility`，需要重载`UGameplayAbility::OnAvatarSet()`，该方法在授予`GameplayAbility`属性并设置`AvatarActor`时自动调用，并调用`TryActivateAbility()`。

我建议在自定义的`UGameplayAbility`类中添加一个`bool`来指定是否应该在授予`GameplayAbility`时被激活。示例工程的被动叠护甲功能就是这么做的。

被动`GameplayAbilities`的`Net Execution Policy` (#concepts-ga-net)通常是`Server Only`。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (ActivateAbilityOnGranted)
	{
		bool ActivatedAbility = ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic将此函数描述为初始化被动技能和进行类似`BeginPlay`这样的事情的正确位置。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 取消Abilities

要从内部取消`GameplayAbility`，你可以调用`CancelAbility()`。这将调用`EndAbility()`并将其`WasCancelled`参数设置为true。

要从外部取消`GameplayAbility`，`ASC`提供了几个函数:

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);	

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**注意：** 我发现如果你有一个`非实例化`的`CancelAllAbilities`，`CancelAllAbilities`似乎不能正常工作。它似乎因命中了`非实例化`的`GameplayAbility`而放弃了。`CancelAbilities`可以更好地处理`非实例化`的`GameplayAbilities`，这也就是示例工程所使用的(Jump是一个`非实例化`的`GameplayAbility`)。你的想法可能有所不同。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 Getting Active Abilities

初学者经常会问“我怎样才能获得主动能力?”也许是设置它的变量或者取消它。因为可同时激活多个`GameplayAbility`，所以不存在一个"active ability"。相反，你必须在`ASC`的`ActivatableAbilities`列表中搜索(`ASC`拥有的`GameplayAbilities`)，并找到与你想要的[`Asset`或授予的`GameplayTag`](#concepts-ga-tags)相匹配的那个。

`UAbilitySystemComponent::GetActivatableAbilities()`返回一个`TArray<FGameplayAbilitySpec>`供你迭代。

`ASC`还有另一个辅助函数，它接受`GameplayTagContainer`作为参数来协助搜索，而不是手动遍历`GameplayAbilitySpecs`列表。`bOnlyAbilitiesThatSatisfyTagRequirements`参数将只返回满足`GameplayTag`要求的`GameplayAbilitySpecs`，并且可以立即激活。例如，你可以拥有两种基本的攻击`GameplayAbilities`，一种是武器，一种是赤手空拳，而正确的攻击方式则取决于武器是否装备，并设置`GameplayTag`要求。有关更多信息，请参阅Epic对该函数的注释。

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你获得了你想要的`FGameplayAbilitySpec`，你就可以调用它的`IsActive()`方法。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 Instancing Policy

`GameplayAbility`的`Instancing Policy`决定了`GameplayAbility`在激活时是否以及如何被实例化。

| `Instancing Policy`     | 描述                                                                                      | 使用场景示例                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 每个`ASC`只有一个`GameplayAbility`实例，每次激活复用。    | 这可能是你使用最多的`Instancing Policy`。您可以将它用于任何Ability，并提供激活之间的持久性。设计者负责手动在每次激活间重置变量。                                                                                                                                                             |
| Instanced Per Execution | 每次`GameplayAbility`被激活时，都会创建一个`GameplayAbility`的新实例。 | 这些`GameplayAbilities`的好处是，每次你激活时都会重置变量。这些提供了比`Instanced Per Actor`更糟糕的性能，因为它们每次激活都会生成新的`GameplayAbilities`。示例工程没在任何地方使用这种策略。                                                                                                                                 |
| Non-Instanced           | `GameplayAbility`作用于它的`ClassDefaultObject`。没有创建实例。            | 这是三个方法中性能最好的，但也是限制最多的。`Non-Instanced`的`GameplayAbilities`不能存储状态，这意味着没有动态变量，也没有绑定到`AbilityTask`委托。使用它们的最佳场所是经常使用的简单能力，如MOBA或RTS中的小兵基础攻击。示例工程的跳跃`GameplayAbility`是`Non-Instanced`的。|


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 Net Execution Policy

`GameplayAbility`的`Net Execution Policy`决定了谁以什么顺序运行`GameplayAbility`。

| `Net Execution Policy` | Description                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility`仅在客户端上运行。这对于只做本地外观改变的Abilities可能是有用的。单人游戏应该只使用`Server Only`。                                    |
| `Local Predicted`      | `Local Predicted`的`GameplayAbilities`首先在客户端激活，然后在服务器上激活。服务器的版本会纠正客户端的错误预测。 详见[预测Prediction](#concepts-p). |
| `Server Only`          | `GameplayAbility`只在服务器上运行。被动的`GameplayAbilities`通常是`Server Only`。单人游戏应该使用此策略。                                                                  |
| `Server Initiated`     | `Server Initiated`的`GameplayAbilities`首先在服务器上激活，然后在拥有的客户端上激活。我个人并不经常使用这个。                                                                | 



**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 Ability Tags
`GameplayAbilities` come with `GameplayTagContainers` with built-in logic. None of these `GameplayTags` are replicated.



带有内置逻辑的`GameplayAbilities`与`GameplayTagContainers`一起出现。这些`GameplayTags`都没有被复制。

| `GameplayTag Container`     | Description                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility`拥有的`GameplayTags`。这些只是用来描述`GameplayAbility`的`GameplayTags`。                                                                              |
| `Cancel Abilities with Tag` | 当`GameplayAbility`被激活时，其他有此`GameplayTags`的`GameplayAbilitie`将被取消。                                                  |
| `Block Abilities with Tag`  | 当`GameplayAbility`被激活时，其他有此`GameplayTags`的`GameplayAbilitie`将被阻止激活。                                          |
| `Activation Owned Tags`     | 当`GameplayAbility`处于激活状态时，这些`GameplayTags`将被赋予`GameplayAbility`的所有者。记住这些是不能复制的。                                                  |
| `Activation Required Tags`  | 这个`GameplayAbility`只有在所有者拥有**所有**这些`GameplayTags`时才能被激活。                                                                                                |
| `Activation Blocked Tags`   | 如果所有者拥有**任何**这些`GameplayTags`，则无法激活此`GameplayAbility`。                                                                                                  |
| `Source Required Tags`      | 只有当`Source`拥有**所有**这些`GameplayTags`时，这个`GameplayAbility`才能被激活。只有当`GameplayAbility`通过事件触发时，`Source`的`GameplayTags`才会被设置。 |
| `Source Blocked Tags`       | 如果`Source`有**任何** 这些`GameplayTags`，则`GameplayAbility`无法激活。只有当`GameplayAbility`通过事件触发时，`Source`的`GameplayTags`才会被设置。   |
| `Target Required Tags`      | 这个`GameplayAbility`只有在`Target`拥有**所有**这些`GameplayTags`时才能被激活。只有当`GameplayAbility`通过事件触发时，`Target`的`GameplayTags`才会被设置。 |
| `Target Blocked Tags`       | 如果`Target`有**任何**这样的`GameplayTags`，则无法激活此`GameplayAbility`。只有当`GameplayAbility`通过事件触发时，`Target`的`GameplayTags`才会被设置。   |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec

在`GameplayAbility`被授予后，`GameplayAbilitySpec`才会存在于`ASC`中，并定义了可激活的`GameplayAbility`，即`GameplayAbility`类、级别、输入绑定和运行时状态，这些必须与`GameplayAbility`类保持分离。

当在服务器上授予`GameplayAbility`时，服务器将`GameplayAbilitySpec` `复制（replicates）`到拥有它的客户端，以便她可以激活它。

激活`GameplayAbilitySpec`将根据`GameplayAbility`的`Instancing Policy`创建`GameplayAbility`的实例（除非是`Non-Instanced`的`GameplayAbility`）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 Passing Data to Abilities

`GameplayAbilities`的一般范式是`Activate->Generate Data->Apply->End`。有时需要对已有数据进行操作。GAS提供了一些将外部数据导入`GameplayAbilities`的选项:

| Method                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Activate `GameplayAbility` by Event             | 使用包含`数据载荷（Payload）`的事件激活`GameplayAbility`。事件的`数据载荷（Payload）`从客户端复制到服务器，用于本地`预测（predicted）`的` GameplayAbility`。对于不适合任何现有变量的任意数据，请使用两个`Optional Object`或[`TargetData`](#concepts-targeting-data)变量。这样做的缺点是，它阻止您通过输入绑定来激活`Ability`。要通过事件激活`GameplayAbility`， `GameplayAbility`必须设置它的`Triggers`。为`GameplayEvent`指定一个`GameplayTag`并选择一个选项。要发送事件，使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。 |
| Use `WaitGameplayEvent` `AbilityTask`           | 使用`WaitGameplayEvent`的`AbilityTask`来告诉`GameplayAbility`在激活后监听带有`数据载荷（Payload）`的事件。事件的`数据载荷（Payload）`和发送事件的过程与`Activate GameplayAbility by Event `相同。这样做的缺点是，事件不能被`AbilityTask`复制，只能用于`Local only`和`Server only`的`GameplayAbilities`。你可以编写自己的`AbilityTask`来`复制（Replicate）`事件`数据载荷（Payload）`。                                                                                                                                                                                                                                                                                              |
| Use `TargetData`                                | 一个自定义的`TargetData`结构体是在客户端和服务器之间传递任意数据的好方法。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Store Data on the `OwnerActor` or `AvatarActor` | 使用存储在`OwnerActor`、`AvatarActor`或任何其他你可以引用的对象中的`复制（Replicate）`的变量。这个方法是最灵活的，而且可以和输入绑定激活的`GameplayAbilities`一起工作。但是，它不能保证数据在使用时是通过`复制（Replicate）`即时同步的。你必须提前确保数据的同步——这意味着如果你设置了一个`复制（Replicate）`的变量，然后立即激活`GameplayAbility`，此时接收端却因潜在的丢包问题而不能保证按顺序接收指令（比如接收端上变量的同步在激活`GameplayAbility`之后才发生）。                                                                                                                


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 Ability的成本与冷却

`GameplayAbilities`带有可选成本和冷却时间的功能。

成本是为了激活`GameplayAbilities`，`ASC`必须具有的预定义的`Attributes`数量，通过`Instant`的`GameplayEffect` ([`Cost GE`](#concepts-ge-cost))实现。冷却时间是一个定时器，它可以防止`GameplayAbility`在计时期间重新激活，通过`Duration`的`GameplayEffect`实现([`Cooldown GE`](#concepts-ge-cooldown))。

在`GameplayAbility`调用`UGameplayAbility::Activate()`之前，它会调用`UGameplayAbility::CanActivateAbility()`。这个函数检查拥有的`ASC`是否负担得起成本(`UGameplayAbility::CheckCost()`)，并确保`GameplayAbility`没有处于冷却状态(`UGameplayAbility::CheckCooldown()`)。

在`GameplayAbility`调用`Activate()`之后，它可以随时使用`UGameplayAbility::CommitAbility()`提交成本和冷却时间，该方法会调用`UGameplayAbility::CommitCost()`和`UGameplayAbility::CommitCooldown()`。设计者可以在它们不该同时提交时选择分别调用`CommitCost()`或`CommitCooldown()`。提交成本和冷却时间会再次调用`CheckCost()`和`CheckCooldown()`，这是`GameplayAbility`失败的最后一次机会。拥有的`ASC`的`Attributes`可能在`GameplayAbility`被激活后，但提交成本时失败的情况下发生变化。如果[prediction key](#concepts-p-key)在提交时有效，则提交成本和冷却时间可以[locally predicted](#concepts-p)。

详见[`CostGE`](#concepts-ge-cost)和[`CooldownGE`](#concepts-ge-cooldown)了解实现细节.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 升级Abilities

有两种提升`Ability`的常见方法:

| 升级方式                            | 描述                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 删旧的加新的   | 从`ASC`中取消授予(移除)`GameplayAbility`，并在服务器上重新授予下一级别的`GameplayAbility`。如果`GameplayAbility`在当时处于激活状态，那么它将终止。                                   |
| 提升`GameplayAbilitySpec`的等级 | 在服务器上，找到`GameplayAbilitySpec`，增加它的等级，添加脏标记以便`复制Replicates`到拥有的客户端。如果`GameplayAbility`在当前处于激活状态，这个方法不会终止它。 |

这两种方法的主要区别在于，你是否希望在升级时取消活跃的`GameplayAbilities`。根据你的`GameplayAbilities`，你很可能这两种方法都想用。我建议在你的`UGameplayAbility`子类中添加一个`bool`来指定要使用的方法。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 Ability Sets

`GameplayAbilitySets`是一个为那些具有授予`GameplayAbilities`的逻辑的角色保存输入绑定和`GameplayAbilities`列表的方便的`UDataAsset`类。子类还可以包含额外的逻辑或属性。《Paragon》为每个英雄设置了一个包括所有给定的`GameplayAbilities`的`GameplayAbilitySet`。

目前而言，对`GameplayAbilitySets`的详解我觉得没太大必要。示例项目处理了`GDCharacterBase`及其子类中的`GameplayAbilitySets`的所有功能。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 Ability Batching

传统的`Gameplay Ability`生命周期包括从客户端到服务器的至少2到3次rpc请求。

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (可选)
1. `ServerEndAbility()`

如果`GameplayAbility`在一帧中以原子分组的方式执行所有这些操作，我们可以优化这个工作流，将两个或三个RPC打包(合并)为一个RPC。`GAS`将这种RPC优化称为`Ability Batching`。关于何时使用`Ability Batching`的常见例子是使用`命中扫描（Hitscan）`算法的枪械。`命中扫描（Hitscan）`的枪激发，做一个射线检测，发送[`TargetData`](#concepts-targeting-data)到服务器，并在一帧中结束所有原子组的能力。[GASShooter](https://github.com/tranek/GASShooter)示例项目演示了使用`命中扫描（Hitscan）`算法的枪的技巧。

半自动枪支是最好的场景，将`CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`(子弹命中的结果)和`ServerEndAbility()`打包入一个RPC中，而不是三个RPC。

全自动/枪械把第一发子弹的`CallServerTryActivateAbility()`和`ServerSetReplicatedTargetData()`打包入一个RPC，而不是两个。之后的每发子弹都有他们自己的`ServerSetReplicatedTargetData()` RPC。最后，当枪支停止射击时，`ServerEndAbility()`作为一个单独的RPC发送。这是最坏的情况，即我们只保存第一颗子弹的一个RPC，而不是两个。这种情况也可以通过使用[`Gameplay Event`](#concepts-ga-data)激活能力来实现，它将从客户端将子弹的`TargetData`与`EventPayload`一起发送到服务器。后一种方法的缺点是必须在Ability外部生成`TargetData`，而Batching方法在Ability内部生成`TargetData`。

在[`ASC`](#concepts-asc)上默认禁用`Ability Batching`。要启用`Ability Batching`，需要重载`ShouldDoServerAbilityRPCBatch()`以返回true:

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

现在`Ability Batching`可用了，在激活你想要打包的能力之前，你必须事先创建一个`FScopedServerAbilityRPCBatcher`结构体。这个特殊的结构体将尝试打包处理其范围内的任何能力。一旦`FScopedServerAbilityRPCBatcher`超出范围，任何激活的能力都不会尝试打包处理。`FScopedServerAbilityRPCBatcher`的工作原理是在每个可以打包处理的函数中都有特殊的代码，这些代码拦截了发送RPC的调用，并将消息打包到一个批处理结构体中。当`FScopedServerAbilityRPCBatcher`超出范围时，它会自动在`UAbilitySystemComponent::EndServerAbilityRPCBatch()`中向服务器发送这个批处理结构体。服务器在`UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(fserverabilityrpcbatch&batchinfo)`中接收到批量RPC。`BatchInfo`参数将包含标记，用于判断能力是否应该结束、激活时是否按下了输入、`TargetData`是否被包含。这是一个很好的用来打断点确认你的Batching过程是否正常的函数。或者，通过`AbilitySystem.ServerRPCBatching.Log 1`来启用特殊的`Ability Batching`日志。

这种机制只能在c++中，且只能靠`FGameplayAbilitySpecHandle`来激活能力时实现。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter为半自动和全自动枪械复用了相同的`Batched GameplayAbility`，但从不直接调用`EndAbility()`（它是由一个外部的`local-only`的Ability处理的，该能力基于当前的开火方式管理玩家的输入和对`Batched Ability`的调用）。由于所有的RPC都必须在`FScopedServerAbilityRPCBatcher`的范围内进行，所以我提供了`EndAbilityImmediately`参数，以便让 控制/本地管理（managing local-only） 可以说明此Ability是否应当打包`EndAbility()`的调用（半自动），或者不应打包`EndAbility()`的调用（全自动），而是在它自己的RPC发生后被调用。

GASShooter暴露了一个蓝图节点，允许之前提到的`local-only ability`触发`batched Abilities`的打包。


![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 Net Security Policy

一个`GameplayAbility`的`NetSecurityPolicy`决定了一个能力应该在网络的哪部分执行。它可以防止客户端试图执行受限能力。

| `NetSecurityPolicy`     | Description                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | 没有安全需求。客户端或服务器可以自由地触发此Ability的执行和终止。                                           |
| `ServerOnlyExecution`   | 请求执行此功能的客户端将被服务器忽略。客户端仍然可以请求服务器取消或终止此Ability。 |
| `ServerOnlyTermination` | 客户端请求取消或终止此功能将被服务器忽略。客户端仍然可以请求执行此Ability。      |
| `ServerOnly`            | 服务器控制此Ability的执行和终止。客户端发出的任何请求都会被忽略。                                      |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>
### 4.7.1 Ability Task 描述

`GameplayAbilities`只在一帧中执行。这本身不允许太大的灵活性。为了执行随着时间推移而发生的操作或需要对稍后某个时间点触发的委托做出响应，我们使用称为`AbilityTasks`的潜在操作。

GAS有很多随时可用的`AbilityTasks`:
* 使用`RootMotionSource`移动Characters的Tasks
* 一个用来播放AnimationMontages的Task
* 用来响应`Attribute`变化的Tasks
* 用来响应`GameplayEffect`变化的Tasks
* 用来响应玩家输入的Tasks
* 以及更多

`UAbilityTask`构造函数强制硬编码游戏范围内最多1000个并发的`AbilityTasks`同时运行。在设计像RTS游戏那样同时拥有数百个角色的`GameplayAbilities`时，请记住这一点。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 自定义Ability Tasks

通常你会创建自己的自定义`AbilityTasks`(用c++)。示例项目包含两个自定义的`AbilityTasks`:

1. `PlayMontageAndWaitForEvent`是默认的`PlayMontageAndWait`和`WaitGameplayEvent`以及`AbilityTasks`的组合。这允许`AnimationMontages`将事件通过`AnimNotifies`发回播放它们的`GameplayAbility`。使用它在`AnimationMontages`的特定时间触发动作。

1. `WaitReceiveDamage`监听`OwnerActor`以接收伤害值。当英雄受到伤害时，`GameplayAbility`会移除一些护甲。

`AbilityTasks`是由以下组成的:

* 创建`AbilityTask`新实例的静态函数
* 当`AbilityTask`完成其目的时进行广播的代理
* 一个`Activate()`函数来启动它的主要工作，绑定到外部委托，等等
* 一个用于清理的`OnDestroy()`函数，包括它绑定的外部委托
* 它绑定到的任何外部委托的回调函数
* 成员变量和任何内部辅助函数

**注意：** `AbilityTasks`只能声明一种类型的输出委托。所有的输出委托都必须是这种类型，无论它们是否使用参数。为未使用的委托参数传递默认值。

`AbilityTasks`只在运行`GameplayAbility`的客户端或服务器上运行；但是，`AbilityTasks`可以通过在`AbilityTask`构造函数中设置`bSimulatedTask = true;`，重写`virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`，并设置任何成员变量为`复制的（Replicated）` 来在模拟客户端上运行。这只在极少数情况下有用，如移动类型`AbilityTasks`，你不想`复制的（Replicate）`每个移动变化，而是`模拟（simulate）`整个移动`AbilityTask`。所有的`RootMotionSource`的`AbilityTasks`都会这样做。以`AbilityTask_MoveToLocation.h/.cpp`为例。

如果在`AbilityTask`构造函数中设置`bTickingTask = true;`并重写`virtual void TickTask(float DeltaTime);`， `AbilityTasks`就可以`Tick`了。这当你需要平滑地跨帧进行插值时很有用。以`AbilityTask_MoveToLocation.h/.cpp`为例。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 使用 Ability Tasks

通过C++创建并激活一个`AbilityTask`（From `GDGA_FireGun.cpp`）：

```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在蓝图中，我们只使用为`AbilityTask`创建的蓝图节点。我们不需要调用`ReadyForActivate()`。这个函数会被`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`自动调用。如果你的`AbilityTask`类中存在`BeginSpawningActor()`和`FinishSpawningActor()`， `K2Node_LatentGameplayTaskCall`也会自动调用它们(参见`AbilityTask_WaitTargetData`)。重申一下，`K2Node_LatentGameplayTaskCall`只对蓝图起自动魔法作用。在c++中，我们必须手动调用`ReadyForActivation()`、`BeginSpawningActor()`和`FinishSpawningActor()`。

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

要手动取消`AbilityTask`，只需在蓝图(被称为`Async Task Proxy`)或c++中调用`AbilityTask`对象的`EndTask()`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 Root Motion Source Ability Tasks

GAS带有随着时间移动`Characters`的`AbilityTasks`，例如倒退，复杂跳跃，推动，以及在`CharacterMovementComponent`中hook`Root Motion Sources`的冲刺。

**注意：** 预测（Predicting） `RootMotionSource`的`AbilityTasks`在引擎4.19以及4.25以上生效. 预测（Predicting）在引擎版本4.20-4.24有BUG; 然而，`AbilityTasks`仍然在优质网络条件的多人游戏中发挥其功能，并在单人游戏中完美运行。可以将[prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c)功能从4.25版本中筛除，加入自定义的4.20-4.24版本的引擎。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 Gameplay Cue Definition

`GameplayCues` (`GC`)执行与游戏玩法无关的内容，如音效、粒子效果、相机抖动等。`GameplayCues`通常被`复制（Replicated）`(除非显式地在本地`Executed`、`Added`或`Removed`)并`预测Predicted`。

我们通过发送一个对应的`GameplayTag`来触发`GameplayCues`，这个`GameplayTag`的**父元素名称必须是`GameplayCue`**。并且通过`ASC`将事件类型(`Execute`、`Add`或`Remove`)传递给`GameplayCueManager`。`GameplayCueNotify`对象和其他实现了` IGameplayCueInterface`的`Actors`可以基于`GameplayCue`的`GameplayTag` (`GameplayCueTag`)来监听这些事件。

**注意：** 重申一下，`GameplayCue`的`GameplayTags`需要从`GameplayCue`的父级`GameplayTag`开始。例如，一个有效的`GameplayCue`的`GameplayTag`可能是`GameplayCue. a.b.c `。

`GameplayCueNotifies`有两个类，`Static`和`Actor`。它们对不同的事件做出反应，不同类型的`GameplayEffects`可以触发它们。用你的逻辑重写相应的事件。



| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | 静态`GameplayCueNotifies`在`ClassDefaultObject`上进行操作(意味着没有实例)，并且非常适合用于一次性效果，比如命中效果。                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | Actor的`GameplayCueNotifies`会在`Added`时生成一个新实例。因为它们是实例化的，所以它们可以在被`Removed`前随着时间的推移进行操作。它非常适合那些要在`Duration`或`Infinite`的`GameplayEffect`被自动或手动移除时随之消失的循环声音和粒子效果。它还带有控制同时间内允许的`Added`数量的选项，以便多次应用相同效果时只启动一次声音或粒子。 |

`GameplayCueNotifies`技术上可以响应任何事件，但上面这些事我们通常使用它们的方式。

**注意：** 当使用`GameplayCueNotify_Actor`时，请检查`Auto Destroy on Remove`，否则后续调用`Add`时`GameplayCueTag`将不起作用。

当使用除`Full`之外的`ASC` [Replication Mode](#concepts-asc-rm)时，`Add`和`Remove` `GC`事件将在服务器玩家(listen server)上触发两次，一次用于应用`GE`，另一次从“最小”的`NetMultiCast`到客户端。然而，`WhileActive`事件仍然只会触发一次。所有事件只会在客户端触发一次。

示例项目包括用于眩晕和冲刺效果的`GameplayCueNotify_Actor`。它还有一个`GameplayCueNotify_Static`用于开枪的命中效果。这些`GC`可以通过[本地触发它们](#concepts-gc-local)而不是通过`GE`复制它们来进一步优化。我选择在示例项目中以初学者的方式使用它们。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Triggering Gameplay Cues

当成功应用`GameplayEffect`时(不被Tags或免疫阻止)，应在其内部填写所有应被触发的`GameplayCues`的`GameplayTags`。

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility`为`Execute`、`Add`或`Remove`一个`GameplayCues`提供了蓝图节点。

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在c++中，你可以直接在`ASC`上调用函数(或者在你的子类`ASC`中将其暴露给蓝图)：

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 Local Gameplay Cues

默认情况下，触发`GameplayAbilities`和`ASC`的用于生成`GameplayCues`的暴露给蓝图的函数都会被`复制Replicated`。每个`GameplayCue`事件都是一个多播RPC。这会导致大量的RPC请求。GAS还强制每次网络更新最多有两个相同的`GameplayCue`RPC。我们可以通过使用本地`GameplayCues`来避免这种情况。本地的`GameplayCues`只能在自己客户端上执行`Execute`、`Add`或`Remove`。

我们可以使用本地`GameplayCues`的场景:
* 子弹命中
* 近战命中
* 从AnimationMontages触发的`GameplayCues`

你应该添加到你的`ASC`子类中的本地`GameplayCue`函数:

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 Gameplay Cue Parameters

`GameplayCue`接收一个包含了`GameplayCue`额外信息的`FGameplayCueParameters`结构体作为参数。如果你手动从`GameplayAbility`或`ASC`的函数中触发`GameplayCue`，那么你必须手动填写传递给`GameplayCue`的`GameplayCueParameters`结构体。如果`GameplayCue`是由`GameplayEffect`触发的，那么以下变量将自动填充到`GameplayCueParameters`结构体中:

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (如果`GameplayEffect`在`GameplayCue`Tag容器上选择了一个`Magnitude`的`Attribute`，且有影响此`Attribute`的对应`Modifier`)

当手动触发`GameplayCue`时，`GameplayCueParameters`结构体中的`SourceObject`变量可能是一个向`GameplayCue`传递任意数据的好地方。

**注意：** 参数结构中的一些变量，例如`Instigator`，可能已经存在于`EffectContext`中。`EffectContext`还可以包含一个`FHitResult`，用于指定在世界中产生`GameplayCue`的位置。继承`EffectContext`是传递更多数据给`GameplayCues`的潜在好方法，特别是那些由`GameplayEffect`触发的数据。

以下是3个在[`UAbilitySystemGlobals`](#concepts-asg)中的为`GameplayCueParameters`结构体填充更多信息的函数。它们是virtual的，因此您可以重写它们以自动生成更多信息。

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager

默认情况下，`GameplayCueManager`会扫描整个游戏目录中的`GameplayCueNotifies`，并在播放时将它们加载到内存中。我们可以在`DefaultGame.ini`中设置`GameplayCueManager`的扫描路径。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们希望`GameplayCueManager`能够扫描并找到所有的`GameplayCueNotifies`；但是，我们不希望它在运行时每个都异步加载。这将导致其无论使用与否，每个`GameplayCueNotify`和它们所有的音效和粒子都会放入内存中。在像《Paragon》这样的大型游戏中，这可能会在内存中带来几百MB的无用资源，并导致卡顿和启动卡死。

除了在启动时异步加载每个`GameplayCue`之外，另一个选择是只在游戏中触发`GameplayCue`时异步加载它们。这意味着用在运行时首次播放特定`GameplayCue`的潜在延迟来交换不必要的内存使用和潜在的游戏卡顿。这种潜在的延迟对于SSD来说不太是问题。我没有在机械硬盘上测试过。如果在UE编辑器中使用此选项，那当编辑器需要编译粒子系统时，可能会在首次加载`GameplayCues`时会出现轻微的卡顿。这在打包后不是问题，因为粒子系统已经编译完成了。

首先，我们必须继承`UGameplayCueManager`，并在`DefaultGame.ini`告诉`AbilitySystemGlobals`类使用我们的子类`UGameplayCueManager`。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在我们的`UGameplayCueManager`子类中，重载`ShouldAsyncLoadRuntimeObjectLibraries()`。

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 从触发中阻止Gameplay Cues

有时候我们并不想触发`GameplayCues`。例如，例如我们阻挡了一次攻击，我们可能不想播放带有伤害`GameplayEffect`的命中效果，或者播放自定义效果。我们可以在[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)中通过调用`OutExecutionOutput.MarkGameplayCuesHandledManually()`，然后手动将`GameplayCue`事件发送给`Target`或`Source`的`ASC`来实现这一点。

如果你不想让任何`GameplayCues`在特定的`ASC`上触发，你可以设置`AbilitySystemComponent->bSuppressGameplayCues = true;`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 GameplayCue批处理

每个触发的`GameplayCue`都是一个不可靠的网络多播RPC。在我们同时触发多个`GCs`的情况下，有一些优化方法可以将它们压缩到一个RPC中，或者通过发送更少的数据来节省带宽。

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 手动RPC

假设你有一把能一次发射八颗子弹的猎枪。这就有8个射线检测和命中`GameplayCues`。[GASShooter](https://github.com/tranek/GASShooter)使用了一种懒方法，它通过将所有射线检测信息作为[`TargetData`](#concepts-targeting-data)存储到 [`EffectContext`](#concepts-ge-ec)中来将它们组合到一个RPC中。虽然这将RPC从8个减少到1个，但它仍然在一个RPC中通过网络发送大量数据(约500字节)。一个更优化的方法是发送一个带有自定义结构体的RPC，在该结构体中你可以更高效地编码命中位置，或者你可以发送一个随机种子，以在接收端重建/模拟命中位置。然后客户端将解包这个自定义结构体并返回到[locally executed `GameplayCues`](#concepts-gc-local)。

这是如何工作的:

1. 声明一个`FScopedGameplayCueSendContext`。这会抑制`UGameplayCueManager::FlushPendingCues()`，直到它超出作用域，这意味着所有的`GameplayCues`都将排队等待，直到`FScopedGameplayCueSendContext`超出作用域。
1. 重写`UGameplayCueManager::FlushPendingCues()`，这将通过基于自定义的`GameplayTag`来批处理成自定义的结构体并通过RPC发送到客户端的方式来合并`GameplayCues`。
1. 客户端接收自定义结构体并将其解包到本地执行的`GameplayCues`中。

这个方法也可用于当你需要的`GameplayCues`参数与`GameplayCueParameters`不匹配，并且你不想将它们添加到`EffectContext`中时，比如伤害数字，暴击标志，破盾标志，被致命命中标志之类的情况。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 Multiple GCs on one GE

`GameplayEffect`中的所有`GameplayCues`都已经在一个RPC中发送了。默认情况下，`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`将以不可靠的网络多播发送整个`GameplayEffectSpec`(但会转换为`FGameplayEffectSpecForRPC`)而不管`ASC`的`Replication Mode`。这可能会导致大量的带宽消耗，这取决于`GameplayEffectSpec`中的内容。我们可以通过设置`AbilitySystem.AlwaysConvertGESpecToGCParams 1`来优化这一点。这将把`GameplayEffectSpecs`转换为`FGameplayCueParameter`结构体并对其进行RPC同步以取代发送整个`FGameplayEffectSpecForRPC`。这可能会节省带宽，但信息也会减少，这取决于`GESpec`如何转换为`GameplayCueParameters`以及你的`GCs`需要知道什么。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 GameplayCue的事件

`GameplayCues`会响应特定的`EGameplayCueEvents`:

| `EGameplayCueEvent` | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | 当`GameplayCue`激活时（添加时）调用。                                                                                                                                                                                                                                                                                 |
| `WhileActive`       | 当`GameplayCue`激活时调用，即使它实际上没有被应用(加入进程等等)。这不是`Tick`！他就像`OnActive`那样在`GameplayCueNotify_Actor`被添加或变得相关时调用一次。如果你需要`Tick()`，只需使用`GameplayCueNotify_Actor`的`Tick()`。毕竟他是个`AActor`。 |
| `Removed`           | 当`GameplayCue`被移除时调用。蓝图`GameplayCue`响应这个事件的函数是`OnRemove`。                                                                                                                                                                                                             |
| `Executed`          | 当执行`GameplayCue`时调用：即时的效果或周期性的`Tick()`。蓝图`GameplayCue`响应这个事件的函数是`OnExecute`。                                                                                                                                                                 |

`GameplayCue`中发生在开始时的任何事情都可以使用`OnActive`处理，但如果后来的参与者错过了也没关系。使用`WhileActive`来处理`GameplayCue`中你希望迟到的参与者看到的正在进行的效果。例如，如果你在MOBA游戏中有一个防御塔爆炸的`GameplayCue`，你就需要将最初的爆炸粒子和爆炸声音放在`OnActive`中，并将任何剩余的持续火焰粒子或声音放在`WhileActive`中。在这种情况下，对于后期的参与者来说，从`OnActive`重放最初的爆炸是没有意义的，但是你会希望他们在爆炸发生后从`WhileActive`看到地面上持续的、循环的火焰效果。`OnRemove`可以清除所有在`OnActive`和`WhileActive`中添加的内容。每当一个Actor进入`GameplayCueNotify_Actor`的关联范围时，`WhileActive`就会被调用。每当一个Actor离开`GameplayCueNotify_Actor`的关联范围时，都会调用`OnRemove`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 GameplayCue的可靠性

`GameplayCues`通常被认为是不可靠的，因此不适合直接影响游戏玩法。

**被执行的`GameplayCues`：** 这些`GameplayCues`通过不可靠的多播应用，并且总是不可靠的。

* `AutonomousProxy`可靠地接收`OnActive`、`WhileActive`和`OnRemove`。`FActiveGameplayEffectsContainer::NetDeltaSerialize()`通过调用`UAbilitySystemComponent::HandleDeferredGameplayCues()`来调用`OnActive`和`WhileActive`。`FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()`调用`OnRemoved`.
* `SimulatedProxy`可靠地接收`WhileActive`和`OnRemove`。`UAbilitySystemComponent::MinimalReplicationGameplayCues`复制调用`WhileActive` and `OnRemove`。`OnActive`事件由不可靠多播调用。

**脱离`GameplayEffect`的`GameplayCues`的应用:**

* `AutonomousProxy`可靠的接收`OnRemove`。
* `OnActive`和`WhileActive`事件被一个不可靠的`Multicast`调用。
* `SimulatedProxies`可靠的接收`WhileActive`和`OnRemove`。
* `UAbilitySystemComponent::MinimalReplicationGameplayCues`的`复制（Replication）`调用`WhileActive`和`OnRemove`。`OnActive`事件被一个不可靠的`Multicast`调用。

如果你要在`GameplayCue`中让一些东西变得`可靠（Reliable）`，那么就从`GameplayEffect`中应用它，并使用`WhileActive`来添加特效，使用`OnRemove`来删除特效。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 Ability System Globals

[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html)类保存有关GAS的全局信息。大多数变量都可以在`DefaultGame.ini`中设置。一般来说，你不需要与这个类交互，但你应该知道它的存在。如果你需要继承[`GameplayCueManager`](#concepts-gc-manager)或[`GameplayEffectContext`](#concepts-ge-context)之类的东西，你必须通过`AbilitySystemGlobals`来完成。

为了继承`AbilitySystemGlobals`需要在`DefaultGame.ini`设置类的名字:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()

从UE4.24开始，必须调用`UAbilitySystemGlobals::Get().InitGlobalData()`来使用[`TargetData`](#concepts-targeting-data)，否则你将得到与`ScriptStructCache`相关的错误，并且客户端将与服务器断开连接。这个函数在项目中只需要调用一次。《堡垒之夜》从`UAssetManager::StartInitialLoading()`中调用它，《Paragon》从`UEngine::Init()`中调用它。正如示例项目中所示，我发现将它放在`UAssetManager::StartInitialLoading()`中比较好。我认为你应该将这些样板代码复制到你的项目中，以避免`TargetData`出现问题。

如果你在使用`AbilitySystemGlobals`的`GlobalAttributeSetDefaultsTableNames`时遇到了崩溃，你可能需要像《堡垒之夜》把调用放在`AssetManager`或`GameInstance`中那样，在稍微靠后的位置调用`UAbilitySystemGlobals::Get(). initglobaldata()`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 预测（Prediction）

GAS支持客户端预测；然而，它并不能`预测（predict）`一切。GAS中的客户端`预测（predict）`意味着客户端不必等待服务器的许可来激活`GameplayAbility`并应用`GameplayEffects`。它可以`预测（predict）`服务器将允许它这样做，并预测它将应用`GameplayEffects`的目标。然后，服务器在客户端激活的一段网络延迟后运行`GameplayAbility`，并告诉客户端他的`预测（predict）`是否正确。如果客户端的任何一个`预测（predict）`是错误的，他都将“回滚”他的“错误`预测（predict）`”以匹配服务器。

GAS相关预测的最终来源是插件源代码中的`GameplayPrediction.h`。

Epic的倾向是只`预测（Predict）`你“可以不拿到”的内容。例如，《Paragon》和《堡垒之夜》就不能预测伤害。他们很可能使用[`ExecutionCalculations`](#concepts-ge-ec)来计算无法`预测（Predict）`的伤害。这并不是说你不能`预测（Predict）`某些类似伤害的必然事件。无论如何，如果你这样做了并且很有效，那就太好了。

> ... we are also not all in on a "predict everything: seamlessly and automatically" solution. We still feel player prediction is best kept to a minimum (meaning: predict the minimum amount of stuff you can get away with).

> 译：……我们并没有完全投入到一个“无缝和自动的预测一切”的解决方案中。我们仍然认为玩家`预测（Predict）`最好保持在最低水平(意思是:`预测（Predict）`最少的那些你拿不拿到都行的东西)。

*Dave Ratti from Epic's comment from the new [Network Prediction Plugin](#concepts-p-npp)*

**什么是`预测（Predict）`的：**
> * Ability的激活
> *	触发的Events
> *	GameplayEffect的应用:
>    * Attribute的修改（例外：Executions目前不`预测（Predict）`，只有AttributeModifiers`预测（Predict）`）
>    * GameplayTag的修改
> * GameplayCue事件（无论是从`预测（Predict）`的游戏效果还是它本身）
> * Montages
> * Movement (建构在UE5的UCharacterMovement中)

**什么不是`预测（Predict）`的：**
> * GameplayEffect的移除
> * GameplayEffect的周期效果(比如Tick处理的Dots)

*From `GameplayPrediction.h`*

虽然我们可以预测`GameplayEffect`的应用，但我们不能预测`GameplayEffect`的移除。我们可以绕过这个限制的一种方法是，当我们想要删除`GameplayEffect`时，预测应用一个反向效果。假设我们预测移动速度放慢40%。我们可以通过应用40%移动速度buff来移除它。然后同时删除这两个`GameplayEffects`。这并不适用于所有场景，并且仍然需要支持预测移除的`GameplayEffect`。来自Epic的Dave Ratti表示希望将其添加到[future iteration of GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)中。

因为我们无法预测`GameplayEffects`的移除，所以我们无法完全预测`GameplayAbility`的冷却，也没有像反向`GameplayEffect`那样的的解决方案。服务器`复制（Replicated）`的`Cooldown GE`将存在于客户端，任何试图绕过它(例如使用`Minimal`复制模式)的尝试都将被服务器拒绝。这意味着具有较高延迟的客户端需要更长的时间来告诉服务器继续冷却，并接收服务器的`Cooldown GE`移除。这意味着具有较高延迟的玩家将比具有较低延迟的玩家拥有更低的射速，从而使他们在面对较低延迟的玩家时处于劣势。《堡垒之夜》通过使用自定义簿记（bookkeeping）而不是`Cooldown GE`来避免这个问题。

关于预测伤害，我个人不推荐它，尽管它是大多数人在开始使用GAS时首先尝试的事情之一。我尤其不建议尝试预测死亡。虽然你可以预测伤害，但这会很棘手。比如如果你错误的预测了施加的伤害，玩家就会看到敌人的生命值反复横跳。而当你试图预测死亡，这可能会特别尴尬和令人沮丧。假设你错误预测了一个`Character`的死亡，那它会进入布娃娃状态，然后等服务器纠正它后，它又停止了布娃娃状态然后向你射击。

**注意：** `Instant`的`GameplayEffects`（比如`Cost GEs`）在改变你自己的`Attributes`时可以无缝的`预测Predicte`，`预测Predicte`其他角色的`Instant`的`Attribute`变化会在它们的`Attributes`中显示一个短暂的异常或“瑕疵”。对`Instant`的`GameplayEffects`的预测实际上与`Infinite`的`GameplayEffects`相同，因此当预测错误时它们可以回滚。当服务器的`GameplayEffect`被应用时，可能存在两个相同的`GameplayEffect`，导致`Modifier`被应用两次或在一瞬间内根本不被应用。它最终会自我纠正，但有时玩家会注意到这个小“瑕疵”。

GAS预测实现中那些正在尝试解决的问题:

> 1. “我能这样做嘛？”，预测的及本协议。
> 2. “撤销”，当预测失败时，如何撤销连带效果。
> 3. “重做”，如何在依旧从服务器`复制Replicate`的情况下避免重播我们在本地预测的连带效果。
> 4. “完整性”，如何确定我们**真的**预测到了所有的连带效果。
> 5. “依赖关系”，如何管理依赖预测和预测事件链。
> 6. “覆盖”，如何预测地覆盖由服务器复制/拥有的状态。

*From `GameplayPrediction.h`*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 Prediction Key

GAS的预测基于`Prediction Key`的概念，这是客户端在激活`GameplayAbility`时生成的整数标识符。

* 客户端在激活`GameplayAbility`时生成`Prediction Key`。这是`Activation Prediction Key`。
* 客户端通过`CallServerTryActivateAbility()`将`Prediction Key`发送给服务器。
* 当`Prediction Key`有效时，客户端将此`Prediction Key`添加到所有应用的`GameplayEffects`。
* 客户端的`Prediction Key`超出作用域。在相同的`GameplayAbility`中进一步的预测效果需要一个新的[Scoped Prediction Window](#concepts-p-windows)。
* 服务器从客户端接收`Prediction Key`。
* 服务器将此`Prediction Key`添加到它应用的所有`GameplayEffects`中。
* 服务器将`Prediction Key` `复制（Predict）`回客户端。

* 客户端从服务器接收带有应用他们的`Prediction Key`的`复制（replicate）`的`GameplayEffects`。如果任何`复制（replicate）`的`GameplayEffects`与客户端中使用相同`Prediction Key`应用的`GameplayEffects`匹配，则`预测(Predict)`正确。在客户端移除`预测(Predict)`的`GameplayEffect`之前，目标上暂时会有两个`GameplayEffect`副本。
* 客户端从服务器接收返回的`Prediction Key`。这是`Replicated Prediction Key`。这个`Prediction Key`现在被标记为过时。
* 客户端移除**所有**用过时的`Prediction Key`创建的`GameplayEffects`。服务器`复制（replicate）`的`GameplayEffects`将持续存在。客户端添加的那些`GameplayEffects`如果没收到过服务器`复制（Replicate）`的匹配版本，那就是错误的预测。

`Prediction Key`在它所`Activation`的`GameplayAbilities`的指令窗口期（instructions window）的原子分组内有效。你可以认为这只在一帧中有效。任何来自潜在操作`AbilityTasks`的回调都将不再具有有效的`Prediction Key`，除非`AbilityTask`具有一个内建的生成新 [Scoped Prediction Window](#concepts-p-windows)的同步点。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 Creating New Prediction Windows in Abilities

To predict more actions in callbacks from `AbilityTasks`, we need to create a new Scoped Prediction Window with a new Scoped Prediction Key. This is sometimes referred to as a Synch Point between the client and server. Some `AbilityTasks` like all of the input related ones come with built-in functionality to create a new scoped prediction window, meaning atomic code in the `AbilityTasks'` callbacks have a valid scoped prediction key to use. Other tasks like the `WaitDelay` task do not have built-in code to create a new scoped prediction window for its callback. 
If you need to predict actions after an `AbilityTask` that does not have built-in code to create a scoped prediction window like `WaitDelay`, we must manually do that using the `WaitNetSync` `AbilityTask` with the option `OnlyServerWait`. 

When the client hits a `WaitNetSync` with `OnlyServerWait`, it generates a new scoped prediction key based on the `GameplayAbility's` activation prediction key, RPCs it to the server, and adds it to any new `GameplayEffects` that it applies. When the server hits a `WaitNetSync` with `OnlyServerWait`, it waits until it receives the new scoped prediction key from the client before continuing. This scoped prediction key does the same dance as activation prediction keys - applied to `GameplayEffects` and replicated back to clients to be marked stale. The scoped prediction key is valid until it falls out of scope, meaning the scoped prediction window has closed. So again, only atomic operations, nothing latent, can use the new scoped prediction key.

为了在`AbilityTasks`的回调中`预测(Predict)`更多的操作，我们需要创建一个具有新的`Scoped Prediction Key`的新的`Scoped Prediction Window`。这有时被称为客户端和服务器之间的同步点。一些`AbilityTasks`就像所有那些输入相关的东西那样，带有创建新`Scoped Prediction Window`的内置函数，这意味着`AbilityTasks`回调中的原子代码有一个有效的`Scoped Prediction Key`可以使用。其他的像`WaitDelay`这样的任务没有内置代码来为其回调函数创建新的`Scoped Prediction Window`。如果你需要在像`AbilityTask`（不像`WaitDelay`那样有创建`Scoped Prediction Window`的内置代码）的地方之后`预测（Predict）`操作，我们必须手动使用带有`OnlyServerWait`选项的`WaitNetSync` `AbilityTask`来做这件事。当客户端通过`OnlyServerWait`触发`WaitNetSync`时，


它会基于`GameplayAbility `的激活预测键生成一个新的作用域预测键，将其发送给服务器，并将其添加到它应用的任何新的`GameplayEffects`中。当服务器使用`OnlyServerWait`触发`WaitNetSync`时，它会一直等待，直到从客户端接收到新的作用域预测键才能继续。这个scoped预测键与激活预测键具有相同的功能-应用于`GameplayEffects`并复制回客户端以标记陈旧。范围预测键在超出范围之前是有效的，这意味着范围预测窗口已经关闭。因此，同样，只有原子操作，没有潜在的操作，可以使用新的作用域预测键。

You can create as many scoped prediction windows as you need.

If you would like to add the synch point functionality to your own custom `AbilityTasks`, look at how the input ones essentially inject the `WaitNetSync` `AbilityTask` code into them.

**Note:** When using `WaitNetSync`, this does block the server's `GameplayAbility` from continuing execution until it hears from the client. This could potentially be abused by malicious users who hack the game and intentionally delay sending their new scoped prediction key. While Epic uses the `WaitNetSync` sparingly, it recommends potentially building a new version of the `AbilityTask` with a delay that automatically continues without the client if this is a concern for you.

The Sample Project uses `WaitNetSync` in the Sprint `GameplayAbility` to create a new scoped prediction window every time we apply the stamina cost so that we can predict it. Ideally we want a valid prediction key when applying costs and cooldowns.

If you have a predicted `GameplayEffect` that is playing twice on the owning client, your prediction key is stale and you're experiencing the "redo" problem. You can usually solve this by putting a `WaitNetSync` `AbilityTask` with `OnlyServerWait` right before you apply the `GameplayEffect` to create a new scoped prediction key.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 Predictively Spawning Actors
Spawning `Actors` predictively on clients is an advanced topic. GAS does not provide functionality to handle this out of the box (the `SpawnActor` `AbilityTask` only spawns the `Actor` on the server). The key concept is to spawn a replicated `Actor` on both the client and the server.

If the `Actor` is just cosmetic or doesn't serve any gameplay purpose, the simple solution is to override the `Actor's` `IsNetRelevantFor()` function to restrict the server from replicating to the owning client. The owning client would have his locally spawned version and the server and other clients would have the server's replicated version.
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

If the spawned `Actor` affects gameplay like a projectile that needs to predict damage, then you need advanced logic that is outside of the scope of this documentation. Look at how UnrealTournament predictively spawns projectiles on Epic Games' GitHub. They have a dummy projectile spawned only on the owning client that synchs up with the server's replicated projectile.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 Future of Prediction in GAS
`GameplayPrediction.h` states in the future they could potentially add functionality for predicting `GameplayEffect` removal and periodic `GameplayEffects`.

Dave Ratti from Epic has [expressed interest](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) in fixing the `latency reconciliation` problem for predicting cooldowns, disadvantaging players with higher latencies versus players with lower latencies.

The new [`Network Prediction` plugin](#concepts-p-npp) by Epic is expected to be fully interoperable with the GAS like the `CharacterMovementComponent` *was* before it.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 Network Prediction Plugin
Epic recently started an initiative to replace the `CharacterMovementComponent` with a new `Network Prediction` plugin. This plugin is still in its very early stages but is available to very early access on the Unreal Engine GitHub. It's too soon to tell which future version of the Engine that it will make its experimental beta debut in.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 Targeting

<a name="concepts-targeting-data"></a>
#### 4.11.1 Target Data
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) is a generic structure for targeting data meant to be passed across the network. `TargetData` will typically hold `AActor`/`UObject` references, `FHitResults`, and other generic location/direction/origin information. However, you can subclass it to put essentially anything that you want inside of them as a simple means to [pass data between the client and server in `GameplayAbilities`](#concepts-ga-data). The base struct `FGameplayAbilityTargetData` is not meant to be used directly but instead subclassed. `GAS` comes with a few subclassed `FGameplayAbilityTargetData` structs out of the box located in `GameplayAbilityTargetTypes.h`.

`TargetData` is typically produced by [`Target Actors`](#concepts-targeting-actors) or **created manually** and consumed by [`AbilityTasks`](#concepts-at) and [`GameplayEffects`](#concepts-ge) via the [`EffectContext`](#concepts-ge-context). As a result of being in the `EffectContext`, [`Executions`](#concepts-ge-ec), [`MMCs`](#concepts-ge-mmc), [`GameplayCues`](#concepts-gc), and the functions on the backend of the [`AttributeSet`](#concepts-as) can access the `TargetData`.

We don't typically pass around the `FGameplayAbilityTargetData` directly, instead we use a [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html) which has an internal TArray of pointers to `FGameplayAbilityTargetData`. This intermediate struct provides support for polymorphism of the `TargetData`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 Target Actors
`GameplayAbilities` spawn [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) with the `WaitTargetData` `AbilityTask` to visualize and capture targeting information from the world. `TargetActors` may optionally use [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) to display current targets. Upon confirmation, the targeting information is returned as [`TargetData`](#concepts-targeting-data) which can then be passed into `GameplayEffects`.
 
`TargetActors` are based on `AActor` so they can have any kind of visible component to represent **where** and **how** they are targeting such as static meshes or decals. Static meshes may be used to visualize placement of an object that your character will build. Decals may be used to show an area of effect on the ground. The Sample Project uses [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) with a decal on the ground to represent the damage area of effect for the Meteor ability. They also don't need to display anything either. For example it wouldn't make sense to display anything for a hitscan gun that instantly traces a line to its target as used in [GASShooter](https://github.com/tranek/GASShooter).

They capture targeting information using basic traces or collision overlaps and convert the results as `FHitResults` or `AActor` arrays to `TargetData` depending on the `TargetActor` implementation. The `WaitTargetData` `AbilityTask` determines when the targets are confirmed through its `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` parameter. When **not** using `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, the `TargetActor` typically performs the trace/overlap on `Tick()` and updates its location to the `FHitResult` depending on its implementation. While this performs a trace/overlap on `Tick()`, it's generally not terrible since it's not replicated and you typically don't have more than one (although you could have more) `TargetActor` running at a time. Just be aware that it uses `Tick()` and some complex `TargetActors` might do a lot on it like the rocket launcher's secondary ability in GASShooter. While tracing on `Tick()` is very responsive to the client, you may consider lowering the tick rate on the `TargetActor` if the performance hit is too much. In the case of `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, the `TargetActor` immediately spawns, produces `TargetData`, and destroys. `Tick()` is never called. 

| `EGameplayTargetingConfirmation::Type` | When targets are confirmed                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | The targeting happens instantly without special logic or user input deciding when to 'fire'.                                                                                                                                                                                                                                                                   |
| `UserConfirmed`                        | The targeting happens when the user confirms the targeting when the [ability is bound to a `Confirm` input](#concepts-ga-input) or by calling `UAbilitySystemComponent::TargetConfirm()`. The `TargetActor` will also respond to a bound `Cancel` input or call to `UAbilitySystemComponent::TargetCancel()` to cancel targeting.                              |
| `Custom`                               | The GameplayTargeting Ability is responsible for deciding when the targeting data is ready by calling `UGameplayAbility::ConfirmTaskByInstanceName()`. The `TargetActor` will also respond to `UGameplayAbility::CancelTaskByInstanceName()` to cancel targeting.                                                                                              |
| `CustomMulti`                          | The GameplayTargeting Ability is responsible for deciding when the targeting data is ready by calling `UGameplayAbility::ConfirmTaskByInstanceName()`. The `TargetActor` will also respond to `UGameplayAbility::CancelTaskByInstanceName()` to cancel targeting. Should not end the `AbilityTask` upon data production.                                       |

Not every EGameplayTargetingConfirmation::Type is supported by every `TargetActor`. For example, `AGameplayAbilityTargetActor_GroundTrace` does not support `Instant` confirmation.

The `WaitTargetData` `AbilityTask` takes in a `AGameplayAbilityTargetActor` class as a parameter and will spawn an instance on each activation of the `AbilityTask` and will destroy the `TargetActor` when the `AbilityTask` ends. The `WaitTargetDataUsingActor` `AbilityTask` takes in an already spawned `TargetActor`, but still destroys it when the `AbilityTask` ends. Both of these `AbilityTasks` are inefficient in that they either spawn or require a newly spawned `TargetActor` for each use. They're great for prototyping, but in production you might explore optimizing it if you have cases where you are constantly producing `TargetData` like in the case of an automatic rifle. GASShooter has a custom subclass of [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) and a new [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask` written from scratch that allows you to reuse a `TargetActor` without destroying it.

`TargetActors` are not replicated by default; however, they can be made to replicate if that makes sense in your game to show other players where the local player is targeting. They do include default functionality to communicate with the server via RPCs on the `WaitTargetData` `AbilityTask`. If the `TargetActor`'s `ShouldProduceTargetDataOnServer` property is set to `false`, then the client will RPC its `TargetData` to the server on confirmation via `CallServerSetReplicatedTargetData()` in `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`. If `ShouldProduceTargetDataOnServer` is `true`, the client will send a generic confirm event, `EAbilityGenericReplicatedEvent::GenericConfirm`, RPC to the server in `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` and the server will do the trace or overlap check upon receiving the RPC to produce data on the server. If the client cancels the targeting, it will send a generic cancel event, `EAbilityGenericReplicatedEvent::GenericCancel`, RPC to the server in `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`. As you can see, there are a lot of delegates on both the `TargetActor` and the `WaitTargetData` `AbilityTask`. The `TargetActor` responds to inputs to produce and broadcast `TargetData` ready, confirm, or cancel delegates. `WaitTargetData` listens to the `TargetActor`'s `TargetData` ready, confirm, and cancel delegates and relays that information back to the `GameplayAbility` and to the server. If you send `TargetData` to the server, you may want to do validation on the server to make sure the `TargetData` looks reasonable to prevent cheating. Producing the `TargetData` directly on the server avoids this issue entirely, but will potentially lead to mispredictions for the owning client.

Depending on the particular subclass of `AGameplayAbilityTargetActor` that you use, different `ExposeOnSpawn` parameters will be exposed on the `WaitTargetData` `AbilityTask` node. Some common parameters include:

| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | If `true`, it will draw debug tracing/overlapping information whenever the `TargetActor` performs a trace in non-shipping builds. Remember, non-`Instant` `TargetActors` will perform a trace on `Tick()` so these debug draw calls will also happen on `Tick()`.                                                        |
| Filter                          | [Optional] A special struct for filtering out (removing) `Actors` from the targets when the trace/overlap happens. Typical use cases are to filter out the player's `Pawn`, require targets be of a specific class. See [Target Data Filters](#concepts-target-data-filters) for more advanced use cases. |
| Reticle Class                   | [Optional] Subclass of `AGameplayAbilityWorldReticle` that the `TargetActor` will spawn.                                                                                                                                                                                                                                 |
| Reticle Parameters              | [Optional] Configure your Reticles. See [Reticles](#concepts-targeting-reticles).                                                                                                                                                                                                                                        |
| Start Location                  | A special struct for where tracing should start from. Typically this will be the player's viewpoint, a weapon muzzle, or the `Pawn`'s location.                                                                                                                                                                          |

With the default `TargetActor` classes, `Actors` are only valid targets when they are directly in the trace/overlap. If they leave the trace/overlap (they move or you look away), they are no longer valid. If you want the `TargetActor` to remember the last valid target(s), you will need to add this functionality to a custom `TargetActor` class. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed). GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 Target Data Filters
Using both the `Make GameplayTargetDataFilter` and `Make Filter Handle` nodes, you can filter out the player's `Pawn` or select only a specific class. If you need more advanced filtering, you can subclass `FGameplayTargetDataFilter` and override the `FilterPassesForActor` function. 
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

However, this will not work directly into the `Wait Target Data` node as it requires a `FGameplayTargetDataFilterHandle`. A new custom `Make Filter Handle` must be made to accept the subclass:
```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 Gameplay Ability World Reticles
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`Reticles`) visualize **who** you are targeting when targeting with non-`Instant` confirmed [`TargetActors`](#concepts-targeting-actors). `TargetActors` are responsible for the spawn and destroy lifetimes for all `Reticles`. `Reticles` are `AActors` so they can use any kind of visual component for representation. A common implementation as seen in [GASShooter](https://github.com/tranek/GASShooter) is to use a `WidgetComponent` to display a UMG Widget in screen space (always facing the player's camera). `Reticles` do not know which `AActor` that they're on, but you could subclass in that functionality on a custom `TargetActor`. `TargetActors` will typically update the `Reticle`'s location to the target's location on every `Tick()`.

GASShooter uses `Reticles` to show locked-on targets for the rocket launcher's secondary ability's homing rockets. The red indicator on the enemy is the `Reticle`. The similar white image is the rocket launcher's crosshair.
![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` come with a handful of `BlueprintImplementableEvents` for designers (they're intended to be developed in Blueprints):

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles` can optionally use [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) provided by the `TargetActor` for configuration. The default struct only provides one variable `FVector AOEScale`. While you can technically subclass this struct, the `TargetActor` will only accept the base struct. It seems a little short-sighted to not allow this to be subclassed with default `TargetActors`. However, if you make your own custom `TargetActor`, you can provide your own custom reticle parameters struct and manually pass it to your subclass of `AGameplayAbilityWorldReticles` when you spawn them.

`Reticles` are not replicated by default, but can be made replicated if it makes sense for your game to show other players who the local player is targeting.

`Reticles` will only display on the current valid target with the default `TargetActors`. For example, if you're using a `AGameplayAbilityTargetActor_SingleLineTrace` to trace for a target, the `Reticle` will only appear when the enemy is directly in the trace path. If you look away, the enemy is no longer a valid target and the `Reticle` will disappear. If you want the `Reticle` to stay on the last valid target, you will want to customize your `TargetActor` to remember the last valid target and keep the `Reticle` on them. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed).  GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers Targeting
[`GameplayEffectContainers`](#concepts-ge-containers) come with an optional, efficient means of producing [`TargetData`](#concepts-targeting-data). This targeting takes places instantly when the `EffectContainer` is applied on the client and the server. It's more efficient than [`TargetActors`](#concepts-targeting-actors) because it runs on the CDO of the targeting object (no spawning and destroying of `Actors`), but it lacks player input, happens instantly without needing confirmation, cannot be canceled, and cannot send data from the client to the server (produces data on both). It works well for instant traces and collision overlaps. Epic's [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) includes two example types of targeting with its containers - target the ability owner and pull `TargetData` from an event. It also implements one in Blueprint to do instant sphere traces at some offset (set by child Blueprint classes) from the player. You can subclass `URPGTargetType` in C++ or Blueprint to make your own targeting types.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>
## 5. Commonly Implemented Abilities and Effects

<a name="cae-stun"></a>
### 5.1 Stun
Typically with stuns, we want to cancel all of a `Character's` active `GameplayAbilities`, prevent new `GameplayAbility` activations, and prevent movement throughout the duration of the stun. The Sample Project's Meteor `GameplayAbility` applies a stun on hit targets.

To cancel the target's active `GameplayAbilities`, we call `AbilitySystemComponent->CancelAbilities()` when the stun [`GameplayTag` is added](#concepts-gt-change).

To prevent new `GameplayAbilities` from activating while stunned, the `GameplayAbilities` are given the stun `GameplayTag` in their [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags).

To prevent movement while stunned, we override the `CharacterMovementComponent's` `GetMaxSpeed()` function to return 0 when the owner has the stun `GameplayTag`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 Sprint
The Sample Project provides an example of how to sprint - run faster while `Left Shift` is held down.

The faster movement is handled predictively by the `CharacterMovementComponent` by sending a flag over the network to the server. See `GDCharacterMovementComponent.h/cpp` for details.

The `GA` handles responding to the `Left Shift` input, tells the `CMC` to begin and stop sprinting, and to predictively charge stamina while `Left Shift` is pressed. See `GA_Sprint_BP` for details.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 Aim Down Sights
The Sample Project handles this the exact same way as sprinting but decreasing the movement speed instead of increasing it.

See `GDCharacterMovementComponent.h/cpp` for details on predictively decreasing the movement speed.

See `GA_AimDownSight_BP` for details on handling the input. There is no stamina cost for aiming down sights.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 Lifesteal
I handle lifesteal inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanLifesteal`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanLifesteal` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` [creates a dynamic `Instant` `GameplayEffect`](#concepts-ge-dynamic) with the amount of health to give as the modifier and applies it back to the `Source's` `ASC`.

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 Generating a Random Number on Client and Server
Sometimes you need to generate a "random" number inside of a `GameplayAbility` for things like bullet recoil or spread. The client and the server will both want to generate the same random numbers. To do this, we must set the `random seed` to be the same at the time of `GameplayAbility` activation. You will want to set the `random seed` each time you activate the `GameplayAbility` in case the client mispredicts activation and its random number sequence becomes out of synch with the server's.

| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. |
| Send a seed through an event payload when you activate the `GameplayAbility` | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind.                                                                                                                                                                     |

If your random deviation is small, most players won't notice that the sequence is the same every game and using the activation prediction key as the `random seed` should work for you. If you're doing something more complex that needs to be hacker proof, perhaps using a `Server Initiated` `GameplayAbility` would work better where the server can create the prediction key or generate the `random seed` to send via an event payload.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 Critical Hits
I handle critical hits inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanCrit`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanCrit` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` generates a random number corresponding to the critical hit chance (`Attribute` captured from the `Source`) and adds the critical hit damage (also an `Attribute` captured from the `Source`) if it succeeded. Since I don't predict damage, I don't have to worry about synchronizing the random number generators on the client and server since the `ExecutionCalculation` will only run on the server. If you tried to do this predictively using an `MMC` to do your damage calculation, you would have to get a reference to the `random seed` from the `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`.

See how [GASShooter](https://github.com/tranek/GASShooter) does headshots. It's the same concept except that it does not rely on a random number for chance and instead checks the `FHitResult` bone name.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target
Slow effects in Paragon did not stack. Each slow instance applied and kept track of their lifetimes as normal, but only the greatest magnitude slow effect actually affected the `Character`. GAS provides for this scenario out of the box with `AggregatorEvaluateMetaData`. See [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated) for details and implementation.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 Generate Target Data While Game is Paused
If you need to pause the game while waiting to generate [`TargetData`](#concepts-targeting-data) from a `WaitTargetData` `AbilityTask` from your player, I suggest instead of pausing to use `slomo 0`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 One Button Interaction System
[GASShooter](https://github.com/tranek/GASShooter) implements a one button interaction system where the player can press or hold 'E' to interact with interactable objects like reviving a player, opening a weapon chest, and opening or closing a sliding door.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>
## 6. Debugging GAS
Often when debugging GAS related issues, you want to know things like:
> * "What are the values of my attributes?"
> * "What gameplay tags do I have?"
> * "What gameplay effects do I currently have?"
> * "What abilities do I have granted, which ones are running, and which ones are blocked from activating?".

GAS comes with two techniques for answering these questions at runtime - [`showdebug abilitysystem`](#debugging-sd) and hooks in the [`GameplayDebugger`](#debugging-gd).

**Tip:** UE5 likes to optimize C++ code which makes it hard to debug some functions. You will encounter this rarely when tracing deep into your code. If setting your Visual Studio solution configuration to `DebugGame Editor` still prevents tracing code or inspecting variables, you can disable all optimizations by wrapping the optimized function with the `PRAGMA_DISABLE_OPTIMIZATION_ACTUAL` and `PRAGMA_ENABLE_OPTIMIZATION_ACTUAL` macros. This cannot be used on the plugin code unless you rebuild the plugin from source. This may or may not work on inline functions depending on what they do and where they are. Be sure to remove the macros when you're done debugging!

```c++
PRAGMA_DISABLE_OPTIMIZATION_ACTUAL
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
PRAGMA_ENABLE_OPTIMIZATION_ACTUAL
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
Type `showdebug abilitysystem` in the in-game console. This feature is split into three "pages". All three pages will show the `GameplayTags` that you currently have. Type `AbilitySystem.Debug.NextCategory` into the console to cycle between the pages.

The first page shows the `CurrentValue` of all of your `Attributes`:
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

The second page shows all of the `Duration` and `Infinite` `GameplayEffects` on you, their number of stacks, what `GameplayTags` they give, and what `Modifiers` they give.
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

The third page shows all of the `GameplayAbilities` that have been granted to you, whether they are currently running, whether they are blocked from activating, and the status of currently running `AbilityTasks`.
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

While you can cycle between targets with `PageUp` and `PageDown`, the pages will only show data for the `ASC` on your locally controlled `Character`. However, using `AbilitySystem.Debug.NextTarget` and `AbilitySystem.Debug.PrevTarget` will show data for other `ASCs`, but it will not update the top half of the debug information nor will it update the green targeting rectangular prism so there is no way to know which `ASC` is currently being targeted. This bug has been reported https://issues.unrealengine.com/issue/UE-90437.

**Note:** For `showdebug abilitysystem` to work an actual HUD class must be selected in the GameMode. Otherwise the command is not found and "Unknown Command" is returned.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GAS adds functionality to the Gameplay Debugger. Access the Gameplay Debugger with the Apostrophe (') key. Enable the Abilities category by pressing 3 on your numpad. The category may be different depending on what plugins you have. If your keyboard doesn't have a numpad like a laptop, then you can change the keybindings in the project settings.

Use the Gameplay Debugger when you want to see the `GameplayTags`, `GameplayEffects`, and `GameplayAbilities` on **other** `Characters`. Unfortunately it does not show the `CurrentValue` of the target's `Attributes`. It will target whatever `Character` is in the center of your screen. You can change targets by selecting them in the World Outliner in the Editor or by looking at a different `Character` and press Apostrophe (') again. The currently inspected `Character` has the largest red circle above it.

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS Logging
The GAS source code contains a lot of logging statements produced at varying verbosity levels. You will most likely see these as `ABILITY_LOG()` statements. The default verbosity level is `Display`. Anything higher will not be displayed in the console by default.

To change the verbosity level of a log category, type into your console:

```
log [category] [verbosity]
```

For example, to turn on `ABILITY_LOG()` statements, you would type into your console:
```
log LogAbilitySystem VeryVerbose
```

To reset it back to default, type:
```
log LogAbilitySystem Display
```

To display all log categories, type:
```
log list
```

Notable GAS related logging categories:

| Logging Category          | Default Verbosity Level |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

See the [Wiki on Logging](https://unrealcommunity.wiki/logging-lgpidy6i) for more information.

**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>
## 7. Optimizations

<a name="optimizations-abilitybatching"></a>
### 7.1 Ability Batching
[`GameplayAbilities`](#concepts-ga) that activate, optionally send `TargetData` to the server, and end all in one frame can be [batched to condense two-three RPCs into one RPC](#concepts-ga-batching). These types of abilities are commonly used for hitscan guns.

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 Gameplay Cue Batching
If you're sending many [`GameplayCues`](#concepts-gc) at the same time, consider [batching them into one RPC](#concepts-gc-batching). The goal is to reduce the number of RPCs (`GameplayCues` are unreliable NetMulticasts) and send as little data as possible.

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent Replication Mode
By default, the [`ASC`](#concepts-asc) is in [`Full Replication Mode`](#concepts-asc-rm). This will replicate all [`GameplayEffects`](#concepts-ge) to every client (which is fine for a single player game). In a multiplayer game, set the player owned `ASCs` to `Mixed Replication Mode` and AI controlled characters to `Minimal Replication Mode`. This will replicate `GEs` applied on a player character to only replicate to the owner of that character and `GEs` applied on AI controlled characters will never replicate `GEs` to clients. [`GameplayTags`](#concepts-gt) will still replicate and [`GameplayCues`](#concepts-gc) will still unreliable NetMulticast to all clients, regardless of the `Replication Mode`. This will cut down on network data from `GEs` being replicated when all clients don't need to see them.

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 Attribute Proxy Replication
In large games with many players like Fortnite Battle Royale (FNBR), there will be a lot of [`ASCs`](#concepts-asc) living on always-relevant `PlayerStates` replicating a lot of [`Attributes`](#concepts-a). To optimize this bottleneck, Fortnite disables the `ASC` and its [`AttributeSets`](#concepts-as) from replicating altogether on **simulated player-controlled proxies** in the `PlayerState::ReplicateSubobjects()`. Autonomous proxies and AI controlled `Pawns` still fully replicate according to their [`Replication Mode`](#concepts-asc-rm). Instead of replicating `Attributes` on the `ASC` on the always-relevant `PlayerStates`, FNBR uses a replicated proxy structure on the player's `Pawn`. When `Attributes` change on the server's `ASC`, they are changed on the proxy struct too. The client receives the replicated `Attributes` from the proxy struct and pushes the changes back into its local `ASC`. This allows `Attribute` replication to use the `Pawn`'s relevancy and `NetUpdateFrequency`. This proxy struct also replicates a small white-listed set of `GameplayTags` in a bitmask. This optimization reduces the amount of data over the network and allows us to take advantage of pawn relevancy. AI controlled `Pawns` have their `ASC` on the `Pawn` which already uses its relevancy so this optimization is not needed for them.

> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.

*Dave Ratti from Epic's answer to [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC Lazy Loading
Fortnite Battle Royale (FNBR) has a lot of damageable `AActors` (trees, buildings, etc) in the world, each with an [`ASC`](#concepts-asc). This can add up in memory cost. FNBR optimizes this by lazily loading `ASCs` only when they're needed (when they first take damage by a player). This reduces overall memory usage since some `AActors` may never be damaged in a match.

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>
## 8. Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 Gameplay Effect Containers
[GameplayEffectContainers](#concepts-ge-containers) combine [`GameplayEffectSpecs`](#concepts-ge-spec), [`TargetData`](#concepts-targeting-data), [simple targeting](#concepts-targeting-containers), and related functionality into easy to use structures. These are great for transfering `GameplayEffectSpecs` to projectiles spawned from an ability that will then apply them on collision at a later time.

<a name="qol-asynctasksascdelegates"></a>
### 8.2 Blueprint AsyncTasks to Bind to ASC Delegates
To increase designer-friendly iteration times, especially when designing UMG Widgets for UI, create Blueprint AsyncTasks (in C++) to bind to the common change delegates on the `ASC` directly from your UMG Blueprint graphs. The only caveat is that they must be manually destroyed (like when the widget is destroyed) otherwise they will live in memory forever. The Sample Project includes three Blueprint AsyncTasks.

Listen for `Attribute` changes:

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

Listen for cooldown changes:

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

Listen for `GE` stack changes:

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. Troubleshooting

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
You need to [initialize the `ASC` on the client](#concepts-asc-setup).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` errors
You need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 Animation Montages are not replicating to clients
Make sure that you're using the `PlayMontageAndWait` Blueprint node instead of `PlayMontage` in your [GameplayAbilities](#concepts-ga). This [AbilityTask](#concepts-at) replicates the montage through the `ASC` automatically whereas the `PlayMontage` node does not.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 Duplicating Blueprint Actors is setting AttributeSets to nullptr
There is a [bug in Unreal Engine](https://issues.unrealengine.com/issue/UE-81109) that will set `AttributeSet` pointers on your classes to nullptr for Blueprint Actor classes that are duplicated from existing Blueprint Actor classes. There are a few workarounds for this. I've had success not creating bespoke `AttributeSet` pointers on my classes (no pointer in the .h, not calling `CreateDefaultSubobject` in the constructor) and instead just directly adding `AttributeSets` to the `ASC` in `PostInitializeComponents()` (not shown in the Sample Project). The replicated `AttributeSets` will still live in the `ASC's` `SpawnedAttributes` array. It would look something like this:

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... any other AttributeSets that you may have
	}
}
```

In this scenario, you would read and set the values in the `AttributeSet` using the functions on the `ASC` instead of [calling functions on the `AttributeSet` made from the macros](#concepts-as-attributes).

```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

So the `GetHealth()` would look something like:

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

Setting (initializing) the health `Attribute` would look something like:

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

As a reminder, the `ASC` only ever expects at most one `AttributeSet` object per `AttributeSet` class.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>
### 9.5 unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)

If you get a compiler error like:

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

This is from trying to call `MarkItemDirty()` on a `FFastArraySerializer`. I've encountered this from updating an `ActiveGameplayEffect` such as when updating the cooldown duration.

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

What's happening is that `WITH_PUSH_MODEL` is getting defined in more than one place. `PushModelMacros.h` is defining it as 0 while it's defined as 1 in multiple places. `PushModel.h` is seeing it as 1 but `PushModel.cpp` is seeing it as 0.

The solution is to add `NetCore` to your project's `PublicDependencyModuleNames` in the `Build.cs`.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 10. Common GAS Acronyms

| Name                                                                                                   | Acronyms            |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>
## 11. Other Resources
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Lyra Sample Project by Epic](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-ability-system`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>
#### 11.1.1 Community Questions 1
[Dave Ratti responses to the Unreal Slackers Discord Server community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. How can we create scoped prediction windows on demand outside or irrespective of GameplayAbilities? For example, how can a fire and forget projectile locally predict a damage GameplayEffectwhen it hits an enemy?

> The PredictionKey system is not really meant to do this. Fundamentally this systems works by a client initiating a predictive action, telling the server about it with a key, and then both client and server running the same thing and associating predictive side effects with the given prediction key. For example, “I am predictively activating an ability” or “I have produced target data and am going to predictively run the part of the ability graph after the WaitTargetData task”.
>
> With this pattern, the PredictionKey “bounces” off the server and comes back to the client via UAbilitySystemComponent::ReplicatedPredictionKeyMap (replicated property). Once the key is replicated back from the server, the client is able to undo all of the locally predictive side effects (GameplayCues, GameplayEffects): the replicated versions *will be there* and if they aren’t then it was a misprediction. Knowing exactly when to undo the predictive side effects is crucial here: if you are too early you will see gaps, if you are too late you will have “double”. (Note this is referring to stateful prediction, like a looping GameplayCue of a duration based Gameplay Effect. “Burst” GameplayCues and instant Gameplay Effects are never “undone” or rolled back. They are just skipped on the client if there is a prediction key associated with them).
>
> To further hit home the point: it’s crucial that predictive action is something the server does not do on their own, but only does so when the client tells them too. So having a generic “Create a key on demand and tell the server so I can run something” does not work unless that “something” is something the server will only do once told to by the client.
>
> Backing up to the original question: something like a fire and forget projectile. Both Paragon and Fornite have projectile actor classes that use GameplayCues. However we do not use the Prediction Key system to do these. Instead we have a concept on Non-Replicated GameplayCues. GameplayCues that just fire off locally and are skipped by the server completely.  Essentially all these are, are direct calls to UGameplayCueManager::HandleGameplayCue. They do not route through the UAbilitySystemComponent so no prediction key checks / early returns are made.
>
> The downside with non replicated GameplayCues is that, well, they are not replicated. So its up to the projectile class/blueprint to make sure the code paths that call these functions are running on everyone. We have for cues startup (called in BeginPlay), explosion, hit wall/character, etc.
>
> These type ofevents are already generated client side, so calling into a non replicated gameplay cue was no big deal. Complicated blueprints can be tricky, and are up to the author to make sure they understand what is running where.


2. When using a WaitNetSyncAbilityTaskwith OnlyServerWaitto create a scoped prediction window in a locally predicted GameplayAbility, could players potentially cheat by delaying their packets to the Server to control GameplayAbilitytiming since the Server is waiting for their RPC withtheir prediction key? Was this ever an issue in Paragon or Fortnite, and if so, what did Epic do to remedy it?

> Yes, this is a valid concern. Any ability blueprint running on the server that is waiting for a client “signal” is potentially vulnerable to lag switch type exploits.
>
> Paragon had a custom targeting task similar to UAbilityTask_WaitTargetData. In this task we had timeouts, or a “max delay” that we would wait on the client for instantaneous targeting modes. If the targeting mode was waiting for user confirmation (button press) then it would be ignored since the user is allowed to take his time. But for abilities that instantly confirmed targeting we would only wait a certain amount of time before either A) generating the target data server side of B) canceling the ability.
>
> We never had such mechanisms for WaitNetSync, which we used pretty sparingly.
>
> I don’t believe Fortnite makes use of anything like this though. The weapon abilities in Fortnite are special cased batched to a single fortnite-specific RPC: one RPC to activate the ability, provide target data, and end the ability. So weapon abilities are intrinsically not vulnerable to this in Battle Royale.
>
> My take is that this is something that could probably be solved system wide but I don’t see us making the change ourselves anytime soon. Spot fixing WaitNetSync to include a max delay for the case you mention is probably a reasonable task, but again - unlikely we will do this on our end in the immediate future.


3. Which EGameplayEffectReplicationModedid Paragon and Fortnite use and what are Epic’s recommendations for when to use each?

> Both games essentially use Mixed mode for their player controlled characters and Minimal for AI controlled (AI minions, jungle creeps, AIHusks, etc). This is what I would recommend most people using the system in a multiplayer game. The sooner into your project you set these, the better.
>
> Fortnite goes a few steps further with its optimizations. It actually does not replicate the UAbilitySystemComponent at all for simulated proxies. The component and attribute subobjects are skipped inside ::ReplicateSubobjects() on the owning fortnite player state class. We do push the bare minimum replicated data from the ability system component to a structure on the pawn itself (basically, a subset of attribute values and a white list subset of tags that we replicate down in a bitmask). We call this a “proxy”. On the receiving side we take the proxy data, replicated on the pawn, and push it back into ability system component on the player state. So you do have an ASC for each player in FNBR, it just doesn’t directly replicate: instead it replicates data via a minimal proxy struct on the pawn and then routes back to the ASC on receiving side. This is advantage since its A) a more minimal set of data B) takes advantage of pawn relevancy.
>
> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.


4. Since we cannot predict the removal of GameplayEffectsas per GameplayPrediction.h, are there any strategies for mitigating the effects of latency on removing GameplayEffects? For example, when removing a movement speed slow, we currently have to wait for the Server to replicate the GameplayEffectremoval resulting in a snap of the player’s character position.

> This is a tough one and I don’t have a good answer. We generally skirted around these problems with tolerances and smoothing. I totally agree that ability system and precise synchronization with the character movement system is not in a good place and something we do want to fix.
>
> I had a shelf of allowing predictive removal of GEs but could never work out all edge cases before having to move on. This doesn’t solve everything though since character movement still has an internal saved move buffer that does not know anything about the ability system and possible movement speed modifiers, etc. It is still possible to get into correction feedback loops even outside of not being able to predict the removal of GEs.
>
> If you think you have a case that is truly desperate, you are able to predictively add a GE that would inhibit your movement speed GEs. I’ve never done this myself but have theorized about it before. It may be able to help with a certain class of problem.


5. We know that the AbilitySystemComponentlives on the PlayerStatein Paragon and Fortnite and on the Characterin the Action RPG Sample. What are Epic’s internal rules, guidelines, or recommendations for where the AbilitySystemComponentshould live --what should its Ownerbe?

> In general I would say anything that does not need to respawn should have the Owner andAvatar actor be the same thing. Anything like AI enemies, buildings, world props, etc.
>
> Anything that does respawn should have the Owner and Avatar be different so that the Ability System Component does not need to be saved off / recreated / restored after a respawn.. PlayerState is the logical choice it is replicated to all clients (where as PlayerController is not). The downside is PlayerStates are always relevant so you can run into problems in 100 player games. (See notes on what FN did in question #3).


6. Is it viable to have several AbilitySystemComponentswhich have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Ownerset to PlayerState)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out - HasAlMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE - which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


7. Is there a way to stop the Server from overwriting the cooldown duration of locally predicted abilities on the Owning Client? In scenarios of high latency, this would let theOwning Client "try" to activate the ability again when its local cooldown expires but it is still on cooldown on the Server. By the time the Owning Client's activation request reaches the Server over the network, the Server may be off cooldown or the Server might be able to queue the activation request for the remaining milliseconds that it has left. Otherwise as is, clients with higher latency have a longer delay before when they can reactivate an ability versus those with less latency. This is most apparent with very low cooldown abilities like a basic attack that can be less than one second of cooldown. If there isn't a way to stop the Server from overwriting the cooldown duration of locally predicted abilities, what is Epic's strategy for mitigating theeffects of high latency on reactivating abilities? To word it another example-based way, how did Epic design Paragon's basic attacks and other abilities so that high latency players could attack or activate at the same speed as low latency players with local prediction?

> The short answer there is not a way to prevent this and Paragon definitely had the problem. Higher latency connections would have a lower ROF with basic attacks.
>
> I attempted to fix this by adding “GE reconciliation” where latency was taken into account when calculating GE duration. Essentially allowing the server to eat some of the total GE time so that the effective time of the GE client side would be 100% consistent with any amount of latency (though fluctuations could still cause issues). However I never got this working in a state that could ship and the project moved fast and we just never fully addressed it.
>
> Fortnite does its own bookkeeping for weapon firing rates: it does not use GEs for cooldowns on weapons. I would recommend this if this is a critical problem for your game.


8. What is Epic’s roadmap for the GameplayAbilitySystem plugin? Which features does Epic plan to add in 2019 and beyond?

> We feel that overall the system is pretty stable at this point and we don’t have anyone working on major new features. Bug fixes and small improvements occasionally are made for Fortnite or from UDN/pull requests, but that is it right now.
>
> Longer term, I think we will eventually do a “V2” or some big changes. We learned a lot from writing this system and feel we got a lot right and a lot wrong. I would love a chance to correct those mistakes and improve some of the fatal flaws that were pointed out above.
>
> If a V2 was to ever come, providing an upgrade path would be of utmost importance. We would never make a V2 and leave Fortnite on V1 forever: there would be some path or procedures that would automatically migrate as much as possible, though there would still almost certainly be some manual remaking required.
>
> The high priority fixes would be:
> * Better interoperability with the character movement system. Unifying client prediction.
> * GE removal prediction (question #4)
> * GE latency reconciliation (question #8)
> * Generalized network optimizations such as batching RPCs and proxy structures. Mostly the stuff that we’ve done for Fortnite but find ways to break it down into more generalized form, at least so that games can write their own game specific optimizations more easily.
>
> The more general refactor type of changes I would consider making:
> * I would like to look at fundamentally moving away from having GEs reference spreadsheet values directly, instead they would be able to emit parameters and those parameters could be filled by some higher level object that is bound to spreadsheet values. The problem with the current model is that GEs become unsharable due to their tight coupling with the curve table rows. I think a generalized system for parameterization could be written and be the underpinning of a V2 system.
> * Reduce number of “policies” on UGameplayAbility. I would remove ReplicationPolicy InstancingPolicy. Replication is, imo, almost never actually needed and causes confusion. InstancingPolicy should be replaced instead by making FGameplayAbilitySpec a UObject that can be subclassed. This should have been the “non instantiated ability object” that has events and is blueprintable. The UGameplayAbility should be the “instanced per execution” object. It could be optional if you need to actually instantiate: instead “non instanced” abilities would be implemented via the new UGameplayAbilitySpec object. 
> * The system should provide more “middle level” constructs such as “filtered GE application container” (data drive what GEs to apply to which actors with higher level gameplay logic), “Overlapping volume support” (apply the “Filtered GE application container” based on collision primitive overlap events), etc.These are building blocks that every project ends up implementing in their own way. Getting them right is non trivial so I think we should do a better job providing some basic implementations. 
> * In general, reducing boilerplate needed to get your project up and running. Possibly a separate module “Ex library” or whatever that could provide things like passive abilities or basichitscan weapons out of the box. This module would be optional but would get you up and running quickly.
> * I would like to move GameplayCues to a separate module that is not coupled with the ability system. I think there are a lot of improvements that could be made here.


> This is only my personal opinion and not a commitment from anyone. I think the most realistic course of action will be as new engine tech initiatives come through, the ability system will need to be updated and that will be a time to do this sort of thing. These initiatives could be related to scripting, networking, or physics/character movement. This is all very far looking ahead though so I cannot give commitments or estimates on timelines.

**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned ? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System ? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys) ?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require. 
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch ? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on Main there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it ? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information. 


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort ?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems ? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own ?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded) 
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (E.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code. 
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system. 
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use. 

**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-4.27"></a>
### 4.27
* Crash Fix: Fixed a root motion source issue where a networked client could crash when an Actor finishes executing an ability that uses a constant force root motion task with a strength-over-time modifier.
* Bug Fix: Fixed a regression in Editor loading time when using GameplayCues.
* Bug Fix: GameplayEffectsContainer's `SetActiveGameplayEffectLevel` method will no longer dirty FastArray if setting the same EffectLevel.
* Bug Fix: Fixed an edge case in GameplayEffect mixed replication mode where Actors not explicitly owned by the net connection but who utilize that connection from `GetNetConnection` will not received mixed replication updates.
* Bug Fix: Fixed an endless recursion occuring in GameplayAbility's class method `EndAbility` which was called by calling `EndAbility` again from `K2_OnEndAbility`.
* Bug Fix: GameplayTags Blueprint pins will no longer be silently cleared if they are loaded before tags are registered. They now work the same as GameplayTag variables, and the behavior for both can be changed with the ClearInvalidTags option in the Project Settings.
* Bug Fix: Improved thread safety of GameplayTag operations.
* New: Exposed SourceObject to GameplayAbility's `K2_CanActivateAbility` method.
* New: Native GameplayTags. Introducing a new `FNativeGameplayTag`, these make it possible to do one off native tags that are correctly registered and unregistered when the module is loaded and unloaded.
* New: Updated `GiveAbilityAndActivateOnce` to pass in FGameplayEventData parameter.
* New: Improved ScalableFloats in the GameplayAbilities plugin to support dynamic lookup of curve tables from the new Data Registry System. Added a ScalableFloat header for easier reuse of the generic struct outside the abilities plugin.
* New: Added code support for using the GameplayTag UI in other Editor customizations via GameplayTagsEditorModule.
* New: Modified UGameplayAbility's PreActivate method to optionally take in trigger event data.
* New: Added more support to filter GameplayTags in the Editor using a project-specific filter. `OnFilterGameplayTag` supplies the referencing property and the tag source, so you can filter tags based on what asset is requesting the tag.
* New: Added option to preserve the original captured SourceTags when GameplayEffectSpec's class method `SetContext` is called after initialization.
* New: Improved UI for registering GameplayTags from specific plugins. The new tag UI now lets you select a plugin location on disk for newly added GameplayTag sources.
* New: A new track has been added to Sequencer to allow for triggering notify states on Actors built using the GameplayAbiltiySystem. Like notifies, the GameplayCueTrack can utilize range-based events or trigger-based events.
* Change: Changed the GameplayCueInterface to pass GameplayCueParameters struct by reference.
* Optimization: Made several performance improvements to loading and regenerating the GameplayTag table were implemented so that this option would be optimized.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
   * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
   * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ Back to Top](#table-of-contents)**
