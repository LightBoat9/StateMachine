# State Machine
Simple state machine for Godot Engine written in GDScript

Developer: [Luke Sloop](https://github.com/LightBoat9)

See: [Godot Engine](https://godotengine.org/) 

## Getting Started
This script functions as a state machine superclass that allows inheriting subclasses to function as state machines. First add the `state_machine.gd` file to your project folder. Then simply extend the script you want to function as a state machine from `state_machine.gd`

```
extends "path/to/state_machine.gd"
```
For example in one of my projects
```
extends "res://Super/state_machine.gd"
```
See: [Godot Inheritance](https://godot.readthedocs.io/en/stable/learning/scripting/gdscript/gdscript_basics.html#inheritance)

## Reference
### **Instance Methods**

- **void set_current_state ( str new_state )**

Sets the current state to `new_state`, this will call the `[new_state]_enter` function and the `[last_state]_exit` function.

- **str get_current_state ( ) **

Returns the current state of the state machine

- **str get_last_state ( )**

Returns the previous state to the current state

## Entering States
By default the current state is `null`. So to change state call `set_current_state` in the StateMachine script. 

- **Note**: It is not recommended to call `set_current_state` from an external script as this breaks some of the functionality of a finite state machine

## Events
There are several events that each state has.
- **_enter ( )** - when the state is first set
- **_exit ( )** - when the `current_state` is changed
- **_update ( )** - called every `process` call
- **_fixed_update ( )** - called every `fixed_process` call
- **_input ( InputEvent event )** - called every `input` call

These names are concatenated to the end of the state name. So for an `idle` state functions can be created with the names `idle_enter`, `idle_update`, `idle_input` etc.

There are also global events, that are called no matter what the `current_state` is.
- **global_enter ( )** - called before the new state's `_enter` function
- **global_exit ( )** - called before the old state's `_exit` function
- **global_pre_update ( )** - called before the current state's `_update` function
- **global_post_update ( )** - called after the current state's `_update` function
- **global_pre_fixed_update ( )** - called before the current state's `_fixed_update` function
- **global_post_fixed_update ( )** - called after the current state's `_fixed_update` function
- **global_input ( InputEvent event )** - called before the current state's `_input` function, takes an `event` parameter

## Exit Conditions
Each state should have exit conditions in one of the events. The exit condition tells the state when it should enter another state. Use caution when changing states exernally because this breaks some of the uses of a state machine and in most cases should be avoided. One simple example of an exit condition for an `idle` state is the recieving movement inputs.
```
func idle_input(event)
    if event.is_action_pressed("ui_walk"):
        set_current_state("walk")
```
This will call `idle_exit` and `walk_enter`. Then from walk `idle` might be set again when colliding.
```
func walk_update():
    if is_colliding():
        set_current_state("idle")
```

## Simple Example
The extending state machine should end up looking something like this **pseudocode**.
```
extends "res://StateMachine.gd"

func _ready():
    set_current_state("idle")
    
func global_update():
    check_for_collisions()    

func idle_enter():
    set_animation("idle")
    
func idle_update():
    if is_moving():
        set_current_state("move")
        
func move_enter():
    set_animation("move")
    
func move_fixed_update():
    if not is_moving():
         set_current_state("idle")
         return
        
    move(velocity)
```
