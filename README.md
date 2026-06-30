###  How to make interactable objects in Godot


## Introduction
Have you ever wondered how you can go up to a chest in Minecraft and press “E” to open it? Or how, when someone dies right next to you, you just pick up all their items by walking into them. Well, wonder no more because I’ll be showing you how those systems work in this project tutorial!

To start, I’m gonna discuss the various elements that we are gonna be using in this tutorial.

StaticBody2D - An element you can use to create barriers, walls, and any type of collision handling. Basically, it doesn't let the characters or any other objects pass through it.
Area2D - This is similar to StaticBody2D, but is used for detecting collisions, signals, and more. In more fun terms, you could blow up a player character who carelessly walks onto a mine.
CollisionShape2D - This is what actually provides form and shape to Area2D and StaticBody2D, and is the most crucial piece to this tutorial.

With that being said, we can move on to how to use these ingredients to make our game come alive!


## Prerequisites
Godot game engine downloaded, download here: https://godotengine.org/
Player character made using CharacterBody2D, tutorial: https://www.youtube.com/watch?v=3M3olvGE-EI
A simple background image and an object that you can drag and drop into your Godot scene


##Getting Started
Now, in your Godot, you want to make sure that your player character has a CollisionShape2D attached to it because that is the most crucial piece to being able to make interactions happen. Here’s my character as an example:

<img width="512" height="265" alt="boyGhost" src="https://github.com/user-attachments/assets/e5e066ca-4ad1-47d6-b00a-3454eb98165b" />

## Collision shapes
Now, we will discuss collision handling. 
In your game, add a StaticBody2D node to all the items that require a collision/barrier so the character can’t just walk on it. Once you’ve added that, attach a CollisionShape2D node to the staticBody2D. It should look something like this:

<img width="301" height="81" alt="staticbody" src="https://github.com/user-attachments/assets/786704c3-378a-46e9-8604-e785bc6db99f" />

Once you have this, go to the inspector on the right side of the screen, and select what kind of shape you want from the dropdown:

<img width="293" height="512" alt="collisionshjape" src="https://github.com/user-attachments/assets/db1d009d-168a-404e-8230-e4e2a0d94a5f" />

From here you can drag the shape and place it onto your object, change its shape and size, and repeat this process for all the objects you need collisions for. Here is what the game looks like after adding all the collisions:

<img width="512" height="336" alt="barriercoll" src="https://github.com/user-attachments/assets/f5640553-3f0d-4548-b6b5-ec98c99e85f6" />

Getting the placement of the collision shapes to feel just right is a very trial and error heavy process, so I suggest you take your time and experiment with the placement and sizing of your collision shapes to get them just right. Now, if you try playing your game, you will not be able to walk into these objects anymore, and will have to walk around them!

## adding interactivity
Now lets move onto the more fun part, adding the ability to interact with objects in your game! To start off you want to add an Area2D node to each of the objects in your game that you specifically want some type of interaction with like being able to click on them, or run into them and trigger something. Once you have that add a collision shape to them, but make sure that these collision shapes are larger than the ones for the static body because these ones you will need your character to enter in order to make the interaction happen!

Here’s my game for example, as you can see these collision shapes are much larger than the ones for the StaticBody2D:

<img width="512" height="333" alt="interactcoll" src="https://github.com/user-attachments/assets/f7408b36-306f-4f93-8540-148bd0ae51f8" />

## Code!
Now that everything is set up, we can move onto the brain of the interaction: Code!

Pick one of the objects, and attach an empty script to its Area2D. Put these lines of code in it:

```
extends Area2D
signal clicked(text)

@export var label_text: String = ""

var player_inside := false
```

These are just variables and some essential signals that need to be setup prior, so we can use them later on in the code.

```
func _ready():
	body_entered.connect(_on_body_entered)
	body_exited.connect(_on_body_exited)

func _on_body_entered(body):
	if body.name == "blobGhostPlayer":
		player_inside = true
		$Node2D/EAnim.play("E")
```

These lines of code are very important. The ```_on_body_entered``` function receives a signal when a body enters that Area2D node (meaning it enters the collision shape attached to the Area2D node) and triggers the code inside the function. In this case, the function actually triggers the code above in _ready which connects our player character’s body to the ```_on_body_entered``` function, so it can get information about the character like its name. Which is then used inside _on_body_entered in line 13, as it verifies if it was the player character that entered the body. If this condition is passed, the variable, player_inside is set to true, and an animation I made is played!

<img width="512" height="216" alt="EEEE" src="https://github.com/user-attachments/assets/3df32046-c990-489b-8ece-1ce9b67e1217" />

Isn’t that cool? With just a few lines of code you can set up the interaction! Now, we can’t forget about what happens when you leave the Area2D, so that’s what this next bit of code handles:

```
func _on_body_exited(body):
	if body.name == "blobGhostPlayer":
		player_inside = false
		$Node2D/EAnim.stop()
		$Node2D/EAnim/letterE.visible = false
```

This function is very similar to the``` _on_body_entered``` we saw earlier, except now it's checking for the player character exiting the Area2D. Here, the player_inside variable gets set to false, the animation gets stopped and hidden.

```
func _process(delta: float) -> void:
	if player_inside and Input.is_action_pressed("interact"):
		print("interact pressed on area")
		$"../Label".visible=true
		$"../../Box2/Label".visible=false
		$"../../Box7/Label".visible=false
		$"../../Bloodblanket/Label".visible=false
```

Moving on, we have the final bit of code. This is actually really cool because not only do we have an event triggering just for walking into the Area2D, but now we are adding an additional level of interaction to that! Now we are checking for two new conditions, which are the player_inside variable that we have been turning on and off whenever we enter and exit our Area2D, and we are checking for an input. In this case, I’ve set up the input to be the letter “E” being pressed on the keyboard, but you can make it whatever you want! Now, when I go into an object's Area2D and press “E”, I get to see a description about that object in the game!

If you are curious about how to set up that input, here are the steps:
1. Click on the project in the top bar, and then go to project settings
2. Go to the input map, give your input a name in the “Add new action” field, and then press Add. The name I gave was “interact”
3. Your new input should show up under that field, click the plus to the right of your new input
4. The input you want should show up as you press that key, and then click ok
5. Voilà, your new input is ready to be used when you reference the name in your code!

<img width="512" height="214" alt="textdisp" src="https://github.com/user-attachments/assets/cfe3ba02-1939-4168-a729-b86aef3e86e1" />

## Conclusion

To summarize, we used StaticBody2D to create physical barriers, we used Area2D for receiving and emitting signals, and we looked at how these signals could be used to create different interactions in our game!

Pretty cool, huh? Well, this is only the tip of the iceberg, and you can do so much more with this. Want an example? I made a [hide-and-seek game](https://aadityananbumani.itch.io/) that checked if the character was in multiple different collision shapes at once and crouching in order to stay hidden and not get caught.
Therefore, go out there and try different things; this is one of my favorite things in game development because it is so versatile and you can do so many things with it!

Thanks for your time!
