# Fortress

A two-player artillery game where two people take turns firing shells from one keyboard. The arrow keys move the cannon, the space bar charges and fires, and the terrain is carved out in a circle where a shell lands. The side whose health runs out, or who falls through a crater off the bottom of the map, loses. A school assignment made with Adobe Flash CS5.5 and ActionScript 3.0.

<p>
  <img src="docs/screenshots/screenshot-1.jpg" width="480" alt="Gameplay">
</p>


## How to play

Download from Releases and open the swf with Flash Player. The background music and sound effects are read from the `Sounds` folder next to the swf. Game Start on the main menu starts a match, and How To and Credit open the instructions and the credits.

| Key | Action |
|---|---|
| Left, Right arrow | Move |
| Up arrow | Jump |
| Hold Space | Charge power. Fires on its own when full |
| Up, Down arrow while holding Space | Adjust the firing angle (15 to 45 degrees) |
| Release Space | Fire |

Only the cannon whose turn it is can move, and the turn passes to the other player after a shot. Neither cannon can be controlled until every shell and flame is gone. Each turn allows 200 pixels of movement, shown by the blue bar above the cannon. Firing pushes the cannon back with recoil.

A shell that hits the ground erases the terrain in a 50 pixel radius and leaves a flame. A cannon touching a flame loses 0.25 of its 100 health every frame. Every shot has a 1 in 5 chance of dropping an item box from the sky, and the cannon that picks it up gets one of two special shots for its next turn. The current shot type is shown in the upper left of the screen.

| Item | Effect |
|---|---|
| Big Bombing | Erases the terrain in a 100 pixel radius and leaves three large flames |
| Carpet Bombing | Fires three shells to slightly different distances |

When the match is decided a result box appears, and clicking it returns to the main menu.


## How it works

**The GameMaker event model is rebuilt on top of ActionScript.** The stage's `ENTER_FRAME` and keyboard events drive KeyPress, KeyDown and KeyRelease events from the key states and a Step event every frame, dispatched to each object, and Create, Collision, Destroy and mouse events are likewise functions that branch on the object type. Each library symbol has a class name such as `ObjCannon`, and GameMaker functions such as `InstanceCreate`, `InstanceDestroy`, `InstanceNumber`, `PlaceFree`, `MoveContact`, `LengthdirX` and `PointDirection` are ported under the same names. GameMaker's `with` statement is imitated by `With`, which takes an anonymous function. The view works by subtracting the view position from every instance's coordinates at the end of each frame and restoring them at the start of the next, so an 800x600 screen follows a 3000 pixel wide map.

```actionscript
var controlTurn;
With(id, ObjGame, function (id, other){
	 controlTurn = id.currentControl;
});
```

**The terrain is tested and erased as a bitmap.** The terrain symbol is drawn once into a `BitmapData`, and collisions use `BitmapData.hitTest` to check whether opaque pixels overlap. When a shell hits, the pixels inside a circle around the impact point are set to transparent with `setPixel32`, leaving a crater.

**The shell's initial speed comes from solving the range formula backwards.** The charged power and the firing angle set a target point, and the horizontal distance and gravity go into the range formula R = v² sin2θ / g to solve for v. Gravity is set between 0.1 and 0.3 by the charged power, and adding it to the velocity every frame in flight makes the parabola.

```actionscript
vector1 = (distance * gravity);
vector2 = LengthdirX(1, angle);
vector3 = LengthdirY(1, angle);
speed = Math.sqrt(vector1 / (2 * Math.abs(vector2 * vector3)));
```

**Slopes are climbed up to a grade limit.** A cannon can step up by the tangent of its `gradeAbility` of 60 degrees times its horizontal speed (at least 1). When something blocks the way, it checks whether the spot raised by that height is free, steps up and snaps back to the ground with `MoveContact`. Going downhill, it snaps down within the same height so it follows the slope.


## Files

| Path | Content |
|---|---|
| `source/Fortress.fla` | Flash CS5.5 project file |
| `source/Scripts.txt` | The full ActionScript from the first frame of the fla, copied out as text |
| `source/Sounds/` | Background music and sound effects, read by the swf at run time |
| `docs/screenshots/` | Screenshots |
| Releases | The swf and the sound files |


## Credits

The slope movement, parabolic motion and terrain destruction code was based on these posts.

- Slope movement: [cafe.naver.com/gmopensource/578](http://cafe.naver.com/gmopensource/578)
- Parabolic motion: [cafe.naver.com/playgm/71681](http://cafe.naver.com/playgm/71681)
- Terrain destruction: [codeonwort.tistory.com/31](http://codeonwort.tistory.com/31)


## License

CC BY-NC-ND 4.0. Unmodified copies may be shared for noncommercial purposes with attribution. Modified versions and commercial use are not allowed. Bundled libraries, graphics, sounds, and maps made by other people keep their own rights. See [LICENSE](LICENSE).
