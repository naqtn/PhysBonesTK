# PhysBonesTK readme

PhysBonesTK is a Tuning Kit (or Tool Kit or Tutorial Kit?) for VRChat Avatar Dynamics PhysBones.
You can tune PhysBone parameters at runtime (i.e. in-game).


## Demo avatars / Basic instruction

Demo avatars are available in this world.
https://vrchat.com/home/launch?worldId=wrld_1935fd5a-ff63-4cfa-92c9-21f22f850e74

You can change PhysBone parameters via Expression Menu.

The bones controlled by PhysBone are on right arm of demo avatars.
(So, if you are in desktop mode, pickup something in the world to test.)


Menus:
- "Example Configs": for loading example preset parameter configs
- "Movement parameters": for physical parameters of PhysBone. (Pull, Spring, etc. )
- "Interaction parameters": for interaction parameters of PhysBone. (CollisionRadius, AllowGrabbing, etc.)
- "Move/Scale": for control item placement, etc. (only available "accessory item" usage)


Parameter values:
Followings are the relation of value of animation parameters and actual parameter value of PhysBone.
(so you can setup your PhysBone directly after tuning them with PhysBonesTK.)

- basically liner. 0% => 0, 50% => 0.5, 100% => 1.0
- "Gravity":
  - if "Narrow Range" is off: 0% => 0, 100% => 1.0 
  - if "Narrow Range" is on:  0% => 0, 100% => 0.1 
  - if "Negative" is on: negative value of aboves.
- "Max Angle":
  - if Narrow is off: 0% => 0, 100% => 180
  - if Narrow is on: 0% => 0, 100% => 10
- "FreezeAxis Angle": 0% => 0, 100% => 180
- "CollisionRadius": 0% => 0, 100% => 0.1 (meaning 10cm if Transform Scale is 1.0)
- "MaxStretch":  0% => 0, 100% => 5.0


## How to install PhysBonesTK to your avatar

PhysBoneTK has tow types of usage:
1. For avatar body bone (PhysBone drives a part of avatar bones)
2. For avatar accessory item (PhysBone drives bone of separated item bone)

The latter has additional features, for instance,
- Show or hide toggle
- World constraint to place the item
- Scale control to change item size

(I added them for making test environment and/or just for fun. )


### Basic installation of "Avatar Body Bone" usage

1. Put prefab to your avatar
  1. Open `Assets/PhysBonesTK/ForAvatarBodyBone` folder in the Project window to pick assets.
  2. Put `PhysBonesTK` prefab asset into your avatar root GameObject.
2. Setup root bone of VRCPhysBone
  1. In the Hierarchy window, choose `PhysBonesTK/PhysBone` GameObject of the prefab you put in step 1.
  2. With the Inspector, fill `RootTransform` field of `VRCPhysBone` component with your avatar bone that you want to apply PhysBone.
3. Setup avatar descriptor
  1. Open your `VRCAvatarDescriptor` in the Inspector.
  2. Open `Assets/PhysBonesTK/ForAvatarBodyBone` folder in the Project window to pick following assets.
  3. Put `PBTK_Controller` asset to `VRCAvatarDescriptor/PlayableLayers/Base/FX`
  4. Put `PBTK_Menu` asset to `VRCAvatarDescriptor/Expressions/Menu`
  5. Put `PBTK_Parameters` asset to `VRCAvatarDescriptor/Expressions/Parameters`

The finished structure will be like this:

    avatar (VRCAvatarDescriptor holds FX Controller, Menu, Parameters assets of PhysBonesTK for this usage)
     - avatar bones (or armature)
     - avatar mesh
     - PhysBonesTK
       - PhysBone (PhysBone RootTransform points somewhere of your avatar bone)


### Basic installation of "Avatar Accessory Item" usage

For this usage, you need to have separated bone structure from avatar bone structure.

This usage provides features like "world constraint", "move position", "changing scale".
Actually they are not PhysBone parameters. So If you don't need features noted above, 
you can do with "Avatar Body Bone" usage even if you want use with separated bone structure item.

