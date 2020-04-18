
 MV3D
======

A 3D rendering plugin for RPG Maker MV.


Requires version 1.6 of RPG Maker MV.  

If you are making a game with this plugin, please consider supporting my
patreon.  
https://www.patreon.com/cutievirus  
You can also unlock some patron-only features by becoming a patron, such as
Dynamic Shadows.  

Discord: http://cutievirus.com/discord

## Getting started

To use the plugin on a new or existing project, download [plugin.zip] and
extract the files into your project directory.
Then, load `babylon.js` and `mv3d.js` as plugins in that order.

[plugin.zip]:
https://github.com/Dread-chan/MV3D/blob/master/plugin.zip


Now when you run your game, the map should be rendered in 3D.

The A3 and A4 tiles will be rendered as walls. You can also change the height
of tiles using regions and terrain tags.

By default, regions 1-7 are configured to affect the height.

Terrain tag 1 is configured to use a cross shape, so tiles with this tag will
stand up like a tree.

Terrain tag 2 is configured to use a fence shape. Try putting this tag on the
fence autotiles that come with MV.

The regions and terrain tags can be reconfigured however you want.

---

## Configuration

Tileset and map configurations are placed in the note area.
Event configurations are placed either in the event note or in comments.
Region and terrain tags are configured through the plugin parameters.

In order for the plugin to recognize configurations, they need to be wrapped
properly.  
Tileset and map configurations need to be wrapped in a <mv3d> </mv3d> block,
while event configurations need to be inside a <mv3d:  > tag.  
Region and terrain tag configurations (defined in plugin parameters)
don't need to wrapped at all.

### Using configuration functions

Configuration functions are used to configure tilesets, regions, terrain tags,
events, and maps.

The following is an example of a basic configuration function. Each function
is passed a list of parameters.

    top(A1,0,0)

Some functions can take a large number of parameters. In these cases the
parameter list is separated into groups. A vertical bar can be used instead of
a comma to jump to the next group. You can also jump to a parameter by name
using a colon.  
Here are some examples:

    ceiling(A4,0,0|2)
    camera(0,60,dist:5,height:0.75)

Parameter groups can also have names, and can be jumped to in the same way you
jump to a named parameter.

---

## Tileset Configuration

Each line in the tileset configuration should start by identifying the tile
you want to configure, followed by a colon, and then a list of configuration
functions.

Choosing a tile is done with the format `img,x,y`, where img is name of the
tileset image (A1, A2, B, C, etc.), and x and y are the position of that tile
on the tileset. For example, `A2,0,0` would be the top left A2 tile. On the
outdoor tileset, this would be the grass autotile.

Here are some exapmle tileset configurations.  

This first one will make our outdoor water sink into the ground a little.

    <mv3d>
    A1,0,0:top(A1,0,0),side(A1,31,54,31,14),depth(0.3),float(0.1)
    </mv3d>

But this example won't look very good if we place the water at the edge of a
cliff, so let's configure it to use a waterfall texture on the outside walls.

    <mv3d>
    A1,0,0:top(A1,0,0),side(A1,1,1),inside(A1,31,54,31,14),depth(0.3),float(0.1)
    </mv3d>


### Setting tile textures

A tile can have up to four different textures. These are the Top texture,
the Side texture, the Inside texture, and the Bottom texture.  
If unset, the inside texture will be the same as the side texture and the 
bottom texture will be the same as the top texture.  
Inside textures are only used if a tile has a depth() specified.

Each texture can be set with the respective top(), side(), inside(), and
bottom() function. Texture() can be used to set both the top and side texture.

Parameter list:
img,x,y,w,h|alpha|glow[anim]animx,animy

The number of parameters passed to the first group will alter the behavior of
these functions.  

With only 2 parameters, it will act as an offset. For example, top(0,1) will
use the texture of the tile below the configured tile.  

With 3 parameters, you specify the tileset image name and tile coordinates of
the tile to use the texture from. For example, top(A1,0,0) will use the top
left tile of the A1 tileset image.  

With 5 parameters, you specify a region with pixel coordinates on the
specified tileset image. For example, top(A1,24,72,48,48).  

The alpha parameter can be used to make the texture partially transparent. Or,
you can set alpha to 1 to turn on alpha blending.  
Examples: texture(|1), texture(alpha:0.5)

The glow parameter will make the texture glow in the dark. Good for lava.  
Can take a number (0-1) or a color.  
Example: texture(||1), texture(glow:1), texture(glow:red)