1. Put prefab to your avatar
  1. Open `Assets/PhysBonesTK/ForAvatarAccessoryItem` folder in the Project window to pick assets.
  2. Put `PhysBonesTK` prefab asset into your avatar root GameObject.
2. Put your accessory item into the prefab
  1. In the Hierarchy window, choose `PhysBonesTK/WorldOrigin/ItemContainer/Item` GameObject of the prefab you put in step 1.
  3. Put your accessory item GameObject as a child of `Item`.
3. Setup root bone of VRCPhysBone
  1. Open `PhysBonesTK/PhysBone` GameObject of the prefab with Inspector.
  2. Fill `RootTransform` field of `VRCPhysBone` component with your item bone that you want to apply PhysBone.
4. Setup constraint components to place and grab the item
  1. Add new GameObject to your avatar bone hierarchy where you want place the item (when world constraint is off). We call the added GameObject as `attachPoint` .
  2. Adjust the position and rotation of `attachPoint`. 
  3. Open `PhysBonesTK/WorldOrigin/ItemContainer` GameObject of the prefab with the Inspector window.
  4. Assign `attachPoint` to `ParentConstraint/Sources` (where it is `None` initially)
  5. Press "Zero" button of `ParentConstraint` component. (You'll see your item moves to `attachPoint`)
  6. (Adjust the position and rotation of `attachPoint` again if you feel it doesn't fit.)
5. Setup avatar descriptor
  7.  Open your `VRCAvatarDescriptor` in the Inspector.
  8.  Open `Assets/PhysBonesTK/ForAvatarAccessoryItem` folder in the Project window to pick following assets.
  9.  Put `PBTK_Controller` asset to `VRCAvatarDescriptor/PlayableLayers/Base/FX`
  10. Put `PBTK_Menu` asset to `VRCAvatarDescriptor/Expressions/Menu`
  11. Put `PBTK_Parameters` asset to `VRCAvatarDescriptor/Expressions/Parameters`

The finished structure will be like this:

    avatar (VRCAvatarDescriptor holds FX Controller, Menu, Parameters assets of PhysBonesTK for this usage)
     - avatar bones (or armature)
       - some bone where item attached
         - attachPoint (attach target transform. parent constraint source)
     - avatar mesh
     - PhysBonesTK
       - PhysBone  (PhysBone RootTransform points somewhere of your item bone)
       - WorldOrigin
         - ItemContainer (ParentConstraint Sources points attachPoint)
           - Item (your accessory item)
             - item bones
             - item mesh (optional.)

Note:
- GameObject name is essential because animations use them.
- You must use identical names and structure (or edit correspondent animation clips).
- In the above structure figure, GameObject names beginning with large letter denotes this naming rule.


## Limitation

- Some of features don't sync to late joiner. (That uses volatile value of PBTK_Command parameter.)


## Technical notes

### Reloading parameters

`PhysBone` component doesn't reflect its property in real-time. It reload property when the GameObject is activated (or the Component enabled). So `PhysBonesTK` inactivate and activate the object in its animation every time after each Puppet control is closed, Toggle changed, and Button pressed.


### menu structure / functionality / parameters

legend: "menu label" [value range](default value) (Control type, animation parameter)

- "PBTK_ExpressionMenu"
  - "Example Configs"  (SubMenu, PBTK_PBExampleConfigsMenu)
    - Reset to PhysBone Default (Button, PBTK_Command=34)
    - other preset (Button, PBTK_Command=128..)
  - "Movement Parameters" (SubMenu, PBTK_ExpressionParameters)
    - "Pull" [0,1](0.2) (Radial, PB_Pull)
    - "Spring" [0,1](0.2) (Radial, PB_Spring)
    - "Immobile" [0,1](0) (Radial, PB_Immobile)
    - "Gravity" (SubMenu, PBTK_PB_GravityMenu) // Gravity [-1,+1](0) using 3 controls
      - "Magnitude" (Radial, PB_GravityMagnitude)
      - "Narrow Range" (Toggle, PB_GravityNarrow) // false: [0,1], true: [0,0.1]
      - "Negative" (Toggle, PB_GravityNegative)
    - "MaxAngle" (SubMenu, PBTK_PB_MaxAngleMenu) // MaxAngle [0,180](180) using 2 controls
      - "Magnitude" (Radial, PB_MaxAngle_Magnitude)
      - "Narrow" (Toggle, PB_MaxAngle_Narrow) true: [0,10], false: [0, 180] 
    - "FreezeAxis" (SubMenu, PBTK_PB_FreezeAxisMenu)
      - "FreezeAxis" (Toggle, PB_FreezeAxis)
      - "FreezeAxis Angle" [0,180](0) (Radial, FreezeAxisAngle)
  - "Interaction Parameters" (SubMenu, PBTK_PBInteractionParametersMenu)
    - "CollisionRadius Max10cm" [0,](0) (PB_CollisionRadius)
    - "Allow Collision" [Bool](True) (Toggle, PB_AllowCollision)
    - "Allow Grabbing" [Bool](True) (Toggle, PB_AllowGrabbing)
    - "Allow Posing" [Bool](True) (Toggle, PB_AllowPosing)
    - "Grab Movement" [0,1](0.5) (PB_GrabMovement)
    - "MaxStretch [0.0-5.0]" [0.0,5.0](0) (PB_MaxStretch)
  - "Move/Scale" (SubMenu, PBTK_TransformChangingMenu) // available only in avatar accessory item use case
    - "Scale x1-x100" (PBTF_Scale) // change scale exponentially [0,1] => [1,100]
    - "Position X-Z" (TowAxisPuppet {PBTF_PositionX, PBTF_PositionZ})
    - "Rotation X-Y" (TowAxisPuppet {PBTF_RotationX, PBTF_RotationY})
    - "Reset Transform" (Button, PBTK_Command=2)
    - "World Constraint ON" (Button, PBTF_Command=4) // not Toggle because of lack of parameter space, so not saved
    - "World Constraint OFF" (Button, PBTF_Command=5)
    - "Show/Hide" (Toggle, PBTF_IsActive)

- PhysBone parameters that is Animation controllable but not supported in PhysBonesTK
  - RootTransform
  - Enabled
  - EndpointPosition

- PBTK_Command
  - 0: stable state
  - 1: (reserved. 1-31 doesn't cause PhysBone parameter reloading)
  - 2: reset transform
  - 4: WorldConstraint Place
  - 5: WorldConstraint Pull
  - 33: while changing setting UI (puppet, button)
  - 34: load PhysBone Component default
  - 128: Fishing Rod
  - 129: ribbon
  - 130: Seaweed
  - 131: Octopus leg
  - 132: Autonomous

## Animation parameters

For control:
- PBTK_Command
- PBTK_ApplyTrigger

PhysBone parameters:
- PB_Pull (0.2)
- PB_Spring (0.2)
- PB_Immobile (0)
- PB_GravityMagnitude (0)
- PB_GravityNegative (false)
- PB_GravityNarrow (false)
- PB_CollisionRadius (0)
- PB_AllowCollision (true)
- PB_MaxAngle_Magnitude (1, meaning 180.0)
- PB_MaxAngle_Narrow (false)
- PB_FreezeAxis (false)
- PB_FreezeAxisAngle (0)
- PB_AllowGrabbing (true)
- PB_AllowPosing (true)
- PB_GrabMovement (0.5)
- PB_MaxStretch (0)

for Transform operation:
- PBTF_Scale
- PBTF_PositionX
- PBTF_PositionY (not defined as expressions parameter because of lack of space)
- PBTF_PositionZ
- PBTF_RotationX
- PBTF_RotationY
- PBTF_WorldConstraint_Float
- PBTF_IsActive

Constant (used as MotionTime for some state):
- ConstOne
- ConstZero


## TODO

- Simplified menu for Quest (That doesn't support constraint components. so we should hide world constraint feature)
- Reconsider tail bone on sample model ("PBTK_NoBranch10")
- Add some more example setup
- improve sample avatar modeling

## License

Under MIT license
by naqtn (https://twitter.com/naqtn, naqtn#6808)