[Anim] is a named parameter group for defining custom animated tiles.  
The number supplied to animx and animy will be the offset used by the
animation. The final offset will be equal to the anim offset times the
current frame.  
Animx has frames 0,1,2,1, while animy has frames 0,1,2.  
Examples: texture(|||1,0), texture(anim:0,1)



### Setting tile height

Height and depth can be used to set the height of the tile. A tile's height
is equal to its height minus its depth. Tiles with depth will use their inside
texture on their sides. Depth is good for making pits.  
Examples: height(2), depth(1)  

The float function will make water vehicles float above the surface. If you've
given your water tiles some depth(), this will unsink your ships.  
Example: float(0.3)

The fringe function will change the height of your star tiles.  
Example: fringe(2)



### Changing tile shapes

There are a few special shapes which can be given to your tiles using the
shape() function. Valid shapes are FLAT, FENCE, CROSS, and XCROSS.  
Some shapes can have additional data passed to them. For instance,  
shape(fence,false) will disable fenceposts.
Examples: shape(fence), shape(xcross)



### Slope Tiles

Slope tiles can be used to make hills or stairs to move between elevations.
The slope tiles will automatically try to choose their direction.  
Directional passage will prevent the slope tile from facing certain
directions.  
Second parameter forces slope to face certain direction.
Directions are n, s, e, and w.
Shadow pen can be used to force slope to face a certain direction.
Draw a shadow on the side you want the slope to face.
Example: slope(1), slope(0.45,n), slope(2,e)

### Tile Passage

The pass function will change the passage rules for the tile.  
This can be used to get star passage on A tiles, or to have different passage
rules depending on if 3D is enabled.
Examples: pass(o), pass(x), pass(*)

---


## Event Configuration

Event configurations can be placed in either the event note or comments, and
mush be wrapped in a <mv3d:   > tag.
A few configuration functions, such as pos, will behave slightly differently
depending on whether they're placed in the note or comments.

Here's some examples.

First let's make an event that displays on the edge of a wall.

    <mv3d:shape(fence),scale(0.9,1.3),y(0.51),rot(0),z(0)>

Next let's attach a flashlight to an event.

    <mv3d:flashlight(white,2,6,30)>



### Adjust event position

The offset() function can be used to offset an event's mesh. This is 
purely visual. xoff(), yoff(), and zoff() can be used to change the 
x, y, and z offset independently.  
Examples: offset(0.5,0.5,0.5), offset(z:1),
xoff(0.49), yoff(0.51), zoff(1)

The elevation() function will adjust the event's height above the ground.
With elevation greater than zero, the event will be flying.  
Example: elevation(2)

The z() function will set the event's absolute z position, ignoring ground
level.  
Example: z(0)

The pos() function will change the position of the event. This can be used to
make two events occupy the same space. Prefix numbers with + to use relative
coordinates.  
Examples: pos(1,2), pos(+0,+-1)



### Change event shape

Use the shape function to set what shape the event should use. Valid event
shapes are FLAT, SPRITE, TREE, WALL, FENCE, CROSS, and XCROSS.  
FLAT will make the event lie flat on the ground like a tile.  
SPRITE will make the event rotate to always face the camera.  
TREE will make the event stand up, but rotate horizontally toward camera.  
WALL and FENCE will make the tile stand up without rotating. FENCE is an
alias of WALL.  
CROSS and XCROSS will make the event use a cross mesh. XCROSS is rotated
45 degrees.  
Example: shape(tree)

The scale function can change the size of the event.  
Examples: scale(2,2), scale(1.5,3)

The yaw function will rotate the event's mesh horizontally. Rotation applies
before pitch. Doesn't work with Sprite or Tree shapes.  
0 is south, 90 is east, 180 is north, and 270 is west.  
Example: yaw(45)

The pitch function will tilt the event's mesh vertically. Doesn't worth with
Sprite shapes.  
Example: pitch(20)

The rot function will rotate the event's mesh horizontally. Rotation applies
after pitch. Doesn't work with Sprite or Tree shapes.  
Example: rot(45)



### Event Lights

The lamp function attaches a point light to the event.
Parameter list: color,intensity,range
Examples: lamp(white,0.5,1), lamp(#ff8888,1,3)


The flashlight function attaches a spotlight to the event.
Parameter list: color,intensity,range,angle|yaw,pitch
Examples: flashlight(#ffffff,2,6,30), flashlight(red,2,6,45|90)


The height and offset of the lights can be set with lampHeight(),
flashlightHeight(), lampOffset(), and flashlightOffset().  
The height and offset of both the lamp and flashlight can be moved
together with lightHeight() and lightOffset().  
Examples: lampHeight(0.5), flashlightHeight(0.25), lightOffset(0,1.01)



### Other event settings

You can set whether the event is affected by bush tiles using the bush
function.  
Examples: bush(true), bush(false)

You can disable or change the size of the event's shadow using the shadow
function.  
A second parameter can be supplied to control how far the shadow may travel.
before fading out.  
Examples: shadow(0), shadow(3), shadow(1,6)

The alpha function is used to make the event partially transparent or to turn
on alpha blending.  
Examples: alpha(0.5), alpha(1)

The glow parameter will make the event glow in the dark.  
Can take a number (0-1) or a color.  
Examples: glow(1), glow(red)

The dirfix function will set whether the event rotates depending on the camera
angle.  
Examples: dirfix(true), dirfix(false)

The platform function will set whether the event can be walked on.  
Example: platform(true)

The gravity function will set the fall speed of the event.
Example: gravity(10)

The collide will set the event's collision height.  
collide(false) acts as a shorthand for collide(0),platform(false)  
Examples: collide(0), collide(1), collide(1.5)

The trigger event will set the height range the event can be triggered from.
The first parameter is the height above the event, and the second is the
height below the event.
Examples: trigger(Infinity,Infinity), trigger(0), trigger(2,1)

The pass function when used on events acts as a shorthand for platform and
collide.  
pass(o) turns on platform
pass(x) turns off platform and turns on collision.
pass(*) turns off platform and collision.

---

## Map Configuration

Map configuration goes in the note area of the map settings. Configurations
should be placed in an <mv3d></mv3d> block, which should contain a list of
configuration functions.

Some of these configurations apply when the map is loaded, while others affect
how the map is rendered.

---

### Light and Fog

The light function will set the ambient light level for the map.  
The ambient function is an alias for the light function.  
Examples: light(white), ambient(gray), ambient(#222222), light(rebeccaPurple)

The fog function sets the color and distance of the fog.  
Parameter list: color|near,far  
Examples: fog(white|10,20), fog(#e1feff), fog(black|20,30)



### Camera Settings

The camera function can be used to set various properties of the camera,
including the rotation, distance, height, and projection mode.  
Parameter list: yaw,pitch|dist|height|mode  
Examples: camera(0,60,dist:5,height:0.75), camera(45,45),
    camera(mode:orthographic), camera(mode:perspective)



### Setting a ceiling for the map

The ceiling function sets a ceiling texture and height, and also supports 
all the other properties from the texture function like alpha and animation.  

The backface parameter will force backfaces to be rendered on the ceiling. 
This will also cause the ceiling to collide with the camera (if camera 
collision is on), and to cast shadows (if dynamic shadows is on).

Parameter List:  
img,x,y,w,h|height,backface|alpha|glow[anim]animx,animy  

Example: ceiling(A4,0,0|2),ceiling(A4,0,0|2,true)



### Other map settings

The edge function sets whether to render walls at the map edge.  
Edge can be set to clamp to repeat tiles at the edge of the map.  
Examples: edge(true), edge(false), edge(clamp), edge(clamp,2)

The disable function will turn off the 3D rendering for the map.  
Example: disable()



### Regions and tiles

You can define region and tileset configurations in the map note by using
<mv3d-regions> and <mv3d-tiles> blocks.  
Example:  

    <mv3d-regions>
        12:slope(1)
    </mv3d-regions>
    <mv3d-tiles>
        A2,4,0:top(1,1)
    </mv3d-tiles>

---


## Plugin Commands

In this documentation, the parts surrounded with angle bracks like <n> are
parameters.  
<n> means number.  
<t> means time.  

Some commands (like lamp and flashlight) act on a character. By default the
target character will be the current event.
You can define your own target using the following syntax:

    mv3d ＠target rest of the command

If the second word in the command starts with `＠`, that will be interpreted
as the target.

Valid targets:

- ＠p or ＠player: Targets $gamePlayer.
- ＠e0, ＠e1, ＠e2, ＠e25 etc: Targets event with specified id.
- ＠f0, ＠f1, ＠f2, etc: Targets first, second, third follower, etc.
- ＠v0, ＠v1, ＠v2: Boat, Ship, Airship.

Some parameters can be prefixed with + to be set relative to the current
value.

For example:

    mv3d camera yaw +-45 0.5

---

    mv3d camera pitch <n> <t>
    mv3d camera yaw <n> <t>
    mv3d camera roll <n> <t>
    mv3d camera dist <n> <t>
    mv3d camera height <n> <t>

Sets the camera properties, where <n> is the new value and <t> is the time to
interpolate to the new value.   
Prefix <n> with + to modify the current value instead of setting a new
value.

    mv3d camera zoom <n> <t>

Changes the fov. n greater than 1 zooms in, n lower than 1 zooms out.

---

    mv3d camera target ＠t <t>

Change the camera's target.
Camera will transition to the new target over time <t>.

---

    mv3d camera pan <x> <y> <t>
    mv3d pan <x> <y> <t>

Pans the camera view, relative to current target.

---

    mv3d camera mode <mode>

Set camera mode to PERSPECTIVE or ORTHOGRAPHIC

---

    mv3d allowRotation <true/false>
    mv3d allowPitch <true/false>

Sets whether keyboard control for rotation / pitch is enabled.  
allowRotation is ignored in 1st person mode.

    mv3d lockCamera <true/false>

Locks the camera, preventing keyboard control of rotation & pitch.  

---

    mv3d fog color <color> <t>
    mv3d fog near <n> <t>
    mv3d fog far <n> <t>
    mv3d fog dist <near> <far> <t>
    mv3d fog <color> <near> <far> <t>

<t> is time.  
Prefix values with + to modify the current values instead of setting new
values.

---

    mv3d light <color> <t>
    mv3d ambient <color> <t>

Sets the color for the ambient light.

---

    mv3d ＠t lamp color <color> <t>
    mv3d ＠t lamp intensity <n> <t>
    mv3d ＠t lamp dist <n> <t>
    mv3d ＠t lamp <color> <intensity> <dist> <t>

---

    mv3d ＠t flashlight color <color> <t>
    mv3d ＠t flashlight intensity <n> <t>
    mv3d ＠t flashlight dist <n> <t>
    mv3d ＠t flashlight angle <deg> <t>
    mv3d ＠t flashlight pitch <deg> <t>
    mv3d ＠t flashlight yaw <deg> <t>
    mv3d ＠t flashlight <color> <intensity> <dist> <angle> <t>

Angle is beam width of the flashlight.

---

    mv3d disable
    mv3d enable

Turns 3D rendering on or off.

---

    mv3d ＠t elevation <n> <t>

Sets the elevation of the target character.

---

    mv3d ＠t animation <id>
    mv3d ＠t animation <id> depth <true/false> scale <n>

Play an animation on the event, with additional features.  
Animations played with this command will have depth by default 
(they can be occluded by other 3D objects).  
They can also have a custom scale.  
Examples:  
mv3d ＠p animation 8 scale 0.25

---

    mv3d resolution scale <n> <t>

Sets the resolution scale. n should be a value between 0 and 1.

---

    mv3d ＠t configure <functions>

Configure the target with a list of configuration functions.
Example: mv3d ＠p configure scale(2)

---

    mv3d set <setting> <value(s)>

Change a setting.  
Settings that also appear in the plugin parameters should use
the same syntax.  

valid settings:
    cameraCollision
    diagonalMovement
    turnIncrement

Examples:

    mv3d set cameraCollision Type2 Smooth

---

### Vehicle Commands

    mv3d <vehicle> speed <n>
    mv3d airship height <n>

The vehicles are boat, ship, and airship.  
Speed of the vehicle should be 1-6. It works the same as event speed.   
A higher airship can fly over higher mountains. Perhaps you could let the
player upgrade their airship's height and speed.


---

## Script Calls

mv3d.command can be used to run a plugin command using script. The syntax is 
the same as the plugin command, but without `mv3d` at the beginning.  
The parameters can be passed as arguments.  
Example:  
mv3d.command('camera dist',10,1);  

---

## Patron Knights:

- Whitely
- Izybelle
- Pumpkin Boss
- L
- hsumi
- nemoma
- AmalgamAsh
- Gaikiken
- Mukadelheid
- Clumsydemonwithfire


## Patron Heroes:

- A Memory of Eternity
- Fyoha
- 冬空 橙
- nyrion
- Vaan Auroris
- Chainer Valentine
- Kaboth
- Karenksoon
